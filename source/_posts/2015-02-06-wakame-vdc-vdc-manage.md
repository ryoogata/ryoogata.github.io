---
layout: post
title: "VDC 操作コマンド"
date: 2015-02-06 23:46:44 +0900
comments: true
category: wakame-vdc
tags: ["vdc-manage"]
keywords: wakame-vdc, vdc-manage
published: false

---


VDC 操作コマンド起動
====

```
# /opt/axsh/wakame-vdc/dcmgr/bin/vdc-manage
```

backupobject
====

add
----

```
vdc-manage>> backupobject add \
 --uuid 20150203_2_centos66 \
 --display-name "CentOS 6.6" \
 --storage-id bkst-local \
 --object-key 20150203_2_centos66.img.gz \
 --size              323513867 \
 --allocation-size 10737418240 \
 --container-format gz \
 --checksum d58051e0ee3efbdd5be2095ac2fb664a
```

show
----

```
vdc-manage>> backupobject show

UUID         Account ID  Size       Checksum    Service Type  Name
bo-lucid5d   a-shpoolxx     149084  1f841b195e  std           Ubuntu 10.04 (Lucid Lynx) root partition
bo-centos66  a-shpoolxx  566149917  6cd2353522  std           CentOS 6.6
bo-centos    a-shpoolxx  566149917  6cd2353522  std           CentOS 6.6
```

del
----

```
vdc-manage>> backupobject del bo-centos
```


image
====

add
----

```
vdc-manage>> image add local bo-20150203_2_centos66 \
 --account-id a-shpoolxx \
 --uuid wmi-20150203_2_centos66 \
 --root-device uuid:60dbb3b6-a9d8-4550-82f1-e9acdf40b3f8 \
 --display-name "CentOS 6.6"
```

show 
----

```
vdc-manage>> image show

UUID         Account ID  Service Type  Name                       Boot Type  Arch
wmi-lucid5d  a-shpoolxx  std           Ubuntu 10.04 (Lucid Lynx)  local      x86_64
wmi-centos   a-shpoolxx  std           CentOS 6.6                 local      x86_64
```

show IMAGE_ID
----

```
vdc-manage>> image show wmi-centos

UUID: wmi-centos
Name: CentOS 6.6
Account ID: a-shpoolxx
Boot Type: local
Arch: x86_64
Is Public: false
State: deleted
Service Type: std
Cacheable: false
OS Type: linux
Parent Image ID:
Create: 2015-01-30 15:50:47 UTC
Update: 2015-01-31 13:22:51 UTC
Delete: 2015-01-31 13:22:51 UTC
Features:
{}
Instance Model Name:
```