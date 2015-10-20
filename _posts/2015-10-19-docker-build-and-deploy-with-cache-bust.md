---
layout:     post
title:      How to Deploy With Docker or Cache Busting Done Right
date:       2015-10-19
summary:    A breakdown of how to deploy docker the sexiest, most dev-opsiest, way possible. With appropriate cache-busting techniques.
categories: docker
comments:   true
related:    true
---

When I first started using docker the idea of image composition struck me as being quite powerful. Docker is just a bunch of syntactic sugar for container creation and orchestration, but it's some pretty damn good sugar. However, as I started to dig into the docs a few things started to bother me.

The first thing that bothered me was understanding what a commit in docker actually was. The docker docs don't come out and explicitly say that a Dockerfile and an image are the same thing, but they don't do a good job of seperating the two. At first I thought that a Dockerfile was an image, in the sense that it was a set of static instructions that would always get run when an image was pulled. This isn't the whole story on images. An image is actually the **result** of a specific set of instructions being run, as specified by a Dockerfile, at a particular point in time by a particular user. For example when you pull a Docker image, if the following command was part of the Dockerfile,

{% highlight bash %} 
RUN git clone https://github.com/someuser/somerepo.git
{% endhighlight%} 

That command isn't run when you pull the image. Instead the *result* of that command, when it was last run, by the image's maintainer, is pulled down.
