---
layout: post
title: Playing Linux games with Docker on OS X, or anything
date: '2015-07-20T08:52:00.003-07:00'
author: John Andersen
tags: docker games
modified_time: '2015-07-20T08:52:45.679-07:00'
blogger_id: tag:blogger.com,1999:blog-8439713708369185140.post-4707739597137509789
blogger_orig_url: http://jsapdx.blogspot.com/2015/07/playing-linux-games-on-os-x-with-docker.html
---

Download [boot2docker][boot2docker-url]

start boot2docker

in the boot2docker shell run:

{% highlight bash %}
container_id=$(docker run -d -P pdxjohnny/wesnoth)
docker ps
docker port $container_id 22
  0.0.0.0:<port number>
boot2docker ip
  <ip address>
# ssh in with -X to forward X server (to display the game window)
# using the ip from `boot2docker ip` and the port number from
# `docker port $container_id 22` the last argument is wesnoth-1.10
# which is the name of the game binary
ssh -X user@<ip address> -p <port number> wesnoth-1.10

{% endhighlight %}

[boot2docker-url]: https://docs.docker.com/installation/mac/
