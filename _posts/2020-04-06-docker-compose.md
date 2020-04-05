---
layout: post
title: Take away from a docker-compose file
date: 2020-04-06 03:56
category: 
author: 
tags: []
summary: 
---

## Bootstrap docker/docker-compose in RHEL-7

    $ sudo yum install docker docker-compose -y
    $ sudo systemctl start docker
    $ sudo systemctl status docker
    $ sudo chkconfig docker on

## Write/translate a docker run into a docker-compose yml.

Ref: https://stackoverflow.com/a/49985823/654952

e.g. for this specific ss server, the `docker run` is as follows:

    $ docker run --rm -d --restart=always -e "SS_PORT=8888" \
            -e "SS_PASSWORD=rhyw" -e "SS_METHOD=aes-256-cfb" \
            -e "SS_TIMEOUT=600" imhang/shadowsocks-docker

While the equivalent docker compose file is as follows:

    version: '3'
    services:
    ssserver:
        image: imhang/shadowsocks-docker:latest
        command: '--restart=always -e "SS_PORT=8888" -e "SS_PASSWORD=rhyw" -e "SS_METHOD=aes-256-cfb" -e "SS_TIMEOUT=600"'

Super easy/helpful, huh?

Plus after the docker is up and running, we could check the exact command running by an option `no-trunc`:

    $ docker ps --no-trunc
    $ docker images
    $ docker rmi `name-of-image` or `id of image`
    # This remove all dangling(untagged) images
    $ docker rmi `docker images -f "dangling=true" -q`

To be continue...
