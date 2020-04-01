---
layout: post
title: OpenShift key concepts walkthrough
date: 2020-04-01 14:31
category: openshift
author: 
summary: 
---
I've walked through the key concepts for openshift, by creating a sample project `ruby-ex` using the [web console][Web console]. Follow [rhocp getting started guide][RHOCP getting started guide], and setting triggers by adding github webhook to build images upon restarts. There's also the command line walkthrough which helps to get a brief idea of how deployment on psi works. Useful commands includes:

{% highlight bash %}
oc login
oc new-app
# Track the progress of the build
oc logs bc/ruby-ex
oc get pods[builds|svc|route|dc]
oc expose service <name-of-the-service>
oc describe bc ruby-ex
oc status
{% endhighlight %}


## Play with containers

This section tracks the experiments following the [getting started with containers][Getting started with containers].


## references


[Web console]: https://paas.psi.redhat.com/
[PSI OpenShift Onboarding(internal)]: https://docs.engineering.redhat.com/x/qVvVAg
[RHOCP getting started guide]: https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html#getting-started-developers-console
[Getting started with containers]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html-single/getting_started_with_containers/index#get_started_with_docker_formatted_container_images
