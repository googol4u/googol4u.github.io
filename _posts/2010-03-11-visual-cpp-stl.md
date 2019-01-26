---
layout: post
published: true
title: Visual C++ STL 相关编译错误的解决办法
tags: [STL, VC, C, C++]
categories: [技术] 
description: 
summary: 
---
和所有的垄断企业一样，微软的做法经常是武断和专横的，一个困扰了我一个多月的问题解决了，结果竟是如此的不可思议。有一个库的binary是在VC 2005下编译的，如果用VC 2008编译例程link用VC 2005的库文件时程序会Crash，所以只能用回VC 2005编译例程。在用VC 2005编译例程的时候，link时出现了这样的错误：
	error LNK2001: unresolved external symbol "void __cdecl std::_Throw(class stdext::exception const &)" (?_Throw@std@@YAXABVexception@stdext@@@Z)
	error LNK2001: unresolved external symbol "void (__cdecl* std::_Raise_handler)(class stdext::exception const &)" (?_Raise_handler@std@@3P6AXABVexception@stdext@@@ZA)
	
在网上搜了大半天，没有任何头绪，把Windows SDK升级了也不能解决问题。忽然记起用VC 2008编译时是可以通过的，搜索stdext::exception时，发现网上有些地方贴出来的类似的出错的提示信息中显示的exception头文件中，stdext::exception定义所在的行数和VC 2005中exception头文件不符，才怀疑VC 2005到VC 2008的头文件有变化，结果发现，在VC 2005中，exception是放在std的namespace中，而VC 2008的exception是放在stdext的namespace中。但是这个库的Binary为什么是在VC 2005下编译，但是用的又是stdext::exception呢？突然想到微软经常会出SP1，SP2这些东西，下了VC 2005 SP1安装之后，编译通过。[据说](http://blogs.msdn.com/vcblog/archive/2009/05/25/stl-breaking-changes-in-visual-studio-2010-beta-1.aspx)，VC 2010还会把一些类从std移入stdext，但是为什么要把类移来移去造成混乱呢，没有找到答案。

所有高度依赖STL的VC程序都会有类似的问题，所以一般都会写明要升级到VC 2005 SP1：

* [Google V8的编译说明](http://code.google.com/p/v8/wiki/BuildingOnWindows)
* [Chromium的编译说明](http://dev.chromium.org/developers/how-tos/build-instructions-windows#TOC-Additional-free-downloads)

其中提到的另外一种编译错误：

<span class="Apple-style-span" style="border-collapse: separate; color: black; font-family: Simsun; font-size: medium; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px;"><span class="Apple-style-span" style="font-family: arial,sans-serif; font-size: 13px;"><pre class="prettyprint" style="border-left: 3px solid rgb(204, 204, 204); font-size: 12px; margin-left: 2em; padding: 0.5em;"><span class="pln" style="color: black;">LIBCMTD</span><span class="pun" style="color: #666600;">.</span><span class="pln" style="color: black;">lib</span><span class="pun" style="color: #666600;">(</span><span class="pln" style="color: black;">stdexcpt</span><span class="pun" style="color: #666600;">.</span><span class="pln" style="color: black;">obj</span><span class="pun" style="color: #666600;">)</span><span class="pln" style="color: black;"> </span><span class="pun" style="color: #666600;">:</span><span class="pln" style="color: black;"> error LNK2005</span><span class="pun" style="color: #666600;">:</span><span class="pln" style="color: black;"> </span><span class="str" style="color: #008800;">"public: virtual __thiscall std::exception::~exception(void)"</span><span class="pln" style="color: black;"> </span><span class="pun" style="color: #666600;">(??</span><span class="lit" style="color: #006666;">1exception@std@@UAE@XZ</span><span class="pun" style="color: #666600;">)</span><span class="pln" style="color: black;"> already </span><span class="kwd" style="color: #000088;">defined</span><span class="pln" style="color: black;"> </span><span class="kwd" style="color: #000088;">in</span><span class="pln" style="color: black;"> mksnapshot</span><span class="pun" style="color: #666600;">.</span><span class="pln" style="color: black;">obj<br />LIBCMTD</span><span class="pun" style="color: #666600;">.</span><span class="pln" style="color: black;">lib</span><span class="pun" style="color: #666600;">(</span><span class="pln" style="color: black;">stdexcpt</span><span class="pun" style="color: #666600;">.</span><span class="pln" style="color: black;">obj</span><span class="pun" style="color: #666600;">)</span><span class="pln" style="color: black;"> </span><span class="pun" style="color: #666600;">:</span><span class="pln" style="color: black;"> error LNK2005</span><span class="pun" style="color: #666600;">:</span><span class="pln" style="color: black;"> </span><span class="str" style="color: #008800;">"public: virtual char const * __thiscall std::exception::what(void)const "</span><span class="pln" style="color: black;"> </span><span class="pun" style="color: #666600;">(?</span><span class="pln" style="color: black;">what@exception@std@@UBEPBDXZ</span><span class="pun" style="color: #666600;">)</span><span class="pln" style="color: black;"> already </span><span class="kwd" style="color: #000088;">defined</span><span class="pln" style="color: black;"> </span><span class="kwd" style="color: #000088;">in</span><span class="pln" style="color: black;"> mksnapshot</span><span class="pun" style="color: #666600;">.</span><span class="pln" style="color: black;">obj</span></pre></span></span>

