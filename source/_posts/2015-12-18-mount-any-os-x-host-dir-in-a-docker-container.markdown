---
layout: post
title: "Mount any OS X host dir in a Docker container"
date: 2015-12-18 00:50:24 +0100
comments: true
categories:
  - Docker
  - OSX
---

... or how to access your local git repositories in a Docker container with docker-machine?

## What Docker does by default

When using Docker on OS X you have to run Docker in a VirtualBox. A convenient tool to achieve this is [Docker Toolbox](https://www.docker.com/docker-toolbox).
It comes with boot2docker to run your Docker containers.

The default installation will only [share](https://github.com/boot2docker/boot2docker#virtualbox-guest-additions) your `/Users` directory with the VirtualBox VM.

But you probably don't store all your projects within your user's home directory, are you? And on top of that you may not want to share all your little secrets of your `/Users` dir with Docker.

## What to do to override this default

In order to mount directories from your host machine outside of the `/Users` dir you have to add a shared folder to VirtualBox and let boot2docker mount that folder on start.

Adding a shared folder can be done via console with `VBoxManage`.
To make your docker-machine VM mount your data folder on startup you can either edit `/var/lib/boot2docker/profile` or `/var/lib/boot2docker/bootlocal.sh`. The `profile` file is mentioned in boot2docker's readme on [GitHub](https://github.com/boot2docker/boot2docker#docker-daemon-options) but what's missing there is, when you regenerate your VM's certificates your profile file will be overwritten with the defaults. So you have to use the latter file `bootlocal.sh`.


## TL;DR;

Assuming your Docker VM is named `default`.

Add a shared folder to you VM - replace the `/path/to/git/repositories/`
```
docker-machine stop default
VBoxManage sharedfolder add "default" --name "data" --hostpath "/path/to/git/repositories/" --automount
VBoxManage setextradata "default" VBoxInternal2/SharedFoldersEnableSymlinksCreate/data 1
docker-machine start default
```

Mount folder in VM on startup
```
docker-machine ssh default \
"echo -e '\
#!/bin/sh\n\
\n\
mkdir -p /home/docker/data\n\
mount -t vboxsf -o uid=1000,gid=50 data /home/docker/data\
' | sudo tee -a /var/lib/boot2docker/bootlocal.sh"
```

Run a Docker container and mount a volume
```
docker run -d -v /home/docker/data/your-project:/opt/www/your-project -it --name nginx nginx
```

Et voilà you have access to your project folder
```
/path/to/git/repositories/> ll
total 0
-rw-r--r--  1 user  group     0B 18 Dez 00:34 index.html

/path/to/git/repositories/> docker exec -it nginx bash
root@710706efa911:/# ls -lah /opt/www/your-project/
total 4.0K
drwxr-xr-x 1 1000 staff  102 Dec 17 23:34 .
drwxr-xr-x 3 root root  4.0K Dec 17 23:33 ..
-rw-r--r-- 1 1000 staff    0 Dec 17 23:34 index.html
```
