---
id: 92
title: 'Is Digital Oceans One-click app for Ruby on Rails any good?'
date: 2018-09-12T17:22:21+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=92
permalink: /deploying/is-digital-oceans-one-click-app-for-ruby-on-rails-any-good/
categories:
  - Deploying
  - DigitalOcean
  - Servers
---
[Digital Ocean](https://m.do.co/c/c86b33c659ed) offers 2 types of droplets (servers):

  1. Droplets with a clean Linux install.
  2. Droplets with some application preinstalled: &#8220;One-click apps&#8221;

Let&#8217;s have a look at the One-click Rails installation they offer. I&#8217;ll describe what you get and what I like about, what I don&#8217;t like about it and I&#8217;ll give some tips on how to use it.

<img class="alignright wp-image-96" src="http://www.runrails.com/wp-content/uploads/2018/08/mrpeanutbutter.jpg" alt="" width="144" height="165" /> 

&#8220;_Welcome to One-click apps, what do they do? Do they do things? Let&#8217;s find out!&#8221;_

<!--more-->

## update by Digital Ocean:

On the same day that I published this post, Digital Ocean updated their image. Which is good news for the users but makes this post less useful.

Notable differences besides the newer versions are that they replaced Unicorn with Puma and got rid of the .unicorn.sh script. I&#8217;ll write a new review of their installation tomorrow.

<blockquote class="twitter-tweet" data-conversation="none" data-lang="en">
  <p dir="ltr" lang="en">
    Hi! We launched a brand-new Rails one-click based on 18.04 delivering Ruby 2.5.1 and Rails 5.2.1 this morning as we completed the rebase of all our one-clicks to the latest LTS (we always wait for .1 to rebase to a new LTS)
  </p>
  
  <p>
    — Ryan Quinn (@RyanPQ) <a href="https://twitter.com/RyanPQ/status/1040289162547613696?ref_src=twsrc%5Etfw">September 13, 2018</a>
  </p>
</blockquote>



## What do you get?

The current version of the Digital Ocean Rails install will give you an Ubuntu 16.04 server with:

  * Ruby version 2.4.0 through RVM
  * Postgres database
  * Nginx webserver
  * Unicorn app server
  * Letsencrypt for SSL certs
  * Empty Rails 5.0.2 app

### Bad: Suggested size too big

They suggest a pretty big Droplet (4GB of ram) but it works fine with the smallest size (1GB of ram). It&#8217;s cheaper to start small and upgrade later.

<img class="aligncenter size-large wp-image-94" src="http://www.runrails.com/wp-content/uploads/2018/08/create-rails-droplet-1024x517.png" alt="" width="840" height="424" srcset="https://www.runrails.com/wp-content/uploads/2018/08/create-rails-droplet-1024x517.png 1024w, https://www.runrails.com/wp-content/uploads/2018/08/create-rails-droplet-300x151.png 300w, https://www.runrails.com/wp-content/uploads/2018/08/create-rails-droplet-768x388.png 768w, https://www.runrails.com/wp-content/uploads/2018/08/create-rails-droplet-1200x606.png 1200w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> 

### Bad: the server is not the latest LTS

The server runs Ubuntu 16.04 LTS which is good but not the best. I don&#8217;t like doing dist-upgrades on production systems. My strategy is to start with the latest Ubuntu server LTS version and keep that version until Ubuntu&#8217;s support runs out after 4 years or when I need a version of some dependency that&#8217;s not available in that LTS version. Both situations rarely happen. The benefit of keeping the same LTS version is that it becomes more reliable over time and the amount of updates becomes less. You get the same security updates as newer versions so this approach is perfectly safe.

### Bad: latest updates not installed

The server doesn&#8217;t have the latest updates installed and that&#8217;s the first thing you should do before you get started with a server. These updates may contain changes that will break things, so it&#8217;s best to get that out of the way with this oneliner:

<pre class="lang:default decode:true">apt update && apt dist-upgrade && apt-get autoremove && apt-get clean && reboot</pre>

The &#8220;&&&#8221; will only run the next command if the previous has finished without errors. One &#8220;&&#8221; will run a command in the background and not wait for the others to finish, so if you use one &#8220;&&#8221; it will become a big mess because it will start upgrading before the update is finished!

I think they should run updates more often on their images or run the updates automatically after the first boot. This is a waste of everyone&#8217;s time.

### Good: preconfigured firewall

The server comes with a firewall preinstalled and preconfigured. That&#8217;s great and excactly what I would to. You can change the settings with the ufw command or directly with iptables (not recommended).

### Good: security-updates enabled

Automatic security updates are enabled and automatic reboots are not. Enabling automatic security updates is good, because they do not contain changes in functionality they should not break things. You can enable automatic reboots if you want to in _/etc/apt/apt.conf.d/50unattended-upgrades_

<pre class="lang:default decode:true" title="/etc/apt/apt.conf.d/50unattended-upgrades">Unattended-Upgrade::Automatic-Reboot "true";

Unattended-Upgrade::Automatic-Reboot-Time "02:00";</pre>

### Bad: RVM and Ruby

Ruby is installed through RVM and the RVM-installation is a bit broken. You can upgrade to the latest stable version and fix the configuration with these steps:

As root:

<pre class="lang:default decode:true">apt-get install gnupg2

gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3

rvm get stable --auto-dotfiles</pre>

As rails-user:

<pre class="lang:default decode:true ">echo rvm_silence_path_mismatch_check_flag=1 &gt;&gt; ~/.rvmrc</pre>

That will silence the error.

## Bad: deploy with Filezilla or WinSCP

Digital Ocean suggests that you use Filezilla, WinSCP or Rsync to upload your application. Although uploading files this way will work it&#8217;s very prone to errors and takes too much time. And I understand that they have to offer you the option to use a password because not everyone understands how to use ssh-keys. But you shouldn&#8217;t! Use ssh-keys, I&#8217;ll write about that in another post.<img class="alignright size-large wp-image-100" src="http://www.runrails.com/wp-content/uploads/2018/08/rsync-1024x134.png" alt="" width="840" height="110" srcset="https://www.runrails.com/wp-content/uploads/2018/08/rsync-1024x134.png 1024w, https://www.runrails.com/wp-content/uploads/2018/08/rsync-300x39.png 300w, https://www.runrails.com/wp-content/uploads/2018/08/rsync-768x101.png 768w, https://www.runrails.com/wp-content/uploads/2018/08/rsync.png 1174w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" />

&nbsp;

## Suggestion: Use a git-hook to deploy your app

There are a lot of ways to deploy an app to a server. A simple way to deploy is to use a git and a git-hook instead of scp: <a href="http://www.runrails.com/deploying/use-a-git-hook-to-deploy-your-app/" target="_blank" rel="noopener">git-hook to deploy</a>

Move the empty Rails app that&#8217;s on the server out of the way before using that method with:

<pre class="lang:default decode:true">mv rails_project/ rails_project.bak</pre>

## Could be better: Unicorn

The server uses Unicorn to run the Rails application. Although not my preference this is a good choice. In the original rails projects folder that comes with the installation is a script (.unicorn.sh) that does the magic. So don&#8217;t delete that when you remove the empty rails project. And don&#8217;t forget to add the unicorn gem to your project or install it system wide.

## Bad: missing log rotation

It looks like the Rails log (log/production.log) and Unicorn log (/var/log/unicorn/unicorn.log) are not rotated. So you&#8217;ll have to add that manually to prevent your disk from filling up.

## Bad: letsencrypt not working

Yes, there is a letsencrypt package installed but you are better of installing the current version from github or ppa:certbot/certbot with <a href="https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04" target="_blank" rel="noopener">this</a> howto. The MOTD (message you see when you login) even suggests that howto. So we agree. The recent version of cerbot will setup your nginx configuration for you.

## Conclusion: should you use DO One Click Rails installation?

No, you can use it after some tweaks but in my opinion it&#8217;s not worth the effort. You won&#8217;t end up with a nice and stable installation. I&#8217;m working on a tutorial for a perfect Rails installation, if you subscribe to my mailinglist below I&#8217;ll let you know when it&#8217;s finished.

&nbsp;
