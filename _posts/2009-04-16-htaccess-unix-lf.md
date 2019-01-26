---
layout: post
published: true
title: .htaccess 与 Unix 换行符
tags: [网站]
categories: [技术]    
description: .htaccess是一个很好的工具，尤其对于静态网站。我的网站是全静态的，原来是把RSS放在了/rss/目录下，后来改成了直接放在根目录，但是又不想每次更新都要去同步两边的文件，所以想把原来/rss/下的RSS Feed重定向到根目录下的RSS Feed，我在.htaccess文件中写了这两句：Redirect /r
summary: .htaccess是一个很好的工具，尤其对于静态网站。我的网站是全静态的，原来是把RSS放在了/rss/目录下，后来改成了直接放在根目录，但是又不想每次更新都要去同步两边的文件，所以想把原来/rss/下的RSS Feed重定向到根目录下的RSS Feed，我在.htaccess文件中写了这两句：Redirect /rss.xml http://blog.yypig.net/rss.xml Redirect /atom.xml 
---
.htaccess是一个很好的工具，尤其对于静态网站。

我的网站是全静态的，原来是把RSS放在了/rss/目录下，后来改成了直接放在根目录，但是又不想每次更新都要去同步两边的文件，所以想把原来/rss/下的RSS Feed重定向到根目录下的RSS Feed，我在.htaccess文件中写了这两句：
	Redirect /rss.xml http://blog.yypig.net/rss.xml
	Redirect /atom.xml http://blog.yypig.net/atom.xml

但是一放到网站上，就出现了500 Internal Server Error，但是如果只有一行就可以了，百思不得其解，以为一个文件里面只能出现一个Redirect语句。后来看到了这个[链接](http://archives.hwg.org/hwg-techniques/20020812191449.522.qmail@web12406.mail.yahoo.com)，恍然大悟，原来只是换行符的问题，如果服务器是Unix的，一定要用Unix换行符，改了之后一切正常。

.htaccess其实功能很强大，最近就通过它的URL Rewrite功能，把公司的动态网页的URL全部变成由目录和文件名组成的貌似静态网页的结构,以类似“服务.html”代替“index.php?cat=2&item=1”，据说这样对搜索引擎比较友好。

