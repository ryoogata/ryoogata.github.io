---
layout: post
title: "chef 関連の Directory 構成を考える"
date: 2014-10-22 22:05:02 +0900
comments: true
category: chef
tags: ["chef-repo"]
keywords: chef, chef-repo
published: true

---

概要
====



やりたいこと
====

* 1 台の PC で複数の Workstation を扱いたい
* chef-server と chef-solo を併用したい
* chef-solo から将来的に chef-server へ移行する際の面倒を避けたい


Directory 全体像
====
```
.
|-- berkshelf
|-- site-cookbooks
|-- workstation
```

site-cookbooks
====

* 自作 cookbook は同 Directory 以下に作成する
* 各 chef-repo 以下には site-cookbooks を作成しない


workstations
====

* workstations directory 以下に組織毎に chef-repo 用 directory を作成する  
* 組織は hosted chef の Organization と紐付けすることを想定
* chef-server/chef-solo どちらで利用するかに関係なく workstations directory 以下に作成していく


```
.
`-- workstations
    |-- Organization_A
    |   |-- .chef
    |   |-- cookbooks
    |   |-- data_bags
    |   |-- environments
    |     :     
    |     :     
    |   `-- roles
    |-- Organization_B
      :
      :
    `-- Organization_Z
        |-- .chef
        |-- cookbooks
        |-- data_bags
        |-- environments
          :     
        `-- roles

```

.chef/knife.rb
====

```
log_level                :info
log_location             STDOUT
node_name                'r-ogata'
client_key               'r-ogata.cer'
validation_client_name   'tech-sales-validator'
validation_key           'tech-sales-validator.cer'
chef_server_url          'https://api.opscode.com/organizations/tech-sales'
knife[:editor] =         '/usr/bin/vim'
cookbook_path            ['cookbooks', '../../site-cookbooks']
```



