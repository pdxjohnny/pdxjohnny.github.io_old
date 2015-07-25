---
layout: post
title: Gitlab Automated Docker Builds
date: '2015-07-24T11:11:00.003-07:00'
author: John Andersen
tags: docker gitlab python
modified_time: '2015-07-24T11:11:00.003-07:00'
---

I've been using docker to deploy a python application due to the
large number of dependencies that it has. Docker is great for this because
in the docker build everything gets installed and will run the same
on any machine.

The project I'm working on is for work so I can't host the source on github
and use the automated build system the docker registry provides. I set up a
Gitlab server to host the source of several of my projects and got to playing
around with it. I discovered that Gitlab has web hooks which will send a json
to a webserver on a push or other action.

Web Hook server
---

If you want to set up automated builds you can clone my [docker repo][docker-repo-url].

{% highlight bash %}
git clone https://github.com/pdxjohnny/docker.git
{% endhighlight %}

The files we are interested in are in docker/gitlab_webhooks

<pre>
<code>
docker
├── adb
│   ╰── Dockerfile
├── apache-php
│   ├── Dockerfile
│   ╰── README.md
├── <font color="red">gitlab_webhooks</font>
│   ├── <font color="purple">app.py</font>
│   ├── <font color="blue">hooks.py</font>
│   ├── <font color="green">notify.py</font>
│   ├── irc_client.py
│   ├── README.md
│   ╰── requirements.txt
├── ircd
│   ╰── Dockerfile
├── wesnoth
│   ╰── Dockerfile
├── x11
│   ╰── Dockerfile
╰── README.md
</code>
</pre>

To start the web hook server we run

{% highlight bash %}
python docker/gitlab_webhooks/app.py
{% endhighlight %}
> More information on how this works can be found in
> [docker/gitlab_webhooks/README.md][docker-gitlab-webhook-readme-url]

Now the web hook receiver server is listening
To view the output of the server, and see the builds as they happen
You need to run sillystream client. Sillystream is a python module that
can be used to redirect stdout and stderr of processes to all clients
connected to its server.

If you don't already have sillystream then you can install it with pip

> You probably don't because I made it last night from 11 to 2
> and hopefully you were asleep. Its like silly string get it. Huh huh!

{% highlight bash %}
sudo -HE pip install sillystream
{% endhighlight %}

Once you have sillystream then connect to the host that you started app.py on

{% highlight bash %}
python -m sillystream client --host localhost
{% endhighlight %}

Now you can see the output of the app daemon when it builds

If you want to get really fancy, and I'm sure you do you're already this far.
You can run notify.py, it takes the same arguments sillystream client does,
host and port. notify.py will run as a daemon and watch the output of build
server (app.py). It will send you a little notification in the top right
corner of your screen notifying you when builds start and are finished. This way you don't have to watch the output of sillystream client.

![webhook-commit](/assets/webhook_commit.gif)

{% highlight bash %}
python docker/gitlab_webhooks/notify.py
{% endhighlight %}
> I've only tested this on ubuntu

![webhook-commit](/assets/docker_build_done.gif)

Gitlab Web Hooks
---

Now that you've set up the web hook server its time to create a web hook in
Gitlab.

The default port that the web hook server (app.py) runs on is 9898.
If you ran app.py on localhost then the url to add is `http://localhost:9898/`

![gitlab-webhook-setup](/assets/gitlab_webhook_setup.png)

Registry
---

By default the registry to push the built image to is `localhost:5000`
If you want to push to the public registry set `DOCKER_REGISTRY = False`
in docker/gitlab_webhooks/hooks.py

If you need to set up a registry then look at dockers page on [deploying a registry server][deploy-reg-url]

> The command should be as follows

{% highlight bash %}
docker run -d -p 5000:5000 \
  --restart=always --name registry registry:2
{% endhighlight %}
> Thats what it was when I wrote this

Push
---

Now you've got everything set up. Make sure that the repository url on Gitlab
is accessible by the machine running app.py

Then push your changes! You should see that app.py has started to build

![sillystream-docker](https://raw.githubusercontent.com/pdxjohnny/sillystream/master/examples/docker.gif)

If you're running notify.py you should receive notifications on the builds
start and finish

###Links
1. [Docker repo][docker-repo-url]
2. [Docker gitlab webhook readme][docker-gitlab-webhook-readme-url]
3. [Deploy registry][deploy-reg-url]

[docker-repo-url]: https://github.com/pdxjohnny/docker.git
[docker-gitlab-webhook-readme-url]: https://github.com/pdxjohnny/docker/tree/master/gitlab_webhooks/README.md
[deploy-reg-url]: https://docs.docker.com/registry/deploying/
