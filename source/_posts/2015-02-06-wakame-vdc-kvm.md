---
layout: post
title: "KVM VM に OS をインストール"
date: 2015-02-06 23:02:07 +0900
comments: true
category: kvm
tags: ["kvm"]
keywords: wakame-vdc, kvm
published: true

---

概要
====

KVM VM に OS をインストールする手法のまとめ


CentOS 6.6 の場合
====
  
仮想HDDイメージファイルの作成
----


```
# dd if=/dev/zero of=/var/lib/libvirt/images/centos66.img bs=1M count=10240
```

インストール開始
----

VNC経由もしくはテキストモードにてインストールを実施する


### VNC 経由の場合

VNC 経由で作業を実施するために、```virt-viewr``` を事前にインストールしておく


```
# yum install virt-viewer
```

下記のコマンドを実行し、インストールを実施

```
# virt-install \
 --virt-type=kvm \
 --hvm \
 --connect qemu:///system \
 --vcpus 1 \
 --ram=1024 \
 --os-type=linux \
 --os-variant=rhel6 \
 --network bridge=br0 \
 --vnc --vncport=29741 --vnclisten=0.0.0.0 \
 --name centos66 \
 --disk=/var/lib/libvirt/images/centos66.img \
 --location='/var/lib/libvirt/images/CentOS-6.6-x86_64-bin-DVD1.iso' \
 --accelerate
```

### テキストモードの場合

テキストモードインストールでは、GUIインストールとは異なり、パーティション等の編集メニューが出てこない。
インストール時に指定したい場合には、キックスタートを利用してインストールすればよい。


```
# virt-install \
 --virt-type=kvm \
 --hvm \
 --connect qemu:///system \
 --vcpus 1 \
 --ram=1024 \
 --os-type=linux \
 --os-variant=rhel6 \
 --network bridge=br0 \
 --nographics \
 --extra-args='console=tty0 console=ttyS0, 115200n8' \
 --name centos66 \
 --disk=/var/lib/libvirt/images/centos.img \
 --location='/var/lib/libvirt/images/CentOS-6.6-x86_64-bin-DVD1.iso' \
 --accelerate \
```


ドメインの起動
----


```
# virsh start centos66
```

ubuntu-14.04 の場合
====
作業途中で失敗する...


仮想HDDイメージファイルの作成
----

```
# dd if=/dev/zero of=/var/lib/libvirt/images/ubuntu.img bs=1M count=10240
```

ISO イメージをループバックマウント
----

```
# mount --read-only --options loop ubuntu-14.04.1-server-amd64.iso ISO
```

インストール開始
----

### VNC 経由の場合

```
# virt-install \
 --name=TestMachine \
 --ram=2048 \
 --vcpus=1 \
 --os-variant ubuntuprecise \
 --hvm \
 --connect qemu:///system \
 --virt-type=kvm \
 --disk=/var/lib/libvirt/images/ubuntu.img,format=qcow2 \
 --network=bridge:br0 \
 --keymap=ja \
 --location /var/lib/libvirt/images/ISO/ \
 --serial pty \
 --extra-args=console=ttyS0
```

Tips
====

SSH PortFoward
----

```
# ssh -L 29741:localhost:29741 root@172.16.1.108 
```


gzip 圧縮 ( 元ファイル残す )
----

```
# gzip -c centos66.img > 20150202_2_centos66.img.gz
```


参照 URL
====

* [CentOS/パーティションの UUID を確認・変更する方法](http://www.maruko2.com/mw/CentOS/%E3%83%91%E3%83%BC%E3%83%86%E3%82%A3%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE_UUID_%E3%82%92%E7%A2%BA%E8%AA%8D%E3%83%BB%E5%A4%89%E6%9B%B4%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95)
* [Wakame-VDC のマシンイメージを作ってみる](http://blog.osamu.habuka.jp/blog/2014/12/03/try-to-make-wakame-vdcs-image/)
* [KVMにおけるゲストOSのバックアップ/リストア ](http://www.oss-d.net/virt/kvm/backup)
* [Custom images](https://github.com/axsh/wakame-vdc/wiki/Custom-images)
* [CentOS6.4(minimal) KVMによる仮想環境構築(テキストモード)](http://blog.livedoor.jp/hide_system/archives/51888446.html)
* [KVM ゲスト OS としてコンソールから Ubuntu をインストール ](http://fishrimper.blogspot.jp/2014/01/kvm-os-ubuntu.html)