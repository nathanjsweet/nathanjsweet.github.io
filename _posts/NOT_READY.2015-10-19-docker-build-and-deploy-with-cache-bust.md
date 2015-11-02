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

The first thing that bothered me was understanding what a commit in docker actually was. The docker docs don't come out and explicitly say that a Dockerfile and an image are the same thing, but they don't do a good job of seperating the two. At first I thought that a Dockerfile *was* an image, in the sense that it was a set of static instructions that would always get run when an image was pulled. This isn't true, and would actually make image stability completely useless if it was. 

An image (always a tag, `:latest` by default) is actually the **result** of a specific set of instructions being run, as specified by a Dockerfile, at a particular point in time by a particular user. This snapshot (or image) of that composition can be pulled and run.

For example when you pull a Docker image, if the following command was part of the Dockerfile,
<pre>
  <code class="dockerfile">
RUN git clone https://github.com/someuser/somerepo.git
  </code>
</pre>
That command isn't run when you pull the image. Instead the *result* of that command, when it was last run, by the image's maintainer, is pulled down. That command may or may not have even been run the last time that specific image was built. Mostly the commands `RUN`, `ADD`, `COPY`

1. Any command is run again if the command prior to it ran.
2. `RUN` commands run again if the command has changed in anyway or if rule 1 applies
3. `ADD` and `COPY` commands are run again if the files they are copying have changed since the last time they were copied (this is done by computing comparing the hashes of all the files being copied), or, again, if rule 1 applies.

If you put this altogether you begin to realize that images are built by Dockerfiles, but different tags of the same image don't necessarily even have to be built by the same Dockerfile. This is desireable, because the up-and-running nature of docker images is dangerous and a slew of bad operations practices have cropped as a result. Such as services being run as root, and compilers, linkers, git, etc, are deployed in the image, mostly so that people can build on the same computer they're running on. Furthermore, people are using cache-busting techniques to make sure that their `RUN` commands that do things like `git clone` or 'wget' are being run everytime the image builds.
