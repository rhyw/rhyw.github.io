---
layout: post
title: Playing with containers
date: 2020-04-02 23:56
category: container, podman, openshift
author: Yuguang Wang
tags: []
summary: 
---

So let's try some useful concepts/commands w/ containers. Below are some key takeaway based on [working with containers][working with containers]

[working with containers]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html-single/getting_started_with_containers/index#working_with_containers

## Working with containers

### Running containers
When you execute a `podman run` command, you essentially spin up and create a new container from a container image. The command you pass on the podman run command line sees the inside the container as its running environment so, by default, very little can be seen of the host system.

Perhaps the manpage gives a better brief of what this `podman run` is doing:

    $ podman run --help
    Run a command in a new container

    Description:
    Runs a command in a new container from the given image

    Usage:
    podman run [flags] IMAGE [COMMAND [ARG...]]

    Examples:
    podman run imageID ls -alF /etc
    podman run --net=host imageID dnf -y install java
    podman run --volume /var/hostdir:/var/ctrdir -i -t fedora /bin/bash

### Inspecting containers
###
