---
id: 37
title: 'Why you should not run two Rails apps on the same server'
date: 2018-08-08T00:00:33+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=37
permalink: /deploying/why-you-shouldnt-run-two-rails-apps-on-the-same-server/
categories:
  - Deploying
  - Servers
---
It sounds convenient right? Install a server once and every time you build an app you add it to the server. If you update something on the server it will be updated for all the apps at the same time. Which may save you time and money.

Is it hard to install multiple apps on the same server? No not at all. Will it save you time setting things up? Yes. Will it make you happy in the long run? No!

<!--more-->

My number one rule in server management is KISS. Keep It Simple and Straightforward. And putting multiple apps on the same server breaks that rule for me. Let me give you some examples of situations I have been in when separate servers for apps would have made troubleshooting easier. Troubleshooting is when it matters most, because by definition it is harder than setting things up.

> Everyone knows that debugging is twice as hard as writing a program in the first place. &#8230; Even if debugging code is 1 unit of brainpower harder than coding. If you put all your units of brainpower into writing your code you won&#8217;t be able to debug it.

### 1. When both applications use the same services

Take Redis for example. Redis is an in-memory key-value store that does not have authentication or authorisation by default. If two applications start using Redis with the default settings they will write in the same store. That may cause some really weird behaviour that is easy to fix but hard to find.

&#8211;update: Josh Divr pointed out that there is a [Redis namespace gem](https://github.com/resque/redis-namespace) to solve this particular example, but other services can still have this problem.

### 2. Security

If you have a security issue or breach the fallout is not limited to that app but all apps on that server. Of course every app has it&#8217;s own user account and cannot access the others. But you never know for sure. There are many more local root exploits (bug that will give a normal user access to root permissions) than there are remote exploits.

### 3. Performance issues

Performance issues with an application are much easier to troubleshoot when the only thing a server is doing is running your app. If it&#8217;s running two or more apps it can be hard to tell which app is taking up your resources.

### 4. Time slots for updating

Most applications have a time in the day when usage is low, for example because the primary users are in the same country or timezone. When they sleep you can do maintenance. When you have multiple applications and they serve people on different sides of the world you may be left with little or no time to do maintenance. It&#8217;s may not be a big problem for routine updates but extending a partition on a disk that takes an hour or more may not be possible without upsetting people.

### 5. Software dependencies

Managing a server and solving problems becomes harder when apps require different versions of libraries and services. Installing two different versions of PostgreSQL, imagemagick or Redis on a normal Linux distribution isn&#8217;t straightforward and will be harder to maintain and update.

### Bonus: server costs on small Digital Ocean droplets

This is an exception I found and not a general rule. On <a href="https://m.do.co/c/c86b33c659ed" target="_blank" rel="noopener">Digital Ocean</a> the smallest server (1 vCPU, 1 GB memory, 25GB disk) is usually enough for a Rails application. The smallest server also has the best value for money. When you double the memory to 2 GB, so it can run 2 Rails apps, the price doubles but you still have 1 vCPU. And when you use 2 servers you have 2 vCPU&#8217;s and 2GB of memory. I know it&#8217;s not a big profit, but I see so many people asking about cheap Rails hosting that thought I should mention it.

&nbsp;

&nbsp;

&nbsp;
