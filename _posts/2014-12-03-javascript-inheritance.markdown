---
layout: post
title: JavaScript Inheritance
date: '2014-12-03T23:17:00.001-08:00'
author: John Andersen
tags:
modified_time: '2014-12-03T23:17:50.750-08:00'
blogger_id: tag:blogger.com,1999:blog-8439713708369185140.post-2805284161001054320
blogger_orig_url: http://jsapdx.blogspot.com/2014/12/javascript-inheritance.html
---

I was recently struggling with inheritance in java script. Obviously its a very nice thing to have being that it makes for less code and more specialization.

Say for instance that we have a class that we want to inherit from called "inherit_from". The class we are making this from we'll call the special class whose name is "special".

Here's how it works

{% highlight javascript %}
var special = function special ( image, canvas_div )
{
  this.image.src  = image;
  this.x = canvas_div.width / 2;
  this.y = canvas_div.height / 2;
  this.allow_movement();
}

special.prototype = new inherit_from();
special.prototype.constructor = special;
{% endhighlight %}

This lets us use a preexisting class which already has a great deal of methods but in addition this can have special methods without the need to copy paste them all.
