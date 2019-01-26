---
layout: post
published: true
title: Zim Wiki 的中文乱码问题的解决办法
tags: [中文, zim, wiki, 笔记, 汉化]
categories: [技术]    
description: Zim 是一款桌面维基软件，可以当桌面笔记来用，小众软件推荐的&nbsp;，由于是用perl+gtk写成的，所以在Windwos下的安装非常麻烦，不过已经有人做了Windows的包，在这里&nbsp;。刚开始用，
summary: Zim是一款桌面维基软件，可以当桌面笔记来用，小众软件推荐的，由于是用perl+gtk写成的，所以在Windwos下的安装非常麻烦，不过已经有人做了Windows的包，在这里。刚开始用，
---
[Zim](http://zim-wiki.org/)是一款桌面维基软件，可以当桌面笔记来用，[小众软件推荐的](http://www.appinn.com/zim-wiki/)，由于是用perl+gtk写成的，所以在Windwos下的安装非常麻烦，不过已经有人做了Windows的包，在[这里](http://code.google.com/p/zimdesktopwiki-windows/)。刚开始用，不知道好不好用。不过首先发现的问题是日期乱码，而且发现在它的release note里面明明写了加上了简体中文翻译，但是界面还是英文的，设置里面好像也没有办法改，正想把它扔了，不过有点不甘心，发扬了开源的DIY精神，终于找到办法了。
 
在安装目录的bin下，有个zim文件，这是个perl脚本，也是Zim的主运行文件。在32至34行：
	# i18n initialization
	$Zim::CODESET = 'utf-8';
	$Zim::LANG = '';
	
把它改成：
	# i18n initialization
	$Zim::CODESET = 'gbk';
	$Zim::LANG = 'zh_CN';
	重新打开Zim，就是中文界面，而且日期不会乱码。

**2013/05/05更新**

Zim Wiki Desktop已经改为用Python写了，要想它显示中文，必须在系统环境变量里面设置LANG="zh_CN.UTF-8"。它的组织文档的方式我比较喜欢，其实有点像Markdown，目录结构变成了笔记分类的树形结构，还可以在目录里面放一般的文件，自动变成笔记的附件。我非常喜欢拿它来组织文档，并且用Git管理版本。它的一个大家盼望已久的功能：笔记的排序功能，一直未能实现，如果实现了，就非常完美了。

