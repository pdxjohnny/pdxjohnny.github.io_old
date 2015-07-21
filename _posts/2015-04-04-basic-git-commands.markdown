---
layout: post
title: Basic Git Commands
date: '2015-04-04T14:12:00.000-07:00'
author: John Andersen
tags:
modified_time: '2015-04-04T14:12:01.905-07:00'
blogger_id: tag:blogger.com,1999:blog-8439713708369185140.post-4529327805420040232
blogger_orig_url: http://jsapdx.blogspot.com/2015/04/basic-git-commands.html
---

Create a new repository:

{% highlight bash %}
cd to/directory
git init
{% endhighlight %}

Create Remote

{% highlight bash %}
cd to/directory
git remote add origin https://url
{% endhighlight %}

Set Remote

{% highlight bash %}
cd to/directory
git remote set-url origin https://url
{% endhighlight %}

Stage files for commit ( you need to do this to commit files )

{% highlight bash %}
cd to/directory
git add . # Adds all files
git add filename # Adds specific file
{% endhighlight %}

Commit and push files ( once you've added your changes )

{% highlight bash %}
cd to/directory
git commit -m "Commit message, describes what changed in this commit"
git push
{% endhighlight %}
