---
id: 303
title: Sidekiq with environment variables and systemd
date: 2019-02-12T08:39:20+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=303
permalink: /deploying/sidekiq-with-environment-variables-and-systemd/
categories:
  - Deploying
  - Servers
---
## Normal setup

Running Sidekiq with systemd is easy, download the sidekiq.service file from <a href="https://github.com/mperham/sidekiq/blob/master/examples/systemd/sidekiq.service" target="_blank" rel="noopener">Github</a> and place it in /etc/systemd/system.

Start it with: sudo **systemctl** start sidekiq

Enable it to run at boot: sudo **systemctl** enable sidekiq

And stop it: sudo **systemctl** stop sidekiq

The only lines you have to change are:

<pre class="lang:default decode:true ">WorkingDirectory=/opt/myapp/current 

User=sysadmin

Group=sysadmin</pre>

&nbsp;

## With environment variables

But what if you have some environment variables that you want to load before starting Sidekiq? Keep in mind that there are many ways to do this, and this only how I do it.

I put the environment variables in a hidden file called &#8216;.env&#8217; in the home directory of the user that runs the application. If this user is called deploy, the file would be /home/deploy/.env

In it are my environment variables with export in front of them:

<pre class="lang:default decode:true ">export DATABASE_URL=postgres://deploy:sdfsdfDNFSDF@localhost/my_prod_db
export SECRET_KEY_BASE=640c916asdfasdfasdfasdfasdfasdfasdfasdf
export SMTP_ADDRESS="smtp.mandrillapp.com"
</pre>

These are loaded when you log in through ssh with source as the first line in .bashrc. It doesn&#8217;t have to be the first line but it has to be above the &#8220;if not running interactively don&#8217;t do anything&#8221; line.

<pre class="lang:default decode:true">source ~/.env</pre>

But how do these env variables reach Sidekiq? We&#8217;ll simply start Sidekiq with bash in the Systemd service file. Change the ExecStart line in /etc/systemd/system/sidekiq.service and add /bin/bash -lc in front.

<pre class="lang:default decode:true">ExecStart=/bin/bash -lc '/usr/local/bin/bundle exec sidekiq -e production -C /home/sysadmin/checkout/config/sidekiq.yml'
</pre>

Bash will load the environment variables and then start Sidekiq.