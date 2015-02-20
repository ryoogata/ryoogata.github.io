---
layout: post
title: "wakame-vdc ( OpenVZ 版 HVA ) インストールメモ"
date: 2015-02-07 23:05:21 +0900
comments: true
category: wakame-vdc
tags: []
keywords: wakame-vdc
published: true

---

目的
====
wakame-vdc ( OpenVZ 版 HVA ) をインストールする際の手順をまとめてみる


yum repository の準備
====
wakame-vdc インストールするために必要なレポジトリの登録

```
# curl -o /etc/yum.repos.d/wakame-vdc.repo -R https://raw.githubusercontent.com/axsh/wakame-vdc/master/rpmbuild/wakame-vdc.repo
# yum install -y epel-release
```


DCMGR のインストール
====


パッケージのインストール
----

```
# yum install -y wakame-vdc-dcmgr-vmapp-config
```

設定ファイルのコピー
----

```
# cp /opt/axsh/wakame-vdc/dcmgr/config/dcmgr.conf.example /etc/wakame-vdc/dcmgr.conf

```

MySQL の起動
----
```
# service mysqld start
```

DB の作成
----

```
# mysqladmin -uroot create wakame_dcmgr

```

DB Table の作成
----

```
# cd /opt/axsh/wakame-vdc/dcmgr
# /opt/axsh/wakame-vdc/ruby/bin/rake db:up

```


OpenVZ 版 HVA のインストール
====

前提条件
----
* VM は 192.168.3.0/24 の IP Address レンジに作成される
* HVA の物理ホスト eth0 に 192.168.3.100 が設定されている

yum repository の準備
----
OpenVZ 版 HVA をインストールするために必要なレポジトリの登録

```
# curl -o /etc/yum.repos.d/openvz.repo -R https://raw.githubusercontent.com/axsh/wakame-vdc/master/rpmbuild/openvz.repo
```

パッケージのインストール
----

```
# yum install -y wakame-vdc-hva-openvz-vmapp-config
```

設定ファイルのコピー
----

```
# cp /opt/axsh/wakame-vdc/dcmgr/config/hva.conf.example /etc/wakame-vdc/hva.conf

```

bridge network の設定
----

### bridge Interface の作成

* /etc/sysconfig/network-scripts/ifcfg-br0

```
DEVICE=br0
TYPE=Bridge
BOOTPROTO=static
ONBOOT=yes
NM_CONTROLLED=no
IPADDR=192.168.3.100
NETMASK=255.255.255.0
GATEWAY=192.168.3.1
DNS1=8.8.8.8
DELAY=0
```

* /etc/sysconfig/network-scripts/ifcfg-eth0

```
DEVICE="eth0"
ONBOOT="yes"
BRIDGE=br0
NM_CONTROLLED=no
```

設定後、ネットワークの再起動

```
# service network restart
```

NODE_ID の設定
----
HVA を一意に識別するために、NODE_ID の設定をする


* /etc/default/vdc-hva

```
NODE_ID=demo1
```

※ Default だとコメントアウトされている


HVA を DCMGR に登録する
----


```
/opt/axsh/wakame-vdc/dcmgr/bin/vdc-manage host add hva.demo1 \
   --uuid hn-demo1 \
   --display-name "demo HVA 1" \
   --cpu-cores 100 \
   --memory-size 10240 \
   --hypervisor openvz \
   --arch x86_64 \
   --disk-space 102400 \
   --force
```

Network 関連パラメータを DCMGR に登録する
----

### VDC 操作コマンド起動
```
# /opt/axsh/wakame-vdc/dcmgr/bin/vdc-manage
```

### Network レンジの登録
`nw-demo1` という Network レンジを作成する 

```
vdc-manage>> network add \
  --uuid nw-demo1 \
  --ipv4-network 192.168.3.0 \
  --prefix 24 \
  --ipv4-gw 192.168.3.1 \
  --dns 8.8.8.8 \
  --account-id a-shpoolxx \
  --display-name "demo network"
``` 


### DHCP レンジの登録
`nw-demo1` の DHCP で割り当てる IP アドレスのレンジを設定

```
vdc-manage>> network dhcp addrange nw-demo1 192.168.3.1 192.168.3.254

```

### DHCP 割り当て除外 IP の登録
`nw-demo1` の DHCP で割り当てる IP アドレスのレンジ内の割り当て除外 IP の登録

```
vdc-manage>> network reserve nw-demo1 --ipv4 192.168.3.100
```

### VM 用 MAC Address レンジの登録

```
vdc-manage>> macrange add 525400 1 ffffff --uuid mr-demomacs
```

### dc_network の作成と Network レンジとの紐付け
`public` という名称の　dc_network　を作成し、`nw-demo1` と紐付ける

```
vdc-manage>> network dc add public --uuid dcn-public --description "the network instances are started in"
vdc-manage>> network dc add-network-mode public securitygroup
vdc-manage>> network forward nw-demo1 public
```

### 作成した dc_network と物理/Bridge Interface が紐付いているか確認

* /etc/wakame-vdc/hva.conf

```
dc_network('public') {
  bridge_type 'linux'
  interface 'eth0'
  bridge 'br0'
}
```




OS の再起動
----
OpenVZ 用カスタムカーネルが動作するように OS を再起動する


WebUI のインストール
====


パッケージのインストール
----

```
# yum install -y wakame-vdc-webui-vmapp-config
```

設定ファイルのコピー
----

```
# cp /opt/axsh/wakame-vdc/frontend/dcmgr_gui/config/database.yml.example /etc/wakame-vdc/dcmgr_gui/database.yml

# cp /opt/axsh/wakame-vdc/frontend/dcmgr_gui/config/dcmgr_gui.yml.example /etc/wakame-vdc/dcmgr_gui/dcmgr_gui.yml

# cp /opt/axsh/wakame-vdc/frontend/dcmgr_gui/config/instance_spec.yml.example /etc/wakame-vdc/dcmgr_gui/instance_spec.yml

# cp /opt/axsh/wakame-vdc/frontend/dcmgr_gui/config/load_balancer_spec.yml.example /etc/wakame-vdc/dcmgr_gui/load_balancer_spec.yml
```

DB の作成
----

```
# mysqladmin -uroot create wakame_dcmgr_gui
```


DB Table の作成
----

```
# cd /opt/axsh/wakame-vdc/frontend/dcmgr_gui/
# /opt/axsh/wakame-vdc/ruby/bin/rake db:init
```

システム用アカウントの作成
----

### GUI マネージャ操作コマンド起動

```
# /opt/axsh/wakame-vdc/frontend/dcmgr_gui/bin/gui-manage

```

### default アカウントの追加

```
gui-manage>> account add --name default --uuid a-shpoolxx
```

新規ユーザの追加
----

下記のユーザ名/パスワードのユーザを登録

* ユーザ名: demo
* パスワード: demo

### 新規ユーザの登録

```
gui-manage>> user add --name "demo user" --uuid u-demo --password demo --login-id demo
```

### 追加ユーザとアカウントを紐づける

```
gui-manage>> user associate u-demo --account-ids a-shpoolxx
```

デモ用マシンイメージの登録
====

* デモ用マシンイメージを利用する
  *  下記のコマンドを実行して事前に Download しておく
  
   ```
   # mkdir -p /var/lib/wakame-vdc/images
   # cd /var/lib/wakame-vdc/images
   # curl -O http://dlc.wakame.axsh.jp.s3.amazonaws.com/demo/vmimage/ubuntu-lucid-kvm-md-32.raw.gz
   ```
  * デモ用マシンイメージの md5 sum はメモしておく
  
  ```
  # md5sum ubuntu-lucid-kvm-md-32.raw.gz
  ```

VDC 操作コマンド起動
----

```
# /opt/axsh/wakame-vdc/dcmgr/bin/vdc-manage
```


backupstorage の作成
----
マシンイメージを保存する場所としてローカルのストレージを登録する

```
vdc-manage>> backupstorage add \
  --uuid bkst-local \
  --display-name "local storage" \
  --base-uri "file:///var/lib/wakame-vdc/images/" \
  --storage-type local \
  --description "storage on the local filesystem"
```

マシンイメージをローカルのバックアップストレージに登録
----

```
vdc-manage>> backupobject add \
  --uuid bo-lucid5d \
  --display-name "Ubuntu 10.04 (Lucid Lynx) root partition" \
  --storage-id bkst-local \
  --object-key ubuntu-lucid-kvm-md-32.raw.gz \
  --size 149084 \
  --allocation-size 359940 \
  --container-format gz \
  --checksum 1f841b195e0fdfd4342709f77325ce29
``` 

マシンイメージをシステムに登録
----

```
vdc-manage>> image add local bo-lucid5d \
  --account-id a-shpoolxx \
  --uuid wmi-lucid5d \
  --root-device uuid:148bc5df-3fc5-4e93-8a16-7328907cb1c0 \
  --display-name "Ubuntu 10.04 (Lucid Lynx)"
```

Wakame-vdc の起動
====


RabbitMQ Server の起動
----

```
# service rabbitmq-server start
```

Wakame-vdc 各コンポーネントの起動
----

```
# start vdc-dcmgr
# start vdc-collector
# start vdc-hva
# start vdc-webui
```

Web UI へのアクセス
====

下記の URL にアクセス

* URL: http://192.168.3.100:9000

下記のユーザ名/パスワードのユーザを登録

* ユーザ名: demo
* パスワード: demo


