---
layout: post
title: "docker boot2docker"
date: 2014-10-27 22:43:43 +0900
comments: true
category: docker
tags: ["boot2docker"]
keywords: docker, boot2docker
published: true

---

概要
====
Mac OSX Yosemite (ver. 10.10 ) に boot2docker をインストールした際のメモ


インストール
====

https://github.com/boot2docker/osx-installer/releases/tag/v1.3.0


初期設定
----

```
$ boot2docker init

Latest release for boot2docker/boot2docker is v1.3.0
Downloading boot2docker ISO image...
Success: downloaded https://github.com/boot2docker/boot2docker/releases/download/v1.3.0/boot2docker.iso
	to /Users/ryo/.boot2docker/boot2docker.iso
Generating public/private rsa key pair.
Your identification has been saved in /Users/ryo/.ssh/id_boot2docker.
Your public key has been saved in /Users/ryo/.ssh/id_boot2docker.pub.
The key fingerprint is:
38:85:1a:95:36:4d:a2:d0:9c:f2:23:61:f3:6b:89:52 ryo@MacBook-Air.local
The key's randomart image is:
+--[ RSA 2048]----+
|  .o .o+.        |
|  =.+o+o.        |
| . *o....        |
|  E +o o         |
| . o.+o S        |
|. . +  .         |
| . .             |
|                 |
|                 |
+-----------------+
```


```
$ boot2docker start

Waiting for VM and Docker daemon to start...
...........................oooooooooooooooooooooooooooo
Started.
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/ca.pem
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/cert.pem
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/key.pem

To connect the Docker client to the Docker daemon, please set:
    export DOCKER_TLS_VERIFY=1
    export DOCKER_HOST=tcp://192.168.59.103:2376
    export DOCKER_CERT_PATH=/Users/ryo/.boot2docker/certs/boot2docker-vm
```

.bashrc

```
export DOCKER_TLS_VERIFY=1
export DOCKER_HOST=tcp://192.168.59.103:2376
export DOCKER_CERT_PATH=/Users/ryo/.boot2docker/certs/boot2docker-vm
```

```
$ $(boot2docker shellinit)
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/ca.pem
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/cert.pem
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/key.pem    
```

```
$ boot2docker socket

	 export DOCKER_HOST=tcp://192.168.59.103:2376


```

動作検証
----


### boot2docker へのログイン


```
$ boot2docker ssh
                        ##        .
                  ## ## ##       ==
               ## ## ## ##      ===
           /""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
           \______ o          __/
             \    \        __/
              \____\______/
 _                 _   ____     _            _
| |__   ___   ___ | |_|___ \ __| | ___   ___| | _____ _ __
| '_ \ / _ \ / _ \| __| __) / _` |/ _ \ / __| |/ / _ \ '__|
| |_) | (_) | (_) | |_ / __/ (_| | (_) | (__|   <  __/ |
|_.__/ \___/ \___/ \__|_____\__,_|\___/ \___|_|\_\___|_|
boot2docker: 1.3.0
             master : a083df4 - Thu Oct 16 17:05:03 UTC 2014

```

```
$ docker run hello-world
Unable to find image 'hello-world' locally
hello-world:latest: The image you are pulling has been verified

511136ea3c5a: Pull complete 
7fa0dcdc88de: Pull complete 
ef872312fe1b: Pull complete 
Status: Downloaded newer image for hello-world:latest
Hello from Docker.
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (Assuming it was not already locally available.)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

For more examples and ideas, visit:
 http://docs.docker.com/userguide/
```

```
$ docker pull ubuntu
ubuntu:latest: The image you are pulling has been verified
d497ad3926c8: Pull complete 
ccb62158e970: Pull complete 
e791be0477f2: Pull complete 
3680052c0f5c: Pull complete 
22093c35d77b: Pull complete 
5506de2b643b: Pull complete 
511136ea3c5a: Already exists 
Status: Downloaded newer image for ubuntu:latest
```

```
$ docker run ubuntu /bin/echo hello world
hello world
```

2 回目以降
====

```
$ boot2docker start

Waiting for VM and Docker daemon to start...
.................oooo
Started.
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/ca.pem
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/cert.pem
Writing /Users/ryo/.boot2docker/certs/boot2docker-vm/key.pem
Your environment variables are already set correctly.
```

Memo
====

Dockerfile

```
FROM ubuntu:12.04

RUN apt-get -qq update
RUN apt-get install -yqq apache2
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
```


参照 URL
====

http://qiita.com/AH_korikori/items/8a165c081e8eb699deb3
/