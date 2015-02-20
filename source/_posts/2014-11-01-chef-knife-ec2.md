---
layout: post
title: "knife-ec2 を利用したインスタンス操作"
date: 2014-11-01 22:09:37 +0900
comments: true
category: chef
tags: ["knife-ec2"]
keywords: chef, knife-ec2
published: true

---

概要
====
chef-server との連携を前提とした knife ec2 のオペレーションのまとめ



knife ec2 オペレーション
====

インスタンスの作成
----

`$ knife ec2 server create --node-name chef_test --ssh-user ec2-user --security-group-ids sg-6744b502`

* ec2 インスタンスの作成
  * --node-name
  	  * Chef-Server に登録する際の Node の名前
  * --ssh-user
      * SSH ログインユーザ名
  * --security-group-ids
      * VPC セキュリティグループ      
* chef client のインストール
* `chef server への node の追加`

```
$ knife ec2 server create --node-name chef_test --ssh-user ec2-user --security-group-ids sg-6744b502
```


インスタンスの削除
----

`$ knife ec2 server delete i-xxxxxxxx --purge`

* インスタンスの削除
* chef server 上に登録された Node の情報の削除
* chef server 上に登録された Client の情報の削除

```
$ knife ec2 server delete i-5effb8ac --purge
```

参照 URL
====

* [opscode/knife-ec2](https://github.com/opscode/knife-ec2)


