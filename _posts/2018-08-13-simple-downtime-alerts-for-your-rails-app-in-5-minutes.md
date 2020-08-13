---
id: 47
title: Simple downtime alerts for your Rails app in 5 minutes
date: 2018-08-13T16:32:21+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=47
permalink: /servers/simple-downtime-alerts-for-your-rails-app-in-5-minutes/
categories:
  - Monitoring
  - Servers
---
How do you know your Rails app is still online? How do you know it&#8217;s not displaying some error? That&#8217;s what monitoring is for. If you look around you&#8217;ll find lots of solutions. Most of these solutions are overkill if you are just starting with Rails servers or if you only have a few applications.

## What is important when choosing a monitoring tool?

<!--more-->

### 1. It should be a hosted solution

If you have to install and maintain it yourself you need another monitoring tool to monitor the monitoring&#8230; Yo dawg&#8230; And hosted solutions provide features that are really hard to maintain by yourself, like monitoring from multiple locations and sms messaging.

### 2. Notifcations

You need some kind of notification that you will notice immediately when it comes in. So if you disabled notifications for email on your phone you need something else. For me SMS (text message) works great because nobody in Europe uses them anymore. A smartphone App with customizable notifications is fine too.

### 3. Simplicity

A lot of monitoring tools are too complicated and have features you&#8217;ll never use. And maintaining all these features often makes them expensive. You more prone to make mistakes setting things up if the interface is more complicated. Monitoring tools should be simple and have a reasonable pricing structure.

## My favorite: [updown.io](https://updown.io/r/vw0un)

Updown.io is definitely my favorite. The interface and the pricing structure are really simple. But it still has all the features you need in an uptime monitoring tool. It even checks ipv6 adressess. It does not have a monthly cost but you buy credits, and every check costs a credit. You get 100.000 credits to start with. Which is enough to check 1 app for a year at a 5 minute interval.

It has a nice [status page](https://updown.io/hgv7?locale=en) that you can share with a client or put up on a tv-screen. You can even put it on your own domain: [status.runrails.com](https://status.runrails.com/?locale=en)

You can obviously use updown.io for WordPress and other sites as well. But since this blog is about Rails it&#8217;s worth mentioning that it has a [Ruby Gem.](https://github.com/askehansen/updown-ruby)<figure id="attachment_60" aria-describedby="caption-attachment-60" style="width: 700px" class="wp-caption aligncenter">

<img class="wp-image-60" title="updown interface" src="http://www.runrails.com/wp-content/uploads/2018/08/updown-interface-300x175.png" alt="" width="700" height="408" srcset="https://www.runrails.com/wp-content/uploads/2018/08/updown-interface-300x175.png 300w, https://www.runrails.com/wp-content/uploads/2018/08/updown-interface-768x448.png 768w, https://www.runrails.com/wp-content/uploads/2018/08/updown-interface-1024x597.png 1024w, https://www.runrails.com/wp-content/uploads/2018/08/updown-interface-1200x700.png 1200w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /> <figcaption id="caption-attachment-60" class="wp-caption-text">updown interface</figcaption></figure> 

## How to setup updown.io

  1. Go to [updown.io](https://updown.io/r/vw0un), sign up and verify your email address.
  2. Login and go to settings to add your phone number if you want to receive text messages.
  3. Now it&#8217;s time to add a check, go back to the main screen and select &#8220;https&#8221; in the first column. If you monitor your app through &#8220;https&#8221; you will get **notifications when your SSL certificate is about to expire**.
  4. Put your url in the second column, for example: &#8220;www.runrails.com&#8221;
  5. Adding an Alias is optional, I usually put in a short description like &#8220;runrails website&#8221;
  6. Contains is also optional, but useful. If you enter a few words that are displayed on your site updown.io will check for them. I&#8217;m going for &#8216;Ask me a question!&#8217; because that&#8217;s on every page and it definitely won&#8217;t be shown when the site is down or giving an error.
  7. Check Interval: I usually go for 5 or 10 minutes when monitoring for uptime, and for 1 hour when I only care about the SSL certificate.
  8. Apdex is the response time of your app or site. If your site suddenly becomes slow, updown.io will notice and send you and alert. I usually go for the 1 or 2 second values because I only want an alert when an app is really slow.
  9. You can leave the other options at their default settings.
 10. Click the save icon to save your site and start monitoring.

Now don&#8217;t worry if it doesn&#8217;t go to green immediately or shows status &#8220;UNKNOWN&#8221; for a while. That is normal for the first hours.

### Optional settings:

  * Enable the checkbox for &#8220;SSL expiration notifications&#8221; if you want to receive email notifications for SSL certificates that are about to expire.
  * Add email addresses of your colleages or clients in the &#8220;<label for="user_cc">Email alert addresses&#8221; field. They don&#8217;t need their own accounts if you add them to yours.</label>
  * Connect your teams Slack Channel.

&nbsp;