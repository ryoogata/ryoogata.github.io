---
layout: post
title: "docker docker-hub"
date: 2014-11-07 12:01:33 +0900
comments: true
category: docker
tags: ["docker-hub"]
keywords: docker, docker-hub
published: false

---

Repository
----

Docker Hub へログイン

```
$ docker login

Username: ryoogata
Password: 
Email: hogehoge@example.com
Login Succeeded
```

```
$ docker push ryoogata/memcached

The push refers to a repository [ryoogata/memcached] (len: 1)
Sending image list
Pushing repository ryoogata/memcached (1 tags)
511136ea3c5a: Image already pushed, skipping 
d497ad3926c8: Image already pushed, skipping 
ccb62158e970: Image already pushed, skipping 
e791be0477f2: Image already pushed, skipping 
3680052c0f5c: Image already pushed, skipping 
22093c35d77b: Image already pushed, skipping 
5506de2b643b: Image already pushed, skipping 
8335323317d3: Image successfully pushed 
Pushing tag for rev [8335323317d3] on {https://cdn-registry-1.docker.io/v1/repositories/ryoogata/memcached/tags/latest}
```


Automated build
----


参照 URL
====

[Docker Hubの使い方とGitHubからのDockerイメージ自動ビルド ](http://www.atmarkit.co.jp/ait/articles/1408/26/news038_2.html)