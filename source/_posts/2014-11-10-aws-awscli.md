---
layout: post
title: "aws awscli"
date: 2014-11-10 16:59:46 +0900
comments: true
category: aws
tags: ["awscli"]
keywords: aws, awscli
published: false

---

概要
====


設定ファイル
----

* ~/.aws/config

```
[default]
output = text
region = ap-northeast-1
```

Access/Secret key は環境変数から取得

* AWS_ACCESS_KEY_ID
* AWS_SECRET_ACCESS_KEY


AWS CLI のアップグレード
----

```
$ sudo pip install awscli --upgrade
```


インスタンスの起動
----


```
$ aws ec2 run-instances --image-id ami-29dc9228 --instance-type t2.micro --key-name r-
ogata-AWS-AP-Tokyo --security-group-ids sg-6744b502
611856513750	r-4301b65a
INSTANCES	0	x86_64	None	False	xen	ami-29dc9228	i-5d034caf	t2.micro	r-ogata-AWS-AP-Tokyo	2014-11-10T08:29:39.000Z	ip-172-31-18-5.ap-northeast-1.compute.internal	172.31.18.None	/dev/xvda	ebs	True	None	subnet-74058b1c	hvm	vpc-73058b1b
MONITORING	disabled
NETWORKINTERFACES	None	0a:f1:7c:da:d6:5a	eni-c0db6499	611856513750	ip-172-31-18-5.ap-northeast-1.compute.internal	172.31.18.5	True	in-use	subnet-74058b1c	vpc-73058b1b
ATTACHMENT	2014-11-10T08:29:39.000Z	eni-attach-122c0e14	True	0	attaching
GROUPS	sg-6744b502	default-web
PRIVATEIPADDRESSES	True	ip-172-31-18-5.ap-northeast-1.compute.internal	172.31.18.5
PLACEMENT	ap-northeast-1c	None	default
SECURITYGROUPS	sg-6744b502	default-web
STATE	0	pending
STATEREASON	pending	pending
```