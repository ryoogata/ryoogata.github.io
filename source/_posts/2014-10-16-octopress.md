---
layout: post
title: "Octopress 入門してみた"
date: 2014-10-16 12:20:15 +0900
comments: true
categories: octopress
published: true 

---

概要
====
Octopress を使って Blog を公開してみようと思い立ってから、あれこれ調べたこと。


参照 URL
----
* [Octpress簡単スタートガイド](http://www.slideshare.net/yizawa/octopress)


Octopress セットアップ
====

Octopress を持ってくる
----
```
$ git clone git://github.com/imathis/octopress.git octopress
```


bundler の実行
----
```
$ bundle install --path vendor/bundle --binstubs vendor/binstubs
```

Default テーマの設定
----
```
$ rake install
```

static サイトの生成
----


### サイトの生成
```
$ rake generate
```

### Preview の表示
```
$ rake preview
```


手元の octpress Directory と github の master ブランチを連携させる
----

```
$ rake setup_github_pages
Enter the read/write url for your repository
(For example, 'git@github.com:your_username/your_username.github.io.git)
           or 'https://github.com/your_username/your_username.github.io')
Repository url: https://github.com/ryoogata/ryoogata.github.io.git
Added remote https://github.com/ryoogata/ryoogata.github.io.git as origin
Set origin as default remote
Master branch renamed to 'source' for committing your blog source files
rm -rf _deploy
mkdir _deploy
cd _deploy
Initialized empty Git repository in /Users/ryo/Dropbox/octopress/_deploy/.git/
[master (root-commit) 9208c45] Octopress init
 1 file changed, 1 insertion(+)
 create mode 100644 index.html
cd -

---
## Now you can deploy to https://github.com/ryoogata/ryoogata.github.io.git with `rake deploy` ##
```

octpress Directory 自体 を source ブランチに登録する
----
```
$ git add .
$ git commit -m "first commit"
$ git push origin source

Counting objects: 4901, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2181/2181), done.
Writing objects: 100% (4901/4901), 1.34 MiB | 231.00 KiB/s, done.
Total 4901 (delta 2366), reused 4871 (delta 2338)
To https://github.com/ryoogata/ryoogata.github.io.git
 * [new branch]      source -> source
```



記事の作成
====

記事の準備
----
```
$ rake new_post['vagrant chef-Server']
```

rake new_post['']の引数として、空白区切りで 最初の単語をcategory、後の単語を全てtagsとして登録する


記事を複数のカテゴリに含める
----

category の箇所に複数のカテゴリを記述する

```
layout: post
title: "Vagrant の Provisioner に Chef Client を利用する際に遭遇したトラブルとその解決策" 
date: 2014-10-16 16:41:05 +0900
comments: true
category: ["vagrant", "chef"]
tags: ["Chef-Server"]
keywords: Chef, Chef-Server, vagrant
published: true 
```


Customize
====


記事のパーマリンク変更
----

_config.yml

- 修正前
permalink: /blog/:year/:month/:day/:title/

- 修正後
permalink: /blog/:categories/:title/


タグクラウド
----
[Octopress Tips](http://rcmdnk.github.io/blog/2013/03/23/octopress/#section-4)

```
$ cd /tmp
$ git clone https://github.com/tokkonopapa/octopress-tagcloud
$ cd octopress-tagcloud/
$ cp tag_cloud.rb ~/Dropbox/ryoogata.github.io/plugins/
$ cp source/_includes/custom/asides/tag_cloud.html ~/Dropbox/octopress/source/_includes/custom/asides/
$ cp source/_includes/custom/asides/category_list.html ~/Dropbox/octopress/source/_includes/custom/asides/
```

記事の下書き設定
----


Rakefile に `published: false` を追加

```
111   open(filename, 'w') do |post|
112     post.puts "---"
113     post.puts "layout: post"
114     post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
115     post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M:%S %z')}"
116     post.puts "comments: true"
117     post.puts "categories: "
118     post.puts "published: false"
119     post.puts "---"
120   end
```


Tips
====

rake deploy について
----
* master ブランチ
  * static サイト用コンテンツ
  * 手元の octpress directory で generate したファイルが commit される
* source ブランチ
  * octpress 用の記事のソース

github には master ブランチのファイルを元にページが作成される
