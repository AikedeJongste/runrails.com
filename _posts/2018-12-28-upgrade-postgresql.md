---
id: 274
title: Upgrade PostgreSQL with pg_upgradecluster
date: 2018-12-28T20:49:51+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=274
permalink: /servers/upgrade-postgresql/
categories:
  - Servers
---
Another post about upgrading PostgreSQL? Really? Yes, I had to upgrade from 9.3 to 9.5 on Ubuntu 16.04 after upgrading from 14.04 today.  And I couldn&#8217;t find a post that completely described my situation. So I&#8217;m writing my own.

&nbsp;

<!--more-->

&nbsp;

The first thing you need to know is how to see which version of Postgres is currently running:

<pre class="lang:default decode:true ">root@mijn-prod:~# ps aux | grep postgres | grep main
postgres   831  0.0  0.5 294604 22796 ?        S    21:14   0:00 /usr/lib/postgresql/9.5/bin/postgres -D /var/lib/postgresql/9.5/main -c config_file=/etc/postgresql/9.5/main/postgresql.conf</pre>

Yes, that&#8217;s 9.5 running. On Ubuntu 2 versions are installed at the same time:

<pre class="lang:default decode:true">root@mijn-prod:~# dpkg -l | grep postgresql
ii  postgresql                           9.5+173ubuntu0.2                           
ii  postgresql-9.3                       9.3.24-0ubuntu0.14.04                      
ii  postgresql-9.5                       9.5.14-0ubuntu0.16.04</pre>

To start or stop a specific version you can use:

<pre class="lang:default decode:true ">systemctl stop postgresql@9.5-main.service

or 

systemctl start postgresql@9.5-main.service</pre>

After upgrading from Ubuntu 14.04 to 16.04 a version 9.5 was installed and a new cluster was created by the installer. You have to drop it if you want to upgrade your 9.3 cluster to 9.5. And you have to stop version 9.5 if you want to drop the cluster.

<pre class="lang:default decode:true ">systemctl stop postgresql@9.5-main.service 
pg_dropcluster 9.5 main</pre>

&nbsp;

<pre class="lang:default decode:true">postgres@mijn-prod:~$ pg_dropcluster 9.5 main
Warning: systemd was not informed about the removed yet. Operations like "service postgresql start" might fail. To fix, run:
  sudo systemctl daemon-reload</pre>

I got this cryptic error message, but it worked anyway. Just run the command again if you are not sure.

<pre class="lang:default decode:true">root@mijn-prod:~# pg_dropcluster 9.5 main
Error: specified cluster does not exist</pre>

Well with a clean 9.5 it&#8217;s time to upgrade 9.3. The upgrade will not work properly if you have people using the database and you will get something like this:

<pre class="lang:default decode:true">root@mijn-prod:~# pg_upgradecluster 9.3 main
Stopping old cluster...
Notice: extra pg_ctl/postgres options given, bypassing systemctl for stop operation
pg_ctl: server does not shut down
HINT: The "-m fast" option immediately disconnects sessions rather than
waiting for session-initiated disconnection.
Error: Could not stop old cluster</pre>

So stop the application or nginx or wherever your users come from:

<pre class="lang:default decode:true">systemctl stop nginx</pre>

and try again:

<pre class="lang:default decode:true ">root@mijn-prod:~# pg_upgradecluster 9.3 main
Stopping old cluster...
Notice: extra pg_ctl/postgres options given, bypassing systemctl for stop operation
Disabling connections to the old cluster during upgrade...
Restarting old cluster with restricted connections...
Redirecting start request to systemctl
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = "en_US:en",
	LC_ALL = (unset),
	LC_CTYPE = "UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
Error: The locale requested by the environment is invalid.
Error: Could not create target cluster
</pre>

Ouch! More scary and cryptic error messages. The solution to the locale warnings is to set them again before running pg_upgradecluster:

<pre class="lang:default decode:true">root@mijn-prod:~# export LC_CTYPE=en_US.UTF-8 
root@mijn-prod:~# export LC_ALL=en_US.UTF-8
root@mijn-prod:~# pg_upgradecluster 9.3 main
Stopping old cluster...
Notice: extra pg_ctl/postgres options given, bypassing systemctl for stop operation
Disabling connections to the old cluster during upgrade...
Restarting old cluster with restricted connections...
Redirecting start request to systemctl
Creating new cluster 9.5/main ...

&lt;cut&gt;

Success. Please check that the upgraded cluster works. If it does,
you can remove the old cluster with

  pg_dropcluster 9.3 main</pre>

Use the version specific start/stop commands to see if both versions are working with the same set of data. If everything works well in the new version you can finally drop the 9.3 cluster.

<pre class="lang:default decode:true ">pg_dropcluster 9.3 main</pre>

I got no output but the website no longer works with 9.3 so it&#8217;s gone. Time to remove the packages as well:

<pre class="lang:default decode:true ">apt-get autoremove --purge postgresql-9.3</pre>

Finally don&#8217;t forget to reboot to see if everything starts after a restart.