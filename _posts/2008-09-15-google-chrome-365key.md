---
layout: post
published: true
title: Google Chrome中如何使用365key提交网摘？
tags: [365key, Javascript]
categories: [技术]    
description: 365key自从被收购了之后，越做越糟糕，但是由于它的“将网摘共享到我的网站”上的功能做得比较能适合我的需要，所以一直在用它。它的右键收藏功能在Firefox 3.0之后就没办法使用，所以只能退而使用“书签”的方式提交网摘。“书签”方式就是把一段用于提交网摘的Javascript保存成书签，需要提交网摘时，选择相应书
summary: 365key自从被收购了之后，越做越糟糕，但是由于它的“将网摘共享到我的网站”上的功能做得比较能适合我的需要，所以一直在用它。它的右键收藏功能在Firefox 3.0之后就没办法使用，所以只能退而使用“书签”的方式提交网摘。“书签”方式就是把一段用于提交网摘的Javascript保存成书签，需要提交网摘时，选择相应书签，运行Javascript就OK了，兼容性比较好。当开始使用Google Chrome之后，也想用这种方法来提交网摘，但是发现有一个Bug：选中的文本无法作为摘要提交。原来的代码是
---
365key自从被收购了之后，越做越糟糕，但是由于它的“将网摘共享到我的网站”上的功能做得比较能适合我的需要，所以一直在用它。它的右键收藏功能在Firefox 3.0之后就没办法使用，所以只能退而使用“书签”的方式提交网摘。“书签”方式就是把一段用于提交网摘的Javascript保存成书签，需要提交网摘时，选择相应书签，运行Javascript就OK了，兼容性比较好。当开始使用Google Chrome之后，也想用这种方法来提交网摘，但是发现有一个Bug：选中的文本无法作为摘要提交。原来的代码是这样的：
	javascript:d=document;t=d.selection?(d.selection.type!='None'?d.selection.createRange().text:''):(d.getSelection?d.getSelection():'');void(keyit=window.open('http://www.365key.com/storeit.aspx?t='+escape(d.title)+'&u='+escape(d.location.href)+'&c='+escape(t),'keyit','scrollbars=no,width=475,height=575,left=75,top=20,status=no,resizable=yes'));keyit.focus();
	
其中采用的两种获得选择文本的方法：document.getSelection()或者document.selection.createRange().text，在Google Chrome中都失效了。通过研究，发现在Google Chrome中，获得选择文本的方法为：window.getSeleciton()。所以将代码更改如下：
	javascript:d=document;t=window.getSelection();void(keyit=window.open('http://www.365key.com/storeit.aspx?t='+escape(d.title)+'&amp;u='+escape(d.location.href)+'&c='+escape(t),'keyit','scrollbars=no,width=475,height=575,left=75,top=20,status=no,resizable=yes'));keyit.focus();

结果测试一下可以了，需要的人可以把[提交天天网摘(P)](javascript:d=document;t=window.getSelection(\);void(keyit=window.open('http://www.365key.com/storeit.aspx?t='+escape(d.title\)+'&u='+escape(d.location.href\)+'&c='+escape(t\),'keyit','scrollbars=no,width=475,height=575,left=75,top=20,status=no,resizable=yes'\)\);keyit.focus(\);)(弹出窗口方式)拖到书签栏。

另外，提交网摘网页里面，分类已经在很久以前就不行了，估计也是Javascript的兼容问题，希望365Key可以改一改。

