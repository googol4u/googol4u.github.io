---
layout: post
title: "FreeBSD Java SDK一个需要注意的地方"
tags: [Java, FreeBSD]
categories: 技术    
description: "在FreeBSD 4.x 和 5.x 上用Linux兼容模式运行jdk时，会出现这样一个警告"
summary: "在FreeBSD 4.x 和 5.x 上用Linux兼容模式运行jdk时，会出现这样一个警告"
---
在FreeBSD 4.x 和 5.x 上用Linux兼容模式运行jdk时，会出现这样一个警告：

	Java HotSpot(TM) Client VM warning: Can't detect initial thread stack location
 不知道会有什么问题，总之觉得不是很好，在Google上查了一下，有人说要重编Kernel，有人给出了我认为是正确的解决办法：Java出现这个警告的主要原因是找不相应的进程的信息。因为是Linux兼容模式，Linux下的/proc实际上给映射到了FreeBSD下的/compat/linux/proc目录下，这实际上也是一种文件系统，在FreeBSD下称为linprocfs，默认的时候并没有给mount起来。所以在运行Java前一定要先把/compat/linux/proc给mount起来。具体做法如下：

1、在/etc/fstab 中添加一行：

	linprocfs /compat/linux/proc linprocfs rw 0 0

2、打入命令：

	mount /compat/linux/proc/

再运行Java就不会有原来的错误信息了。
{% unless page.tags == empty %}
{% assign tags_list = page.tags %}
<p class="tags">
Tagged: {{ page.tags | join : ', ' }}
</p>
{% endunless %}
<div id="comments" /></div>