---
layout: post
title:  "Google Analytics in Jekyll in 2020"
date:   2020-08-13 13:37:00 +0200
categories: jekyll
---
I just wasted 30 minutes trying different methods of configuring Google Analytics in my new Jekyll blog. There are enough posts on Stack Overflow and enough blog posts with suggestions but none of them worked well.

## TLDR:
Open `_config.yml` and add this line:

{% highlight yaml %}
  google_analytics: UA-12345678-1
{% endhighlight %}

That's it. No need to change head.html or header.html. No need for a plugin. This works for the `minima` theme.

## Explanation

Recent versions of Jekyll use a different directory structure. By default you don't have `_includes` and `_layouts` directories. So you can't really see what your current theme is and how it works. A theme is a Rubygem and with this command you can see where it is installed.


{% highlight bash %}
bundle info --path minima
/var/lib/gems/2.5.0/gems/minima-2.5.1
{% endhighlight %}

To show the contents of the theme you can use this command:

{% highlight bash %}
tree `bundle info --path minima`
{% endhighlight %}

You may have to install tree if you don't have it. 

It will show you the directory structure and the files. When you open `_includes/google-analytics.html` you can see that the tracking script is already installed and you only have to put your tracking code in `_config.yml`.

You can find the full explanation here: [jekyll-docs](https://jekyllrb.com/docs/themes/)
