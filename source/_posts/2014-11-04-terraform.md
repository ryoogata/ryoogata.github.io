---
layout: post
title: "terraform"
date: 2014-11-04 22:54:40 +0900
comments: true
category: terraform
tags: []
keywords: terraform
published: false

---

Source ( Binary ) の Download
----
[Terraform Download URL](https://terraform.io/downloads.html)


展開
----
適当な Directory に Download した Source を展開

コンパイルとか、インストールは不要


設定
----
展開した Directory に設定用のファイルを設置

aws.tf

```
provider "aws" {
  access_key = "*********"
  secret_key = "*********"
  region = "ap-northeast-1"
}
```



```
$ ./terraform plan
Refreshing Terraform state prior to plan...


The Terraform execution plan has been generated and is shown below.
Resources are shown in alphabetical order for quick scanning. Green resources
will be created (or destroyed and then created if an existing resource
exists), yellow resources are being changed in-place, and red resources
will be destroyed.

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.

+ aws_security_group.allow_all
    description:                 "" => "allow tcp 22 and 80"
    ingress.#:                   "" => "2"
    ingress.0.cidr_blocks.#:     "" => "1"
    ingress.0.cidr_blocks.0:     "" => "0.0.0.0/0"
    ingress.0.from_port:         "" => "22"
    ingress.0.protocol:          "" => "tcp"
    ingress.0.security_groups.#: "" => "0"
    ingress.0.self:              "" => "0"
    ingress.0.to_port:           "" => "22"
    ingress.1.cidr_blocks.#:     "" => "1"
    ingress.1.cidr_blocks.0:     "" => "0.0.0.0/0"
    ingress.1.from_port:         "" => "80"
    ingress.1.protocol:          "" => "tcp"
    ingress.1.security_groups.#: "" => "0"
    ingress.1.self:              "" => "0"
    ingress.1.to_port:           "" => "80"
    name:                        "" => "allow_all"
    owner_id:                    "" => "<computed>"
    vpc_id:                      "" => "vpc-73058b1b"
```


```
MacBook-Air:terraform_0.3.1_darwin_amd64 ryo$ ./terraform apply
aws_security_group.allow_all: Creating...
  description:                 "" => "allow tcp 22 and 80"
  ingress.#:                   "" => "2"
  ingress.0.cidr_blocks.#:     "" => "1"
  ingress.0.cidr_blocks.0:     "" => "0.0.0.0/0"
  ingress.0.from_port:         "" => "22"
  ingress.0.protocol:          "" => "tcp"
  ingress.0.security_groups.#: "" => "0"
  ingress.0.self:              "" => "0"
  ingress.0.to_port:           "" => "22"
  ingress.1.cidr_blocks.#:     "" => "1"
  ingress.1.cidr_blocks.0:     "" => "0.0.0.0/0"
  ingress.1.from_port:         "" => "80"
  ingress.1.protocol:          "" => "tcp"
  ingress.1.security_groups.#: "" => "0"
  ingress.1.self:              "" => "0"
  ingress.1.to_port:           "" => "80"
  name:                        "" => "allow_all"
  owner_id:                    "" => "<computed>"
  vpc_id:                      "" => "vpc-73058b1b"
aws_security_group.allow_all: Creation complete

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

The state of your infrastructure has been saved to the path
below. This state is required to modify and destroy your
infrastructure, so keep it safe. To inspect the complete state
use the `terraform show` command.

State path: terraform.tfstate
```



```
$ ./terraform show ./terraform.tfstate 
aws_security_group.allow_all:
  id = sg-a507d1c0
  description = allow tcp 22 and 80
  ingress.# = 2
  ingress.0.cidr_blocks.# = 1
  ingress.0.cidr_blocks.0 = 0.0.0.0/0
  ingress.0.from_port = 22
  ingress.0.protocol = tcp
  ingress.0.security_groups.# = 0
  ingress.0.self = false
  ingress.0.to_port = 22
  ingress.1.cidr_blocks.# = 1
  ingress.1.cidr_blocks.0 = 0.0.0.0/0
  ingress.1.from_port = 80
  ingress.1.protocol = tcp
  ingress.1.security_groups.# = 0
  ingress.1.self = false
  ingress.1.to_port = 80
  name = allow_all
  vpc_id = vpc-73058b1b
```

既存の Security Group を Terafform で管理する
====


```
MacBook-Air:terraform_0.3.1_darwin_amd64 ryo$ cat terraform.tfstate
{
    "version": 1,
    "serial": 9,
    "modules": [
        {
            "path": [
                "root"
            ],
            "outputs": {},
            "resources": {
                "aws_security_group.allow_all": {
                    "type": "aws_security_group",
                    "primary": {
                        "id": "sg-6204d207",
                        "attributes": {
                            "description": "allow tcp 22 and 80",
                            "id": "sg-6204d207",
                            "ingress.#": "2",
                            "ingress.0.cidr_blocks.#": "1",
                            "ingress.0.cidr_blocks.0": "0.0.0.0/0",
                            "ingress.0.from_port": "22",
                            "ingress.0.protocol": "tcp",
                            "ingress.0.security_groups.#": "0",
                            "ingress.0.self": "false",
                            "ingress.0.to_port": "22",
                            "ingress.1.cidr_blocks.#": "1",
                            "ingress.1.cidr_blocks.0": "0.0.0.0/0",
                            "ingress.1.from_port": "80",
                            "ingress.1.protocol": "tcp",
                            "ingress.1.security_groups.#": "0",
                            "ingress.1.self": "false",
                            "ingress.1.to_port": "80",
                            "name": "allow_all",
                            "owner_id": "611856513750",
                            "tags.Name": "allow_all",
                            "vpc_id": "vpc-73058b1b"
                        }
                    }
                }
            }
        }
    ]
}
```

terraform.tfstate を既存の Security Group を同じように作成する

terraform plan をして、差分がないところまで作り込む

security group 名を既存のものとかぶらないように .tf ファイルを作るのもありかと
