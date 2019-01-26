---
layout: post
published: true
title: 在Blogger的经典模板（Classic Templates）中添加分类（Labels）列表
tags: [blogger, Javascript, blogspot, classic templates]
categories: [技术]    
description: 我的另外一个Blog，是用Blogger的FTP功能发布的，只能使用Classic Templates，常规是不能列出分类（Labels）列表的，不象用Blogspot发布的，可以用新的Layout，用“分类”控件实现。参考了著名的Blogspot Hacker phydeaux3 的文章《<a href="h
summary: 我的另外一个Blog，是用Blogger的FTP功能发布的，只能使用Classic Templates，常规是不能列出分类（Labels）列表的，不象用Blogspot发布的，可以用新的Layout，用“分类”控件实现。参考了著名的Blogspot Hacker 
---
我的[另外一个Blog](http://blog.yypig.net/)，是用Blogger的FTP功能发布的，只能使用Classic Templates，常规是不能列出分类（Labels）列表的，不象用Blogspot发布的，可以用新的Layout，用“分类”控件实现。

参考了著名的Blogspot Hacker [phydeaux3](http://phydeaux3.blogspot.com/)的文章《<a href="">[Automatic List of Labels for Blogger Classic Templates / FTP](http://phydeaux3.blogspot.com/2007/05/automatic-list-of-labels-for-classic.html)》。摸索出了适合中文的方法，介绍如下。

首先是如何列出所有的Labels。Blogger所提供的一个叫“metafeed”的功能可以实现这个，地址是：
	http://www.blogger.com/feeds/USERID/blogs/BLOGID
	
其中，USERID和BLOGID，可以在Blogger的控制面板中查到，所有对于用户级别的设置的链接中，可以看到USERID，所有关于特定Blog的设置的链接，都可以找到BLOGID。这个地址支持标准的GData的接口，所以可以通过在后面添加参数实现其他功能。这个地址其实是个Feed，所以原来的格式是atom。Javascript处理XML比较困难，所以要把它转化成Javascript比较容易处理的JSON格式，而且选用GData的json-in-script的方式，以便把它嵌入到网页中作为Javascript运行。所以可以采用：
	http://www.blogger.com/feeds/USERID/blogs/BLOGID?alt=json-in-script&callback=listLabels

其中listLabels是回调函数，参数就是JSON描述的对象，通过在回调函数中对JSON对象的处理，就可以列出所有的Labels。由于www.blogger.com在G～F～W外面，回传的东西里面都是Labels，一旦Labels包括某些关键字，后果可想而知，好在Google的大部分服务都提供https，所以最终采用的链接是：
	https://www.blogger.com/feeds/USERID/blogs/BLOGID?alt=json-in-script&callback=listLabels

列出所有的Labels还是不够，关键要解决每个Label所对应的链接是什么。对于全部是英文或者数字的Label，链接就是http://BLOGURL/Labels/后面加Label名称再加上html扩展名，但是一旦Label包含有中文就不是这样了，假如全部是中文，Label名称要进行base64编码，假如Label包括中文和英文或数字，那么Label名称里面的英文保持原文，每个中文要转为“=XX=YY=ZZ”，其中，XXYYZZ为16进制表示的中文字的Unicode编码。在参考了[这个连接](http://www.webtoolkit.info/javascript-base64.html)里面的Base64 Javascript实现后，最终的脚本为：
{% highlight javascript %}
	<script type="text/javascript">
	//<![CDATA[
	    var Base64 = {
		    
			// private property
		    _keyStr : "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=",
			// public method for encoding
			encode : function (input) {
			        var output = "";
			        var chr1, chr2, chr3, enc1, enc2, enc3, enc4;
			        var i = 0;
			        var go = false;
			        /* 判断是否包括数字和字母 */
			        if (input.match(/[A-Za-z0-9\+\/\=]/g)){
			            go = true;
			        }
			        input = Base64._utf8_encode(input);
			
			        while (i < input.length) {
			            /* 如果包括数字和字母，按特殊方式处理，否则用base64编码 */
			            if (go){
			                chr1 = input.charCodeAt(i++);
			                if (chr1<127){
			                    output = output +input.charAt(i-1);
			                    continue;
			                }
			                output = output + '=' + chr1.toString(16).toUpperCase();
			                continue;
			            }
			
			            chr1 = input.charCodeAt(i++);
			            chr2 = input.charCodeAt(i++);
			            chr3 = input.charCodeAt(i++);
			
			            enc1 = chr1 >> 2;
			            enc2 = ((chr1 & 3) << 4) | (chr2 >> 4);
			            enc3 = ((chr2 & 15) << 2) | (chr3 >> 6);
			            enc4 = chr3 & 63;
			
			            if (isNaN(chr2)) {
			                enc3 = enc4 = 64;
			            } else if (isNaN(chr3)) {
			                enc4 = 64;
			            }
			
			            output = output +
			            this._keyStr.charAt(enc1) + this._keyStr.charAt(enc2) +
			            this._keyStr.charAt(enc3) + this._keyStr.charAt(enc4);
			
			        }
			        /* 将结果中的斜杠替换成两个下划线 */
			        output = output.replace(/\//g,"__");
			        return output;
			    },
			
			
			    // private method for UTF-8 encoding
			    _utf8_encode : function (string) {
			        string = string.replace(/\r\n/g,"\n");
			        var utftext = "";
			
			        for (var n = 0; n < string.length; n++) {
			
			            var c = string.charCodeAt(n);
			
			            if (c < 128) {
			                utftext += String.fromCharCode(c);
			            }
			            else if((c > 127) && (c < 2048)) {
			                utftext += String.fromCharCode((c >> 6) | 192);
			                utftext += String.fromCharCode((c & 63) | 128);
			            }
			            else {
			                utftext += String.fromCharCode((c >> 12) | 224);
			                utftext += String.fromCharCode(((c >> 6) & 63) | 128);
			                utftext += String.fromCharCode((c & 63) | 128);
			            }
			
			        }
			
			        return utftext;
			    }
			
		};
			
		function listLabels(root){
			    var baseURL = 'http://blog.yypig.net/labels/';
			    var baseHeading = "Labels";
			    var isFTP = true;
			    var entry = root.entry;
			    var category = entry.category;
			    labelSort = new Array();
			    for(p in category){
			        labelSort[labelSort.length] = [category[p].term];
			    }
			    //labelSort.sort();
			    for (var r=0; r < labelSort.length; r++){
			        document.write('<a href="'+baseURL+Base64.encode(labelSort[r]+'')+'.html" >');
			        document.write(labelSort[r]+'');
			        document.write('</a>, ');
			    }
			
		}
			
	//]]>
	</script>
	<script type="text/javascript" src="https://www.blogger.com/feeds/USERID/blogs/BLOGID?alt=json-in-script&amp;callback=listLabels" ></script>
{% endhighlight %}			