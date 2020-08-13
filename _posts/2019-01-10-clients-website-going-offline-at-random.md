---
id: 221
title: Clients website going offline at random
date: 2019-01-10T13:29:17+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=221
permalink: /sysadmin-stories/clients-website-going-offline-at-random/
categories:
  - sysadmin-stories
---
Have you ever been asked for one of those cases that nobody can figure out? Or do you have one of those recurring problems that are impossible to troubleshoot? Maybe you fix them with a reboot when they occur. I love cases like that. You can&#8217;t always fix them, but you always learn something. I had one yesterday and I managed to fix it. Here are some approaches that I used to solve it.

## What was the setup?

This client had two servers. One with some WordPress sites and one with their application. The users connect to the application server and this server is also the proxy to the WordPress sites. This is an easy way to make it look like the WordPress pages are part of the application. When the user is filling forms they interact with the application and when they click on documentation they get a WordPress page. And because the application server proxying this page, it looks like the documentation is in the application. It&#8217;s on the same domain and uses the same layout. Pretty neat.

Making a quick sketch of the situation is often a good idea. It shows the client that you understand their setup and their problem. And it gives you insights that you wouldn&#8217;t by just clicking around.

## What was the problem?

According to the customer, the WordPress server would be completely unreachable and they had no idea what triggered it. Maybe Digital Oceans network was to blame. They solved it temporarily by changing Nginx configs, connecting the proxy to internal addresses instead of public addresses and flushing firewall rules. Oh yes, and lots of reboots.

## Don&#8217;t believe the customer

Like any detective show on television, server problems also come with a lot of misinformation. My client was blaming the Digital Ocean network for being unreliable. But Digital Ocean has a <a href="https://status.digitalocean.com/" target="_blank" rel="noopener">status page</a>, so I went there first. According to them, everything was fine when the problems occurred. So I noted that as their alibi.

When I logged into the server it turned out they were using Apache and not Nginx. And when I asked them if the WordPress pages were unreachable for everyone or just the production server, nobody knew.

## Test it yourself

I did not get a chance to experience the unreachable state myself. Because the downtime was a big problem for their customers and they would fix it as soon as it happened. If you get the chance to see a problem live that&#8217;s always better than relying on reports from eyewitnesses.

I had 3 approaches left to follow:

  1. look around on the server
  2. read the logfiles
  3. read the config files.

The solution is probably in the log files but I choose to look around on the server first. Just a quick check to see the uptime, the load, disk usage, last logins and what is running.

My look around showed nothing that made me suspicious. I used &#8216;w&#8217; to see uptime, load and logged in users with one command:

<pre class="theme:powershell lang:sh decode:true ">user@server:~$ w
 12:51:47 up 2 days,  2:11,  1 user,  load average: 0.25, 0.22, 0.15
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
user     pts/0    213.127.192.xx   12:49    1.00s  0.17s  0.01s w</pre>

## Read the logs

The log files are the place where you usually find the murder weapon. Or in this case, the line that will explain the problem. I always start with the syslog. It&#8217;s in /var/log/syslog. If it&#8217;s not too big I&#8217;ll just open it with Vim or Nano and scroll through it so I get an idea what is going on. If it is big I&#8217;ll grep it for keywords like error, block, killed, etc.

After that, I read individual log files starting with the ones that are the most system and networking related. For example the kernel log, firewall log, fail2ban log. And then the logs from applications that run on the server like the webserver and the database server.

## Conclusion

This time it was fail2ban that blocked the server with the WordPress sites because it was sending too much traffic. I disabled fail2ban and the site has been up ever since!

&nbsp;