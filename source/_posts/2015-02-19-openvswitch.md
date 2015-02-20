---
layout: post
title: "openvswitch のインストールと初期セットアップ"
date: 2015-02-19 23:58:33 +0900
comments: true
category: openvswitch
tags: ["network"]
keywords: openvswitch
published: true

---

yum 経由のインストール
====


レポジトリの追加
----

```
# yum install http://rdo.fedorapeople.org/openstack-icehouse/rdo-release-icehouse.rpm

openvswitch x86_64 2.1.2-1.el6 openstack-icehouse 2.2 M
```


パッケージのインストール
----

```
# yum install openvswitch
```

bridge kernel module の削除
----

```
# /sbin/rmmod bridge
```


blacklist.conf への追加
----
kernel module の bridge をロードしてほしくないので、/etc/modprobe.d/blacklist.conf に記述

```
install bridge /bin/false
```

Service の起動
----

```
# service openvswitch start
```

interface の設定
----

### ifcfg-br0 

```
DEVICE=br0
ONBOOT=yes
DEVICETYPE=ovs
TYPE=OVSBridge
BOOTPROTO=static
IPADDR=A.B.C.D
NETMASK=X.Y.Z.0
HOTPLUG=no
```

### ifcfg-eth1 

```
DEVICE=eth1
DEVICETYPE=ovs
HWADDR=40:A8:F0:39:96:A9
TYPE=OVSPort
UUID=22ff31c5-1d59-478d-8fa4-55f2984a94d8
ONBOOT=yes
NM_CONTROLLED=no
BRIDGE=br0
```


Tips
====

bridge kernel module の追加方法
----

```
# insmod /lib/modules/2.6.32-504.el6.x86_64/kernel/net/bridge/bridge.ko
```


bridge kernel module の削除方法
----

```
/sbin/rmmod bridge
```

br0 の作成と eth0 の追加
----

```
# ovs-vsctl add-br br0
# ovs-vsctl add-port br0 eth0
```


Memo
====


yum install http://rdo.fedorapeople.org/rdo-release.rpm   
↑これはダメ。レポジトリ先が存在していない…

yum erase rdo-release で削除


参照 URL
====

* [CentOS 6.4 で openvswitch を使ってみる](http://qunisaki.blog.fc2.com/blog-entry-6.html)
* [Open vSwitchとRyuを使った最も単純なOpenFlowネットワークの構築](http://blog.hirokikana.com/?p=533)
* [Kernel modules (日本語)](https://wiki.archlinux.org/index.php/Kernel_modules_%28%E6%97%A5%E6%9C%AC%E8%AA%9E%29)