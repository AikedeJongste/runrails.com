---
id: 265
title: How to enable pruning on bitcoind
date: 2018-12-10T20:35:54+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=265
permalink: /crypto/how-to-enable-pruning-on-bitcoind/
categories:
  - Crypto
---
If you don&#8217;t want to have the whole bitcoin blockchain on your computer or server you don&#8217;t have to. There is an option that deletes previously verified blocks from your local copy. This is called pruning. To enable it you have to add the following line to your bitcoind config. This config file is usually in &#8216;/etc/bitcoin/bitcoin.conf&#8217;.

<pre class="lang:default decode:true">prune=5000</pre>

After that you have to restart bitcoind and 5 minutes later you have a whole lot more free diskspace.

You can check if pruning is enabled with:

<pre class="lang:default decode:true ">bitcoin-cli getblockchaininfo | grep prune</pre>

It will output something like this:

<pre class="lang:default decode:true">"pruned": true,
  "pruneheight": 552518,
  "prune_target_size": 1073741824,</pre>

## Good to know:

The prune= value has to be higher than 550. And the number indicates the diskspace to be used in Megabytes. I went with 5000 because 5GB seemed reasonable to me.

## Troubleshooting:

After I enabled the prune=5000 option bitcoind wouldn&#8217;t start anymore. There was no usable error:

<pre class="lang:default decode:true">root@cryptodoos:~# service bitcoind start
Job for bitcoind.service failed because the control process exited with error code.
See "systemctl status bitcoind.service" and "journalctl -xe" for details.</pre>

I checked for typo&#8217;s in the config file and read the logfile in /var/lib/bitcoin/debug.log but everything seemed fine. So I started the daemon by hand. This is usually a good approach to see what&#8217;s going on.

<pre class="lang:sh decode:true">root@cryptodoos:~# /usr/bin/bitcoind -daemon -datadir=/var/lib/bitcoin -conf=/etc/bitcoin/bitcoin.conf -pid=/run/bitcoind/bitcoind.pid
Error: Prune mode is incompatible with -txindex.</pre>

And there it is. You have to disable txindex in the bitcoin.conf to use pruning.