---
id: 63
title: Monitoring Sidekiq with email and SMS alerts
date: 2018-08-14T12:05:16+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=63
permalink: /monitoring/monitoring-sidekiq-with-alerts/
categories:
  - Monitoring
---
[Sidekiq](https://github.com/mperham/sidekiq) is a simple and efficient background processing tool for Ruby on Rails apps. You should use it for tasks that take too long to put in a controller or tasks that need to run on a schedule. Common examples are sending emails, generating pdf&#8217;s and connecting to other services through an API.

Like all things in IT, Sidekiq can crash, get slow or need more capacity. And for some applications that really should not happen. Because nobody directly interacts with Sidekiq it can be a long time before someone notices that something is wrong.

A good solution to quickly restart (kick, hehe) a crashed Sidekiq is to have a watchdog on the server. Both systemd and upstart can do that for you, and there are lot of other watchdogs you can install. But I&#8217;d still like to know something happend, or when an edge case happens where the watchdog cannot fix it. The solution? Monitoring Sidekiq from the outside.

<!--more-->

In this short tutorial we&#8217;ll be using <a href="https://updown.io/r/vw0un" target="_blank" rel="noopener">Updown.io</a> to check if Sidekiq is still running smoothly. To do that we&#8217;ll add a route and simple controller method to connect to Sidekiq and output a text that updown.io can check for. You can use any other website monitoring tool that can check for a text on a page.

## 1. Add a route

Add this route to config/routes.rb. This will redirect yourdomain.com/health\_checks to the sidekiq method in health\_checks_controller.rb that we&#8217;ll create in the next step.

<pre class="lang:default decode:true" title="Add a new route to your config/routes.rb file">get 'health_checks' =&gt; 'health_checks#sidekiq'</pre>

## 2. Create the controller

Put the following code in the new health\_checks\_controller.rb

<pre class="lang:ruby decode:true">class HealthChecksController &lt; ApplicationController
  # only if you use Pundit
  before_action :skip_authorization

  def sidekiq
    require 'sidekiq/api'

    latency = Sidekiq::Queue.new.latency
    stats = Sidekiq::Stats.new

    render(plain: 'No processes',
      status: :service_unavailable) && return if stats.processes_size == 0
    render(plain: "Too many enqueued (#{stats.enqueued})",
      status: :service_unavailable) && return if stats.enqueued &gt; 250
    render(plain: "Latency more than 10 minutes (#{latency})",
      status: :service_unavailable) && return if latency &gt; 600
    render plain: 'Sidekiq is alive and kicking',
      status: :ok
  end
end
</pre>

You can probably make updown.io login to your app and add a user for it, but to me that seems like a waste of resources and useless complexity. So I just made the /health_check page public. There is no security risk, it&#8217;s just a text on a page that nobody knows about.

Because the page has to be public you have to tell Pundit to skip authorization. If you don&#8217;t use Pundit, just delete that line.

The code inside the sidekiq method checks if Sidekiq is alive, if there aren&#8217;t too many jobs in the queue and if it doesn&#8217;t take more than 10 minutes to complete a job. You can add more checks if you have different problems.

The text is a minimal explanation that will be emailed to you by updown.io if the &#8220;Sidekiq is alive and kicking&#8221; text is not visible. That gives you an indication of how urgent the problem is.

## 3. Setup updown.io check

I&#8217;ve written about [updown.io](https://updown.io/r/vw0un) before here: [Simple downtime alerts for your Rails app](http://www.runrails.com/servers/simple-downtime-alerts-for-your-rails-app-in-5-minutes/)

Follow the steps in that post to create an account and add a check that checks if &#8220;https://www.yourdomain.com/health_checks&#8221; displays &#8220;Sidekiq is alive&#8221;.  You can use another monitoring tool if you already have one that you like and trust.<figure id="attachment_70" aria-describedby="caption-attachment-70" style="width: 596px" class="wp-caption alignnone">

<img class=" wp-image-70" src="http://www.runrails.com/wp-content/uploads/2018/08/Screenshot-2018-08-14-13.57.08-300x75.png" alt="" width="596" height="149" srcset="https://www.runrails.com/wp-content/uploads/2018/08/Screenshot-2018-08-14-13.57.08-300x75.png 300w, https://www.runrails.com/wp-content/uploads/2018/08/Screenshot-2018-08-14-13.57.08-768x191.png 768w, https://www.runrails.com/wp-content/uploads/2018/08/Screenshot-2018-08-14-13.57.08.png 1020w" sizes="(max-width: 596px) 85vw, 596px" /> <figcaption id="caption-attachment-70" class="wp-caption-text">What it looks like in the browser.</figcaption></figure> 

[Surface Interval](https://www.surfaceinterval.co) doesn&#8217;t use Sidekiq yet, but I&#8217;ve added the check so you can see what it looks like: <https://www.surfaceinterval.co/health_checks>

## 4. Add more checks

If you want to monitor something else just add another route and another method to the controller. Or if you want to be cheap and save on checks, add it to the same method.

I found no need to add a separate check for Redis, because Sidekiq will fail immediately if there is something wrong with Redis.