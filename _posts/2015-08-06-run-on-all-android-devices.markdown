---
layout: post
title: Run On All Android Devices
date: '2015-08-24T11:10:27.003-07:00'
author: John Andersen
tags: bash android
---

Use this script to run a command on all android devices simultaneously

{% highlight bash %}
#!/bin/bash

for device in $(adb devices | grep -v "List" | awk '{print $1}' );
  do
    adb -s $device "$@" &
done;

{% endhighlight %}
