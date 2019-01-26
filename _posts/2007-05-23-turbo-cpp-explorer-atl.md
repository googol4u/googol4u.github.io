---
layout: post
published: true
title: Turbo C++ Explorer 不支持ATL
tags: [IDE, 开发工具, C++, Borland]
categories: [技术]
description: 在编译一个OPC的例子的时候，发现Turbo C++ Explorer不支持ATL。根据Borland的说法，其实在BDS 2006的时候已经移除了ATL的支持。不知道为什么？BCB 4.0 到 6.0是支持的。如果在编译的时候报告找不到vcla1.h，说明你的程序用了ATL。
summary: 在编译一个OPC的例子的时候，发现Turbo C++ Explorer不支持ATL。根据Borland的说法，其实在BDS 2006的时候已经移除了ATL的支持。不知道为什么？BCB 4.0 到 6.0是支持的。如果在编译的时候报告找不到vcla1.h，说明你的程序用了ATL。
---
在编译一个OPC的例子的时候，发现Turbo C++ Explorer不支持ATL。根据[Borland的说法](http://qc.borland.com/wc/qcmain.aspx?rc=43673)，其实在BDS 2006的时候已经移除了ATL的支持。不知道为什么？BCB 4.0 到 6.0是支持的。<br /><br />如果在编译的时候报告找不到vcla1.h，说明你的程序用了ATL。
