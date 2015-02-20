---
layout: post
title: "OSS chef-server から Hosted Chef への移行"
date: 2014-10-22 22:25:35 +0900
comments: true
category: chef
tags: ["chef-repo", "chef-server"]
keywords: chef, chef-repo, chef-server
published: false

---

概要
====



chef-repo の git 管理がいい加減だったパターン
====

```
$ mkdir chef-repo
$ cd chef-repo
$ git clone git://github.com/opscode/chef-repo.git tech-sales
$ cd tech-sales
$ mkdir .chef
$ cp ~/Dropbox/chef-repo/.chef/knife.rb .

$ knife download environments
$ knife download data_bags
$ knife download roles
$ knife upload cookbooks


knife.rb 編集

node_name                'r-ogata'
client_key               'r-ogata.cer'
validation_client_name   'tech-sales-validator'
validation_key           'tech-sales-validator.cer'
chef_server_url          'https://api.opscode.com/organizations/tech-sales'

$ knife upload environments
$ knife upload data_bags
$ knife upload roles
$ knife upload cookbooks

```


トラブルシュート
====


cookbook upload の際に Bad Request のエラーが発生
----

```
ERROR: cookbooks failed to write: HTTP error writing: 400 "Bad Request" cause: {"error":["Invalid value '[]' for metadata.platforms"]}
```
