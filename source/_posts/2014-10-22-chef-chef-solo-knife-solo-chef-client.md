---
layout: post
title: "chef の登場人物をさらっとまとめてみる"
date: 2014-10-22 13:57:56 +0900
comments: true
category: chef
tags: ["chef-solo", "knife-solo", "chef-client"]
keywords: chef, chef-solo, knife-solo, chef-client
published: true

---

概要
====
chef-solo とか knife-solo とか似たような chef 用語が色々とあるので、整理をかねて簡単にまとめてみる


chef-solo
----
chef-solo とは?

* chef-server を利用しない
* 単一の node で完結
* chef-repo を node 自身に保持


knife-solo
----

knife-solo とは?

* 下記の作業を自動化してくれるツール
  * 手元の cookbook をリモート node に転送
  * リモート node 上で chef-solo コマンド実行


chef-client
----
chef-client とは?

* chef-server と連携して動作
* Node の収束を実施
* Node の情報収集
* etc


### chef-client ローカルモード
chef-client ローカルモードとは?

* Chef 11.x から追加されたモード
* Chef-Client/Server環境の簡易版
* chef-solo の代わり