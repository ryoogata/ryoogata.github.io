---
layout: post
title: "Vagrant と Hosted Chef との連携"
date: 2014-10-20 15:35:34 +0900
comments: true
category: vagrant
tags: ["chef-server", "hosted chef"]
keywords: vagrant, chef-server
published: true

---

概要
====
Vagrant の Provisioner に Chef Client を利用し、Chef Server に Hosted Chef を利用する


Vagrantfile
----

```
config.vm.provision "chef_client" do |chef|
  chef.chef_server_url = "https://api.opscode.com/organizations/tech-sales"
  chef.validation_client_name = "tech-sales-validator"
  chef.validation_key_path = "../../hosted-chef-repo/.chef/tech-sales-validator.pem"
  chef.environment = "setup"
  chef.node_name = "chef-test"
  chef.delete_node = true
  chef.delete_client = true
end
```

* chef_server_url
  * Organizations 毎に変わる

* validation_client_name
  * Hosted Chef Server に Node を登録する際に使用するユーザ名
  * Hosted Chef の Organization の Client で、`Validation Client` として利用する Client 名を指定する
    * default の `chef-validator` ではダメ