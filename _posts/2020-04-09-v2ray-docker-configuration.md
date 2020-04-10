---
layout: post
title: GCP+V2ray+docker configuration 
date: 2020-04-09 02:38
category: 
author: 
tags: []
summary: 
---

## Step-by-step guide on setting up v2ray.

- Apply a server under GCP, using minimum configuration and container optimized OS. 
- Pull image from the official registry:    

        $ sudo docker pull v2ray/official

- Create `/etc/v2ray/config.json` as below.

        $ cat /etc/v2ray/config.json 
        {
        "inbounds": [{
            "port": 8888,
            "protocol": "vmess",
            "settings": {
            "clients": [{ "id": "548d170c-7b25-11ea-9c90-bca8a698e4c3" }]
            }
        }],
        "outbounds": [{
            "protocol": "freedom",
            "settings": {}
        }]
        }

Replace the `id` field by generating your own uuid with uuid generation tool.

- Start docker
  
        sudo docker run -d --name v2ray -v /etc/v2ray:/etc/v2ray -p 8888:8888 v2ray/official  v2ray -config=/etc/v2ray/config.json

- Configure client
  
  Let's say it's android, go to [v2rayNG][V2rayNG] and download the APK.
  Install server and configure below items:

        address: ip addr of the VPS server.
        port: same with above server-side config.
        id: identical id from the above config.json.
        alterid: 0.
        security: auto
        network: tcp
        head type: none
  
  [v2rayNG]: https://github.com/2dust/v2rayNG/releases

- Save and export as vmess string and share with router.
