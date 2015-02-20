---
layout: post
title: "Packer を利用して Windows7 の仮想イメージを作成する"
date: 2014-10-23 23:44:46 +0900
comments: true
category: packer
tags: ["windows"]
keywords: packer, windows
published: true

---

概要
====
Packer を利用して VirtualBox で利用可能な Windows7 のイメージを作成する  



packer 実行の流れ
====

1. Packer の Download 設置
----

下記の Packer Download URL から Packer 本体を Download し解凍後、Directory を ~/ 以下に packer に名前を変更して設置

* [Packer Download URL](https://www.packer.io/downloads.html)


2. Packer の実行ファイルの Download
----

本家から Fork した Packer 実行ファイルを git clone

```
$ git clone https://github.com/ryoogata/packer-windows.git
```


3. Windows iso イメージの設置
----

利用する Windows iso イメージの md5 の値を取得し、必要に応じて windows_7.json を修正

```
$ md5 windows7.iso 
MD5 (windows7.iso) = 69cc2d20bd2c15490f40d34e52d79290
```

* windows_7.json

```
    :
"iso_url": "iso/windows7.iso",
"iso_checksum_type": "md5",
"iso_checksum": "69cc2d20bd2c15490f40d34e52d79290",
    :
```

clone した packer-windows Directory 内の iso Directory に Windows の iso イメージを設置


4. image の作成
----

```
$ export PATH=~/packer:$PATH
$ cd packer-windows
$ packer build -only=virtualbox-iso windows_7.json 
```

- 実行ログ  

```   
:

==> virtualbox-iso (vagrant): Creating Vagrant box for 'virtualbox' provider
    virtualbox-iso (vagrant): Copying from artifact: output-virtualbox-iso/packer-virtualbox-iso-1413034723-disk1.vmdk
    virtualbox-iso (vagrant): Copying from artifact: output-virtualbox-iso/packer-virtualbox-iso-1413034723.ovf
    virtualbox-iso (vagrant): Renaming the OVF to box.ovf...
    virtualbox-iso (vagrant): Using custom Vagrantfile: vagrantfile-windows_7.template
    virtualbox-iso (vagrant): Compressing: Vagrantfile
    virtualbox-iso (vagrant): Compressing: box.ovf
    virtualbox-iso (vagrant): Compressing: metadata.json
    virtualbox-iso (vagrant): Compressing: packer-virtualbox-iso-1413034723-disk1.vmdk
Build 'virtualbox-iso' finished.

==> Builds finished. The artifacts of successful builds are:
--> virtualbox-iso: 'virtualbox' provider box: windows_7_virtualbox.box
```



Tips
====

packer コマンド実行中に実行される Script
----
packer コマンド実行中、windows_7.json に記載がある下記の script が実行される

```
"scripts": [
  "./scripts/vm-guest-tools.bat",
  "./scripts/chef.bat",
  "./scripts/vagrant-ssh.bat",
  "./scripts/disable-auto-logon.bat",
  "./scripts/enable-rdp.bat",
  "./scripts/compile-dotnet-assemblies.bat",
  "./scripts/compact.bat"
]
```


TimeZone の変更
----
answer_files/7/Autounattend.xml の修正

* 修正前

```
<TimeZone>Pacific Standard Time</TimeZone>

```

* 修正後

```
<TimeZone>Tokyo Standard Time</TimeZone>
```


ComputerName の変更
----
answer_files/7/Autounattend.xml の修正

* 修正前

```
<ComputerName>vagrant-2012</ComputerName>
```

* 修正後

```
<ComputerName>vagrant-win7</ComputerName>
```

ComputerName は `15 文字以内` で指定する


Headless の設定
----
Packer 実行ファイルのデバック時などは VirtualBox の Window が表示される方が便利。Default では Headless なので、必要に応じて変更する。


windows_7.json の修正

* 修正前

```
"headless": true,
```

true の場合、Headless になり、packer 実行中の VirtualBox の Window が表示されない

* 修正後

```
"headless": false,
```


Windows の日本語化
----

answer_files/7/Autounattend.xml の修正

en-US の箇所を ja-JP に変更

* 修正前

```
<UILanguage>en-US</UILanguage>
<InputLocale>en-US</InputLocale>
<SystemLocale>en-US</SystemLocale>
<UILanguage>en-US</UILanguage>
<UILanguageFallback>en-US</UILanguageFallback>
<UserLocale>en-US</UserLocale>
```

* 修正前

```
<UILanguage>ja-JP</UILanguage>
<InputLocale>ja-JP</InputLocale>
<SystemLocale>ja-JP</SystemLocale>
<UILanguage>ja-JP</UILanguage>
<UILanguageFallback>ja-JP</UILanguageFallback>
<UserLocale>ja-JP</UserLocale>
```

Image 作成中の Windows Update の停止
----
Packer 実行中、Windows Update を実施しないようにする

answer_files/7/Autounattend.xml の修正

* 修正前

```
    <!-- WITHOUT WINDOWS UPDATES -->
    <!--
        :
    -->
    <!-- END WITHOUT WINDOWS UPDATES -->
    <!-- WITH WINDOWS UPDATES -->
        :
    <!-- END WITH WINDOWS UPDATES -->
```

* 修正後

```
    <!-- WITHOUT WINDOWS UPDATES -->
        :
    <!-- END WITHOUT WINDOWS UPDATES -->
    <!-- WITH WINDOWS UPDATES -->
    <!--
        :
    -->
    <!-- END WITH WINDOWS UPDATES -->
```


トラブルシュート
====

「応答ファイルは無効です」エラー
----


### エラーログ

```
パス[specialize]の無人応答ファイル[C:\Windows\Panther\unattend.xml]を解析または処理できませんでした。応答ファイルは無効です。
```

### 原因  
ComputerName が文字数制限の `15 文字` を超えている文字列を指定していた。

```  
Autounattend.xml

<ComputerName>vagrant-windows7</ComputerName>
```


Error uploading guest additions
----

### エラーログ

```
Build 'virtualbox-iso' errored: Error uploading guest additions: Process exited with: 1. Reason was:  ()

==> Some builds didn't complete successfully and had errors:
--> virtualbox-iso: Error uploading guest additions: Process exited with: 1. Reason was:  ()
```

### 原因  
仮想メディアマネージャの光学ディスクのところに VBoxWindowsAdditions が入っていないとダメかも。適当な Windows OS に VBoxWindowsAdditions を Mount して、再度 packer コマンドを実行したらとりあえず解決できた。



compact.bat の sdelete.exe コマンド実行後に固まる
----

### エラーログ
```
cmd /c C:\Windows\Temp\sdelete.exe -z C:


    virtualbox-iso: SDelete - Secure Delete v1.61
    virtualbox-iso: Copyright (C) 1999-2012 Mark Russinovich
    virtualbox-iso: Sysinternals - www.sysinternals.com
    virtualbox-iso:
    virtualbox-iso: SDelete is set for 1 pass.
    virtualbox-iso: Zeroing free space on C:\: 0%
==> virtualbox-iso: Provisioning with shell script: /var/folders/c0/mrt_fmsx6l1b_kjl7pbstr2h0000gn/T/packer-shell022007924
    virtualbox-iso: rm: cannot remove `/tmp/TMP0000000149D9D38AB494459F': Device or resource busy
==> virtualbox-iso: Unregistering and deleting virtual machine...
==> virtualbox-iso: Deleting output directory...
Build 'virtualbox-iso' errored: Script exited with non-zero exit status: 1

==> Some builds didn't complete successfully and had errors:
--> virtualbox-iso: Script exited with non-zero exit status: 1

==> Builds finished but no artifacts were created.
    :
    :
```

### 原因

packer-windows 実行ファイルの ver 1.23 で修正済み。 1.22 以前のバージョンでは修正の必要あり

[本家 packer-windows URL](https://github.com/joefitzgerald/packer-windows)


windows_7.json の下記を修正

* 修正前

```
     "type": "shell",
      "inline": [
        "rm -rf /tmp/*"
      ]
    }
```

* 修正後

```
     "type": "shell",
      "inline": [
        "rm -rf /tmp/* || ! false"
      ]
    }
```


参照 URL
====

* [OSXでpackerでCentOS6.4のVirtualBox VMを作成する](http://qiita.com/hnakamur/items/da0ed14192a18405f704)

* [Packer で Windows の Box (vmware-iso) を作成するまで](http://azukipochette.hatenablog.com/entry/2014/05/16/212551)

* [packer-windowsテンプレートを使ってWindows 8.1 update(日本語版)評価版のVagrant Boxができた](http://www.nofuture.tv/diary/20140530.html)

* [packer-windows](https://github.com/joefitzgerald/packer-windows)
  * このテンプレートを Fork して使ってみる

* [Index numbers for GuiUnattended/TimeZone](http://unattended.sourceforge.net/timezones.php)

* [packer-windows-vm](https://github.com/develersrl/packer-windows-vm.git)


