---
layout: post
title: "knife-ec2 や kitchen-ec2 のための環境変数設定"
date: 2014-11-02 22:37:31 +0900
comments: true
category: chef
tags: ["knife-ec2", "kitchen-ec2"]
keywords: chef, knife-ec2
published: true

---

概要
====
test-kitchen の dirver に ec2 を利用する際や、knife ec2 コマンド、AWS CLI を利用する際に環境変数から設定値を読み込む設定等のまとめ。

git で管理している設定ファイルに直接 AWS の Access Key や Secret Access Key などを記述しなくても済む方法。


.bash_profile
====
knife-ec2 および kitchen-ec2 の環境変数関連の挙動から、`.bash_profile` に設定する環境変数。

* .bash_profile

```
export EC2_SSH_KEY_PATH=~/.ssh/********.pem
export AWS_ACCESS_KEY_ID=********************
export AWS_SECRET_ACCESS_KEY=********************
export AWS_SSH_KEY_ID=********************
```


knife-ec2
====


knife.rb に環境変数から設定値を読み込む設定を行っている項目
----

```
knife[:identity_file] = ENV['EC2_SSH_KEY_PATH']
knife[:aws_access_key_id] = ENV['AWS_ACCESS_KEY_ID']
knife[:aws_secret_access_key] = ENV['AWS_SECRET_ACCESS_KEY']
knife[:aws_ssh_key_id] = ENV['AWS_SSH_KEY_ID']
```

kitchen-ec2
===

.kitchen.yml に設定項目を記述しなくても、環境変数に特定の値が設定されている場合には自動的に設定値を設定してくれる項目

設定項目|設定のための環境変数
---|---
aws_access_key_id|`AWS_ACCESS_KEY` or `AWS_ACCESS_KEY_ID`
aws_secret_access_key|`AWS_SECRET_KEY` or `AWS_SECRET_ACCESS_KEY`
aws_session_token|`AWS_SESSION_TOKEN` or `AWS_TOKEN`
aws_ssh_key_id|`AWS_SSH_KEY_ID`


.kitchen.yml に環境変数から設定値を読み込む設定を行っている項目
----

* EC2_SSH_KEY_PATH


```
driver:
  name: ec2
  security_group_ids: ["sg-6744b502"]
  ssh_key: <%= ENV['EC2_SSH_KEY_PATH'] %>
  region: ap-northeast-1
  availability_zone: ap-northeast-1a
  interface: public
```

.aws/config
====

* 環境変数から設定値を読み込む
  * AWS_ACCESS_KEY_ID
  * AWS_SECRET_ACCESS_KEY




Tips
====

knife.rb に設定可能な項目 List
----

設定項目|default 設定値
---|---
knife[:aws_credential_file]|-
knife[:aws_access_key_id]|-
knife[:aws_secret_access_key]|-
knife[:region]|-
knife[:use_iam_profile]|false
knife[:flavor]|-
knife[:image]|-
knife[:placement_group]|-
knife[:availability_zone]|-
knife[:chef_node_name]|-
knife[:aws_ssh_key_id]|-
knife[:ssh_port]|22
knife[:ssh_gateway]|-
knife[:ssh_gateway_identity]|-
knife[:bootstrap_version]|-
knife[:bootstrap_proxy]|-
knife[:distro]|chef-full
knife[:template_file]|false
knife[:secret]|-
knife[:secret_file]|-
knife[:s3_secret]|-
knife[:subnet_id]|-
knife[:private_ip_address]|-
knife[:bootstrap_protocol]|-
knife[:fqdn]|-
knife[:aws_user_data]|-
knife[:ebs_volume_type]|standard
knife[:provisioned_iops]|-
