---
layout: post
title:  "rsync as a cron for synchronization between hosts"
date:   2016-05-23 14:13:38 +0800
categories: rsync cron
---
So I've had some tests on the famous `rsync` under linux. One of the key benefits for rsync is the speed and its `delta-transfer` algorithm, as can be addressed from the documentation:

> Rsync is a fast and extraordinarily versatile file copying tool. It can copy locally, to/from another host over any remote shell, or to/from a remote rsync daemon. It offers a large number of options that control every aspect of its behavior and permit very flexible specification of the set of files to be copied. It is famous for its delta-transfer algorithm, which reduces the amount of data sent over the network by sending only the differences between the source files and the existing files in the destination. Rsync is widely used for backups and mirroring and as an improved copy command for everyday use.
> 
> Rsync finds files that need to be transferred using a "quick check" algorithm (by default) that looks for files that have changed in size or in last-modified time. Any changes in the other preserved attributes (as requested by options) are made on the destination file directly when the quick check indicates that the file's data does not need to be updated.

So I've got some repos and commits, some work are on my laptop while others are on my server. Below is what I'm gonna synchronize between my server and my laptop:
{% highlight bash %}
#!/bin/sh

# Back up code repo from my laptop to my code server
rsync -avz -e "ssh -i $HOME/.ssh/id_rsa.pub" $HOME/code/gh yuwang@serveraddr:backup
{% endhighlight %}

Obviously I'd need to run the code from the cron:(Be sure to apply '+x' to the script above)

{% highlight bash %}
sudo crontab -e
05 00 * * * /path/to/my/shell/script
# Save the cron, and we're done.
{% endhighlight %}

More about how `rsync` works can be found in [rsync documentation][rsync-documentation].

[rsync-documentation]: https://download.samba.org/pub/rsync/rsync.html
