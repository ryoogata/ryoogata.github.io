---
layout: post
title: "archlinux"
date: 2014-12-23 22:55:03 +0900
comments: true
category: archlinux
tags: []
keywords: archlinux
published: false

---

http://daimatz.hateblo.jp/entry/2012/12/08/131716

http://www.programming-knowledge.com/wiki/%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF%E3%82%92%E8%A8%AD%E5%AE%9A%E3%81%99%E3%82%8B%28RaspberryPi/ArchLinux%29

パッケージを最新版に

```
# pacman -Syu
```

netctl を使う
netcfg は obsolate


```
[root@alarmpi ~]# cp /etc/netctl/examples/ethernet-static /etc/netctl/eth0
```


timezone の変更

```
# timedatectl set-timezone Asia/Tokyo
```



NTP

https://wiki.archlinux.org/index.php/Systemd-timesyncd

```
# timedatectl set-ntp true 
```



/etc/systemd/timesyncd.conf

```
[Time]
NTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org
FallbackNTP=0.pool.ntp.org 1.pool.ntp.org 0.fr.pool.ntp.org
```

https://bbs.archlinux.org/viewtopic.php?id=182600
