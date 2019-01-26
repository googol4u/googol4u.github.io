---
layout: post
published: true
title: 在mldonkey中使用无tracker的种子文件（torrent）
tags: BT
categories: [技术]    
description: mldonkey是一个功能强大的能用于unix系列服务器的p2p下载工具。支持多协议（包括edonkey和bittorrent），支持web界面，cli界面等等。cli命令强大无比，基本可以实现所有的操作。可以建一个目录，只要把torrent文件放到这个目录下，它会定期去扫描这个目录，发现有新的torrent文件就增
summary: mldonkey是一个功能强大的能用于unix系列服务器的p2p下载工具。支持多协议（包括edonkey和bittorrent），支持web界面，cli界面等等。cli命令强大无比，基本可以实现所有的操作。可以建一个目录，只要把torrent文件放到这个目录下，它会定期去扫描这个目录，发现有新的torrent文件就增加到下载里面去。所以是服务器端下载的最好工具。<br /><br />但是mldoneky有个问题，一直不支持DHT，知道最新近在
---
mldonkey是一个功能强大的能用于unix系列服务器的p2p下载工具。支持多协议（包括edonkey和bittorrent），支持web界面，cli界面等等。cli命令强大无比，基本可以实现所有的操作。可以建一个目录，只要把torrent文件放到这个目录下，它会定期去扫描这个目录，发现有新的torrent文件就增加到下载里面去。所以是服务器端下载的最好工具。

但是mldoneky有个问题，一直不支持DHT，知道最新近在[代码库](http://repo.or.cz/w/mldonkey.git/shortlog/refs/heads/dev/dht)里面才有了DHT的支持，虽然是支持DHT，但对于无tracker的torrent文件，还是判为非法文件，无法下载。想了一下，发现直接更改torrent文件，随便加一个比较常用的tracker进去就可以解决这个问题。直接修改文件要用到[BEncode Editor](http://forum.utorrent.com/viewtopic.php?pid=288182)这个工具。

另外mldoneky的最新版本在添加DHT支持时，代码对64位系统支持不好，Random.Int 函数后面的参数要求最大不能超过2^30-1，但是max_int确却是2^60-1次，所以bT_DHT.ml文件中，要将max_int替换成1073741823才能在64位系统下正常运行。

**更新**

最新的mldonkey已经支持DHT和Magnet磁力链接了。
