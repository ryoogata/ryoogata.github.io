---
layout: post
title: "Wakame 用 カスタム KVM イメージ作成"
date: 2015-02-19 23:49:08 +0900
comments: true
category: wakame-vdc
tags: ["kvm"]
keywords: wakame-vdc
published: true

---


作成手順
====


yum update の実行
----

```
# yum update -y
```


selinux 設定
----

selinux Permissive



必要なパッケージのインストール
----

```
yum install -y telnet tcpdump wget man man-pages ntpdate bind-utils openssh-clients
```


ipv6 disable 設定
----

```
# chkconfig ip6tables off
```

/etc/sysconfig/network に NETWORKING_IPV6=no を追加

```
# echo 'options ipv6 disable=1' > /etc/modprobe.d/disable-ipv6.conf
```

ssh 設定
----

/etc/ssh/sshd_config
Port 2442


wakame-init の Download
----

```
# cd /etc
# wget https://raw.githubusercontent.com/axsh/wakame-vdc/master/wakame-init/rhel/6/wakame-init
```

rc.local の編集
----

/etc/rc.local に設定追加



root-device の UUID の確認
----

```
# blkid
   :
/dev/vda3: UUID="893013b4-bf1e-47b6-baf3-495193b76238" TYPE="ext4" 
```


不要ファイル削除
----

```
rm -rf /root/.bash_history
rm -rf /etc/ssh/ssh_host*
rm -rf /etc/udev/rules.d/70-persistent-net.rules
```

OS イメージの圧縮と移動
----
OS イメージ圧縮前のファイルサイズを控えておく

```
# ls -al centos66.img
```

/var/lib/libvirt/images 


圧縮の実行

```
# gzip -c centos66.img > /var/lib/wakame-vdc/images/20150203_1_centos66.img.gz
```

OS イメージ圧縮後のファイルサイズを控えておく

```
# ls -al 20150203_1_centos66.img.gz
```


OS イメージの md5 の取得
----
OS イメージの md5 checksum 値を控えておく

```
# md5sum 20150203_2_centos66.img.gz
```

vdc へイメージの登録
----
vdc-manage コマンドを実行

```
# /opt/axsh/wakame-vdc/dcmgr/bin/vdc-manage
```

事前に控えていた下記の情報を基にカスタムイメージを DB に登録

* 圧縮前 OS イメージファイルサイズ
* 圧縮後 OS イメージファイルサイズ
* OS イメージの md5 checksum
* root パーティションの UUID


```
backupobject add \
 --uuid 20150213_1_cent_base \
 --display-name "CentOS 6.6" \
 --storage-id bkst-local \
 --object-key 20150213_1_cent_base.img.gz \
 --size              492162200 \
 --allocation-size 17179869184 \
 --container-format gz \
 --checksum 9f4089bd93fcb48a90a0a22b0d0be04d

image add local bo-20150213_1_cent_base \
 --account-id a-shpoolxx \
 --uuid wmi-20150213_1_cent_base \
 --root-device uuid:893013b4-bf1e-47b6-baf3-495193b76238 \
 --display-name "CentOS 6.6"
```


参照 URL
====

* [Custom images](https://github.com/axsh/wakame-vdc/wiki/Custom-images)