---
layout: post
title: iOS Programing
date: '2014-12-17T14:46:00.002-08:00'
author: John Andersen
tags:
modified_time: '2015-01-17T00:47:25.228-08:00'
blogger_id: tag:blogger.com,1999:blog-8439713708369185140.post-8699112320723922852
blogger_orig_url: http://jsapdx.blogspot.com/2014/12/ios-programing.html
---

Rendering
---

If you are ever wondering why events you call such as setting UITextField values are waiting two minutes to update its because of iOS is set up to run display changing things on the main thread. To get around this and execute something as soon as possible we can call:

{% highlight objective-c %}
dispatch_async(dispatch_get_main_queue(),^ {
    [self populate_from_dict: student];
});
{% endhighlight %}

Asynchronous calls and blocks
---

Here's and example of a block function which we can use as a callback:

{% highlight objective-c %}
^(NSHTTPURLResponse * response, NSString * page)
{
  NSLog( @"%@", page );
};
{% endhighlight %}
