---
layout: post
title: "chef knife-container"
date: 2014-11-06 22:27:06 +0900
comments: true
category: chef
tags: ["knife-container"]
keywords: chef, knife-container
published: false

---

```
$ gem install knife-container
Fetching: knife-container-0.2.4.gem (100%)
Successfully installed knife-container-0.2.4
1 gem installed

$ gem install chef-container
Fetching: chef-container-0.1.1.gem (100%)
Successfully installed chef-container-0.1.1
1 gem installed
```
  
```
$ mkdir chef-container
$ mkdir chef-container/.chef
```

```
log_level                :info
log_location             STDOUT
# node_name                'r-ogata'
# client_key               'r-ogata.cer'
# validation_client_name   'tech-sales-validator'
# validation_key           'tech-sales-validator.cer'
# chef_server_url          'https://api.opscode.com/organizations/tech-sales'

# optional
knife[:editor] =         '/usr/bin/vim'

cookbook_path ['../../site-cookbooks/']
```


```
$ knife container docker init chef/ubuntu-12.04 -r 'recipe[apache2]' -z -b

Compiling Cookbooks...
Recipe: knife_container::docker_init
  * directory[./dockerfiles/chef/ubuntu-12.04] action create
    - create new directory ./dockerfiles/chef/ubuntu-12.04
  * template[./dockerfiles/chef/ubuntu-12.04/Dockerfile] action create
    - create new file ./dockerfiles/chef/ubuntu-12.04/Dockerfile
    - update content in file ./dockerfiles/chef/ubuntu-12.04/Dockerfile from none to 57c377
    (diff output suppressed by config)
  * template[./dockerfiles/chef/ubuntu-12.04/.dockerignore] action create
    - create new file ./dockerfiles/chef/ubuntu-12.04/.dockerignore
    - update content in file ./dockerfiles/chef/ubuntu-12.04/.dockerignore from none to e3b0c4
    (diff output suppressed by config)
  * directory[./dockerfiles/chef/ubuntu-12.04/chef] action create
    - create new directory ./dockerfiles/chef/ubuntu-12.04/chef
  * template[./dockerfiles/chef/ubuntu-12.04/chef/zero.rb] action create
    - create new file ./dockerfiles/chef/ubuntu-12.04/chef/zero.rb
    - update content in file ./dockerfiles/chef/ubuntu-12.04/chef/zero.rb from none to cde131
    (diff output suppressed by config)
  * file[./dockerfiles/chef/ubuntu-12.04/chef/first-boot.json] action create
    - create new file ./dockerfiles/chef/ubuntu-12.04/chef/first-boot.json
    - update content in file ./dockerfiles/chef/ubuntu-12.04/chef/first-boot.json from none to 1b9651
    (diff output suppressed by config)
  * template[./dockerfiles/chef/ubuntu-12.04/chef/.node_name] action create
    - create new file ./dockerfiles/chef/ubuntu-12.04/chef/.node_name
    - update content in file ./dockerfiles/chef/ubuntu-12.04/chef/.node_name from none to 26ac58
    (diff output suppressed by config)
  * template[./dockerfiles/chef/ubuntu-12.04/Berksfile] action create
    - create new file ./dockerfiles/chef/ubuntu-12.04/Berksfile
    - update content in file ./dockerfiles/chef/ubuntu-12.04/Berksfile from none to 2669a5
    (diff output suppressed by config)
  * log[Could not find a '/Users/ryo/site-cookbooks' directory in your chef-repo.] action write
  
  * execute[cp -r ./roles/ ./dockerfiles/chef/ubuntu-12.04/chef/roles/] action run (skipped due to not_if)
  * execute[cp -r ./environments/ ./dockerfiles/chef/ubuntu-12.04/chef/environments/] action run (skipped due to not_if)
  * execute[cp -r ./nodes/ ./dockerfiles/chef/ubuntu-12.04/chef/nodes/] action run (skipped due to not_if)
Downloading base image: chef/ubuntu-12.04:latest. This process may take awhile...
Tagging base image chef/ubuntu-12.04 as chef/ubuntu-12.04

Context Created: ./dockerfiles/chef/ubuntu-12.04
```


```
$ knife container docker build chef/ubuntu-12.04 -z
  
Resolving cookbook dependencies...
Fetching cookbook index from https://supermarket.getchef.com...
Using apache2 (2.0.0)
Using iptables (0.14.0)
Using logrotate (1.7.0)
Using pacman (1.1.1)
Resolving cookbook dependencies...
Using apache2 (2.0.0)
Using iptables (0.14.0)
Using logrotate (1.7.0)
Using pacman (1.1.1)
Vendoring apache2 (2.0.0) to /Users/ryo/Dropbox/chef-container/dockerfiles/chef/ubuntu-12.04/dockerfiles/chef/ubuntu-12.04/chef/cookbooks/apache2
Vendoring iptables (0.14.0) to /Users/ryo/Dropbox/chef-container/dockerfiles/chef/ubuntu-12.04/dockerfiles/chef/ubuntu-12.04/chef/cookbooks/iptables
Vendoring logrotate (1.7.0) to /Users/ryo/Dropbox/chef-container/dockerfiles/chef/ubuntu-12.04/dockerfiles/chef/ubuntu-12.04/chef/cookbooks/logrotate
Vendoring pacman (1.1.1) to /Users/ryo/Dropbox/chef-container/dockerfiles/chef/ubuntu-12.04/dockerfiles/chef/ubuntu-12.04/chef/cookbooks/pacman
2014/11/06 22:48:33 no Dockerfile found in ./dockerfiles/chef/ubuntu-12.04
```

```
MacBook-Air:openvpn ryo$ knife container docker build ryoogata/openvpn -z -d /Users/ryo/Dropbox/chef-container/openvpn/dockerfiles
WARNING: No knife configuration file found
Resolving cookbook dependencies...
 :
 [2015-01-03T14:16:33+00:00] INFO: Deleting client key...
 ---> b5e443c34437
Removing intermediate container 8af304de0b1c
Step 3 : RUN rm -rf /etc/chef/secure/*
 ---> Running in aa595aa5fea8
 ---> ab7ab8bdf75c
Removing intermediate container aa595aa5fea8
Step 4 : ENTRYPOINT chef-init
 ---> Running in cbb05baf1b31
 ---> bf8cc5651781
Removing intermediate container cbb05baf1b31
Step 5 : EXPOSE 1194/udp
 ---> Running in 79d5ab0d3009
 ---> 791fd0d7ac11
Removing intermediate container 79d5ab0d3009
Step 6 : CMD --onboot
 ---> Running in 11d16d720628
 ---> 44fbeec75637
Removing intermediate container 11d16d720628
Successfully built 44fbeec75637
```



```
option :local_mode,
  boolean:      true,
  short:        '-z',
  long:         '--local-mode',
  description:  'Include and use a local chef repository to build the Docker image'

option :dockerfiles_path,
  short:        '-d PATH',
  long:         '--dockerfiles-path PATH',
  description:  'Path to the directory where Docker contexts are kept'
```

参照 URL
====

[Chef-Container Beta を使ってみる](http://jedipunkz.github.io/blog/2014/07/16/chef-container/)