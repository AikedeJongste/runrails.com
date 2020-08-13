---
id: 159
title: Multiple Passenger apps on your server? Give them names!
date: 2018-10-11T17:25:54+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=159
permalink: /passenger/multiple-passenger-apps-on-your-server-give-them-names/
categories:
  - Passenger
---
If you run multiple versions of a Rails application on the same server it&#8217;s easy to get them mixed up. I have a client that has 2 versions of the same application on the same server. One version for clients in the Netherlands and one for Belgium. Because they are almost, but not totally, identical I&#8217;m doing as much as I can to make them easy to identify. And today I found a simple trick that I&#8217;d like to share.

<!--more-->

When there are performance or availability problems with these applications I usually start by checking their queue sizes with passenger-status. And by default passenger identifies the apps by the directories they are in. On this server that&#8217;s not helpful at all. So I gave them names.

**Old:**<img class="alignright size-full wp-image-162" src="http://www.runrails.com/wp-content/uploads/2018/10/Screenshot-2018-10-11-11.31.54.png" alt="" width="952" height="662" srcset="https://www.runrails.com/wp-content/uploads/2018/10/Screenshot-2018-10-11-11.31.54.png 952w, https://www.runrails.com/wp-content/uploads/2018/10/Screenshot-2018-10-11-11.31.54-300x209.png 300w, https://www.runrails.com/wp-content/uploads/2018/10/Screenshot-2018-10-11-11.31.54-768x534.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" />

**New:**  
<img class="alignright size-full wp-image-163" src="http://www.runrails.com/wp-content/uploads/2018/10/Screenshot-2018-10-11-12.12.06.png" alt="" width="902" height="682" srcset="https://www.runrails.com/wp-content/uploads/2018/10/Screenshot-2018-10-11-12.12.06.png 902w, https://www.runrails.com/wp-content/uploads/2018/10/Screenshot-2018-10-11-12.12.06-300x227.png 300w, https://www.runrails.com/wp-content/uploads/2018/10/Screenshot-2018-10-11-12.12.06-768x581.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> 

&nbsp;

### How does it work?

You add this line to each Nginx vhost. Or only the vhosts that you want to name. I&#8217;ve added this line right below the line that says passenger environment is production.

<pre class="lang:default decode:true">passenger_app_group_name your_apps_name;</pre>

After that you have to restart or reload Nginx to apply this. I always check my config for errors with _nginx configtest_, and if the config is okay I restart the server. On one line that would be:

<pre class="lang:default decode:true ">service nginx configtest && service nginx restart</pre>

The && will make sure that the restart command won&#8217;t be executed if there is an error in your new config.