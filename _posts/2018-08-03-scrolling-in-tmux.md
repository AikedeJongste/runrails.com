---
id: 9
title: How to scroll back in Tmux
date: 2018-08-03T09:27:12+00:00
author: Aike
layout: post
guid: http://www.runrails.com/?p=9
permalink: /tmux/scrolling-in-tmux/
categories:
  - tmux
---
<p class="p1">
  What if your terminal couldn’t scroll back? When I just started using <a href="https://en.wikipedia.org/wiki/Tmux">Tmux</a> I didn’t know how to scroll back so I used to run Rails Server in a seperate terminal.
</p>

<p class="p1">
  When I finally figured out how to scroll back up I also learned the hard way that you have to scroll down or otherwise your Rails server (and the whole app) will hang.
</p>

<p class="p1">
  There are 2 ways of scrolling in Tmux. You can configure them in your .tmux.conf and they can be used together.
</p>

<!--more-->

### With your mouse {.p1}

<p class="p1">
  Almost every setting in Tmux can be applied while running Tmux and through the .tmux.conf config file. Applying settings while running Tmux is a convenient way to test if they do what you expect. If  you press ctrl + b then type
</p>

<pre class="lang:default decode:true ">:set -g mouse on</pre>

Scrolling with your mouse should work now. To make this setting permanent you have to add it to your config without th colon in front of it. Tmux has 2 config files: ~/.tmux.conf in your home directory for your personal settings and /etc/tmux.conf for the system wide config file. I recommend that you only make changes to your personal config. That will also override any setting made in the system wide config.

### With keyboard shortcuts

<p class="p1">
  Scrolling with keys is enabled by default in Tmux. Just press ctrl + b then [ to move around with the arrow keys.
</p>

<p class="p1">
  If you have recent Macbook with the tiny unusable arrow keys you can use vim-keys or emacs keys as well. To test it while running Tmux use:
</p>

<pre class="lang:default decode:true">:setw mode-keys emacs</pre>

or

<pre class="lang:default decode:true">:setw mode-keys vi</pre>

Just as with the mouse settings you have to add them to your .tmux.conf to make them permanent. Some people use set-window-option in stead of setw, it&#8217;s the same. setw is an alias for set-window-option.

<p class="p1">
  If you want to scroll all the way back down you just press enter. This also stops your app from hanging when rails server is outputting a lot of data.
</p>

Here is a short list of key combinations you can use:

<pre class="lang:default decode:true">Function                     vi              emacs

--------                     --              -----

Half page down               C-d             M-Down
Half page up                 C-u             M-Up
Next page                    C-f             Page down
Previous page                C-b             Page up
Scroll down                  C-Down or C-e   C-Down
Scroll up                    C-Up or C-y     C-Up
Search again                 n               n
Search again in reverse      N               N
Search backward              ?               C-r
Search forward               /               C-s

</pre>

<p class="p1">
  Note that you have to press ctrl-b twice if you use that for page up since ctrl-b is bound as the command key.
</p>