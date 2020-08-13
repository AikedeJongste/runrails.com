---
id: 261
title: Ruby on Rails on Windows 10 in 2019
date: 2019-01-23T12:55:40+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=261
permalink: /windows/ruby-on-rails-on-windows-10-in-2019/
categories:
  - Windows
---
If you want to install Ruby on Rails on Windows, look no further! I recommend that you use either Linux or macOS to develop in Rails if you have the option. But sometimes you have no choice. My Macbook has to go back to Apple for repairs so I decided to try developing on Windows. Here is my setup:

## Install WSL

You can use Ruby in Windows with Ruby for Windows but if you want to use Rails I recommend that you use WSL. WSL is short for Windows Subsystem for Linux and it creates a Linux environment in Windows. WSL is fully supported by Microsoft so you can get it for free from the Microsoft Store:

<img class="aligncenter wp-image-284 size-large" src="http://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.21-1024x605.png" alt="" width="840" height="496" srcset="https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.21-1024x605.png 1024w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.21-300x177.png 300w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.21-768x453.png 768w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.21-1200x709.png 1200w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.21.png 1968w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> 

Installing:

<img class="aligncenter wp-image-286 size-large" src="http://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.59-1024x609.png" alt="" width="840" height="500" srcset="https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.59-1024x609.png 1024w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.59-300x178.png 300w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.59-768x456.png 768w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.59-1200x713.png 1200w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.57.59.png 1972w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> But don&#8217;t start it after the install finishes. Open a Windows Powershell with Administrator rights.

<img class="aligncenter wp-image-288 size-large" src="http://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.01.32-1024x840.png" alt="" width="840" height="689" srcset="https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.01.32-1024x840.png 1024w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.01.32-300x246.png 300w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.01.32-768x630.png 768w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.01.32-1200x984.png 1200w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.01.32.png 1670w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> 

And copy and paste this line:

<pre class="lang:default decode:true">Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux</pre>

<img class="aligncenter wp-image-289 size-large" src="http://www.runrails.com/wp-content/uploads/2019/01/wsl-powershell-1024x487.png" alt="" width="840" height="399" srcset="https://www.runrails.com/wp-content/uploads/2019/01/wsl-powershell-1024x487.png 1024w, https://www.runrails.com/wp-content/uploads/2019/01/wsl-powershell-300x143.png 300w, https://www.runrails.com/wp-content/uploads/2019/01/wsl-powershell-768x365.png 768w, https://www.runrails.com/wp-content/uploads/2019/01/wsl-powershell-1200x570.png 1200w, https://www.runrails.com/wp-content/uploads/2019/01/wsl-powershell.png 1780w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> 

Hit &#8220;Enter&#8221; and restart your computer.

<img class="aligncenter size-medium wp-image-285" src="http://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-11.55.07-300x171.png" alt="" width="300" height="171" srcset="https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-11.55.07-300x171.png 300w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-11.55.07.png 718w" sizes="(max-width: 300px) 85vw, 300px" /> 

If you don&#8217;t enter the &#8220;Enable-WindowsOptionalFeature&#8221; line you will get the following error and WSL won&#8217;t work:

<img class="aligncenter wp-image-290 size-large" src="http://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.59.47-1024x181.png" alt="" width="840" height="148" srcset="https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.59.47-1024x181.png 1024w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.59.47-300x53.png 300w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.59.47-768x136.png 768w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.59.47-1200x212.png 1200w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-12.59.47.png 1608w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> 

## Install updates and dependencies

Now that you have WSL installed it&#8217;s time to start it. The first time it starts you are asked to create a user. Go ahead and create one, make sure you remember the password!

<img class="aligncenter size-large wp-image-295" src="http://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.21.33-1024x310.png" alt="" width="840" height="254" srcset="https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.21.33-1024x310.png 1024w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.21.33-300x91.png 300w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.21.33-768x233.png 768w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.21.33-1200x364.png 1200w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-13.21.33.png 1656w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> Before we start installing stuff we have to update the Linux packages. Copy and paste this line to update everything:

<pre class="lang:default decode:true ">sudo apt update && sudo apt dist-upgrade && sudo apt-get autoremove && sudo apt-get clean</pre>

Tip: you can right-click to paste something in the WSL terminal.

It&#8217;s a good practice to run this line once a month to keep things updated and safe.

<img class="aligncenter wp-image-293 size-large" src="http://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-at-13.23.10-1024x541.png" alt="" width="840" height="444" srcset="https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-at-13.23.10-1024x541.png 1024w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-at-13.23.10-300x158.png 300w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-at-13.23.10-768x406.png 768w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-at-13.23.10-1200x634.png 1200w, https://www.runrails.com/wp-content/uploads/2019/01/Screenshot-2018-12-06-at-13.23.10.png 2000w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> 

## Install Ruby and Rails and dependencies

Just a tip: if you hold shift while clicking on the Ubuntu icon you open a second window.

Rails has a lot of dependencies, let&#8217;s install them:

<pre class="lang:default decode:true ">sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev libreadline-dev zlib1g-dev
</pre>

Before we can install Rails we need Ruby first. There are many ways to install Ruby:

  * from apt with sudo apt install ruby2.5
  * with <a href="https://rvm.io/" target="_blank" rel="noopener noreferrer">RVM</a>
  * with <a href="http://rbenv.org/" target="_blank" rel="noopener noreferrer">rbenv</a>
  * compile from source (hard mode)

Let&#8217;s go with rbenv because that is the most common way:

<pre class="lang:default decode:true">curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer | bash</pre>

and add rbenv to the PATH variable, like it asks for after installation:

<pre class="lang:default decode:true">echo 'export PATH="$HOME/.rbenv/bin:$PATH"' &gt;&gt; ~/.bashrc
echo 'eval "$(rbenv init -)"' &gt;&gt; ~/.bashrc
exec $SHELL</pre>

Be careful when copy & pasting, sometimes Windows turns single quotes (&#8216;) into backticks (\`) or other curly quotes.

When rbenv is installed you can use it to install Ruby and activate it. No need for sudo this time.

<pre class="lang:default decode:true">rbenv install 2.5.3
rbenv local 2.5.3</pre>

TIP: I&#8217;ve noticed that WSL sometimes seems to hang, usually selecting the window and pressing a random key is enough to activate it again. So when the commands above seem to take a very long time press the spacebar every now and then to make sure it&#8217;s still working.

Time to install bundler and then Rails:

<pre class="lang:default decode:true">gem install bundler
gem install rails</pre>

You now have an up to date WSL environment with rbenv, ruby 2.5.3 and the latest bundler and rails installed.

## Databases

I&#8217;ll do a writeup of installing Postgresql in another post. But it basically comes down to two choices:

  1. install Postgres inside WSL
  2. install Postgres with the Windows installer

My recommendation is to use the Windows installer because I ran into some weird problems when trying to use Postgres in WSL. Use the <a href="https://www.enterprisedb.com/downloads/postgres-postgresql-downloads" target="_blank" rel="noopener noreferrer">interactive installer</a>, not the Graphical installer by BigSQL.

## Some tips and tricks

Ping me on Twitter or email if you need more help. Or look at one of the guides other people have written:

GoRails: <a href="https://gorails.com/setup/windows/10" target="_blank" rel="noopener noreferrer">Rails on Windows 10</a>

&nbsp;

&nbsp;