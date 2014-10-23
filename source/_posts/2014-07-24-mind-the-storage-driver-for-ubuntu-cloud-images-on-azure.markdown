---
layout: post
title: "Mind the storage driver for Ubuntu cloud images (on Azure)"
date: 2014-07-24 12:30:43 +0000
comments: true
published: true
categories: [Azure, Docker]
---

A few days ago I wanted to build Firefox OS' newest release for a
friend. Because I did not wanted these GB of code, binaries etc. on my
notebook I fired up an Ubuntu image on Microsoft Azure. I feared that
at a certain point in the build process I may had to download
everything to my local machine and therefore I installed Docker via a
simple

```
sudo apt-get install docker.io
``` 

Then I started the build process as laid out on Mozilla's Developer
Network. But, during downloading the source code (that's about 12 GB
of Git repositories from Mozilla and Android), I got a "no more space
left on device". That was strange: I had a 100 GB volume attached to
the VM and enough space and inodes left. After some searching I asked
on the IRC channel and got a good hint: "What's your storage driver?" 

Well, I thought that it's AUFS; I wanted to add "as usual" because
AUFS was available on my notebook from the beginning. But a `docker.io
info` gave me:

```
$ sudo docker.io info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131188-pool
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 291.5 Mb
 Data Space Total: 102400.0 Mb
 Metadata Space Used: 0.7 Mb
 Metadata Space Total: 2048.0 Mb
Execution Driver: native-0.1
Kernel Version: 3.13.0-29-generic
WARNING: No swap limit support
```

I then learned that somehow the DeviceMapper driver only allows a
certain amount of diffs and I reached that amount with my build
process. (Maybe it's possible to relax that restriction but I do not
know how.)

I learned as well that the Ubuntu cloud image that is provided by
Microsoft Azure doesn't have AUFS support. Therefore Docker uses the
DeviceMapper storage driver instead. After I installed the AUFS
support I could export the images, change the storage driver and
import the images again.

It would be nice seeing the Docker documentation being more detailed
on those storage drivers.

**(Update 2014-10-23)** Thanks to
 [this blog post from Iron.io](http://blog.iron.io/2014/10/docker-in-production-what-weve-learned.html)
 I found some documentation of the devicemapper storage driver. It is
 located in the
 [Repository](https://github.com/docker/docker/tree/master/daemon/graphdriver/devmapper). 
