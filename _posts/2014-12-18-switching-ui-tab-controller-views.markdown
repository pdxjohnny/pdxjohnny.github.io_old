---
layout: post
title: Switching UI Tab Controller Views
date: '2014-12-18T12:45:00.002-08:00'
author: John Andersen
tags:
- uitabbarcontroller
- iPhone
- xcode
- ios
modified_time: '2015-01-17T00:46:50.881-08:00'
blogger_id: tag:blogger.com,1999:blog-8439713708369185140.post-613793846963991984
blogger_orig_url: http://jsapdx.blogspot.com/2014/12/switching-ui-tab-controller-views.html
---

Say you have one view that would like to be able to switch to another when something happens. Here's how you would do that:


{% highlight objective-c %}
// This is how you change from one view to another
[self.view removeFromSuperview];
[self.tabBarController setSelectedViewController:/*Instance of a view*/ ];
// Or you can do, where index is the position on the bottom starting at 0
[self.tabBarController setSelectedIndex:/*Index of a view*/ ];
{% endhighlight %}

I implemented this by having a global class whose members were all view controllers. When the view controller was initialized it assigned itself to one of  the properties like so:

{% highlight objective-c %}
@interface Controller : NSObject

@property (strong, nonatomic) FirstViewController * view_one;
@property (strong, nonatomic) SecondViewController * view_two;

@end
{% endhighlight %}

Then in each view init you call:


{% highlight objective-c %}
extern Controller * control;
control.view_name = view_one;
{% endhighlight %}
