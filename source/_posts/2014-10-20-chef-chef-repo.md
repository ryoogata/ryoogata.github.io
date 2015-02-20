---
layout: post
title: "chef-repo をみなおしてみる"
date: 2014-10-20 10:53:18 +0900
comments: true
category: chef
tags: ["chef-repo"]
keywords: chef, chef-repo
published: true

---

概要
====
chef を使い始めてから chef-repo がだいぶ汚れて(?)きたので整理してみる


```
$ git clone git://github.com/opscode/chef-repo.git hosted-chef-repo
$ cd hosted-chef-repo
$ rm -rf .git
$ mkdir .chef
$ mkdir .berkshelf
```

organization の starter-kit を Download

psg-validator.pem と r-ogata.pem を .chef にコピー


knife.rb

```
log_level                :info
log_location             STDOUT
node_name                ''
client_key               ''
validation_client_name   ''
validation_key           ''
chef_server_url          ''
knife[:editor] =         '/usr/bin/vim'
cookbook_path            ['cookbooks', '../../site-cookbooks/']
```

config.json

```
{
  "chef": {
    "node_name": "",
    "client_key": "",
    "validation_client_name": "",
    "validation_key_path": "",
    "chef_server_url": ""
  },
  "ssl":{
    "verify": false
  }
}
```

https://manage.opscode.com/login

https://manage.opscode.com/starter-kit


参照 URL
====

* [Create the chef-repo](https://docs.getchef.com/essentials_repository_create.html)

