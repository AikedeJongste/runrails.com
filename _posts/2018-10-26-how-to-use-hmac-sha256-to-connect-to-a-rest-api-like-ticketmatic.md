---
id: 183
title: How to use HMAC-SHA256 to connect to a REST API like Ticketmatic
date: 2018-10-26T12:49:25+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=183
permalink: /programming/how-to-use-hmac-sha256-to-connect-to-a-rest-api-like-ticketmatic/
categories:
  - Programming
---
A client recently asked me to export records from Ticketmatic. Ticketmatic is a SAAS application for selling event tickets. They have a JSON API, so I figured it would be easy. Just send a GET request to some URL and parse the result as JSON right?

That doesn&#8217;t work because they use a hashing algorithm called HMAC-SHA256. This requires you to sign every request you make with a secret key to create a signature. After that, you have to put the signature, the current timestamp and an access key in the Authorization header of the request. Not just once but for every request!

&nbsp;

<!--more-->

&nbsp;

There is an example header in the [Ticketmatic API documentation](https://www.ticketmatic.com/docs/api/coreconcepts/authentication/) but not much else for Ruby developers. Here is how I did it.

**TDLR: **[here is the gist](https://gist.github.com/AikedeJongste/38e7e5059afd2352a7cc66fb6b691247) on Github if you don&#8217;t need the explanation.

## Setup variables:

To create your own Authorization header you need some variables. Let&#8217;s start with the keys. You need an access key and a secret key. I got them from the clients&#8217; account manager and put them in separate files (_access_key.txt_ and _secret_key.txt_) and added the filenames to my .gitignore so they won&#8217;t be committed in the repository.

I did not use Rails for this tiny project but just a Ruby script called _connect.rb_. So open your editor again, create connect.rb and require the classes we need. Require &#8216;net/http&#8217; will also require &#8216;uri&#8217; so you don&#8217;t need to require it separately.

<pre class="lang:ruby decode:true " title="connect.rb">require 'openssl'
require 'Base64'
require 'net/http'</pre>

You can import the 2 files with keys we created earlier in your script by simply reading the first line with the _File.open_ command and then strip away any unnecessary spaces with _.strip_.

<pre class="lang:default decode:true">access_key = File.open('access_key.txt', &:readline).strip
secret_key = File.open('secret_key.txt', &:readline).strip</pre>

The next thing we need is the current time in the right format:

<pre class="lang:default decode:true">time = Time.now.utc.strftime('%Y-%m-%dT%H:%M:%S'</pre>

And the Ticketmatic account name:

<pre class="lang:default decode:true ">name = 'aikes-ticketmatic'</pre>

Here is the magic part where we use the variables we just created to generate the HMAC part:

<pre class="lang:default decode:true ">hmac = OpenSSL::HMAC.hexdigest(OpenSSL::Digest.new('sha256'), 
       secret_key, access_key + name + time)</pre>

Next up is the uri we want to connect to. This is the Ticketmatic URL, the name of your account and the information you want from Ticketmatic. In this example I use &#8216;/diagnostics/time&#8217;. That will simply return the current time if you did everything right. This is also something that every API user can request so we won&#8217;t have any issues with permissions.

<pre class="lang:default decode:true">uri = URI("https://apps.ticketmatic.com/api/1/#{name}/diagnostics/time")
</pre>

We are almost there. Let&#8217;s set up a request and add the &#8216;Authorization&#8217; header to it.

<pre class="lang:default decode:true">req = Net::HTTP::Get.new(uri)
req['Authorization'] = "TM-HMAC-SHA256 key=#{access_key} ts=#{time} sign=#{hmac}"
</pre>

And then run the request and return the results:

<pre class="lang:default decode:true">result = Net::HTTP.start(uri.hostname, uri.port, use_ssl: true) { |http| http.request(req) }
return result.body</pre>

## Running the script:

If you named the script _connect.rb_ like I did you can run it with &#8220;ruby connect.rb&#8221;. And if all goes well you&#8217;ll see something like this:

<pre class="lang:js decode:true">{"systemtime":"2018-10-26T11:59:18"}</pre>

That&#8217;s the system time on the Ticketmatic server returned to you in JSON format.

That&#8217;s it, we did the whole HMAC-SHA256 thing and got the time back. A next step is to change the URL to something useful like [/contacts](https://www.ticketmatic.com/docs/api/contacts/getlist/). You can find all of the available operations in the Ticketmatic API documentation.

## Send a SQL query if you need many records

If you need a lot of records you can send SQL queries to the API. I&#8217;ve never seen this before but it works really well. The query was created by Ticketmatic for me and was 50 lines long&#8230; You can send the queries to [_&#8220;/tools/queries/export/&#8221;_](https://www.ticketmatic.com/docs/api/tools/export/) and put the query itself in the body of the request.

<pre class="lang:ruby decode:true">req = Net::HTTP::Post.new(uri, 'Content-Type' =&gt; 'application/json')
req.body = {query: your_query}.to_json</pre>

Of course, _your_query_ is the query you want to execute.

&nbsp;