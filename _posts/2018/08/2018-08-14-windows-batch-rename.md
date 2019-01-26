---
layout: post
title: Windows下如何批量更改文件名
tagline: "windows"
category: null
tags: [windows]
published: true
github_comments_issueid: "2"
---

题目应该是普通用户Windows下如何不写脚本、程序，批量地更改文件名。借助了excel，但是尽量不要写VBA，因为VBA对于普通用户还是有一定的难度。

## 获取文件名

尽量不使用Excel的VBA函数，如何能把一个目录的所有文件的文件名写到Excel里面？

首先，打开目录。

![1534181813236](/assets/post-images/1534181813236.png)

按Ctrl-C或者右键菜单复制：

![1534181929459](/assets/post-images/1534181929459.png)

打开浏览器，将复制的路径粘贴到地址栏并打回车：

![1534182042675](/assets/post-images/1534182042675.png)

出现文件夹内容：

![1534182103786](/assets/post-images/1534182103786.png)

按CTRL-A全选，再CTRL-C或者右键菜单复制。

![1534182226272](/assets/post-images/1534182226272.png)

再打开Excel，按CTRL-V粘贴进去。

![1534182310594](/assets/post-images/1534182310594.png)

删除第一、二行，再调整好宽度，顺序等。

![1534182392091](/assets/post-images/1534182392091.png)

至此，文件名导入EXCEL的工作完成

## 生成目标文件名

一般这种批量更改的情况下，目标文件名都有规律的，无非就是对原来的文件名进行某种更改，比如在这个例子中，可以将DSC替换成PIC等，或者按照一定的排序，用序号方式对文件进行命名，例如：PIC001，PIC002等。这些都需要使用到一点EXCEL的公式。

### 替换原文件名中的部分字符

在这里，将用到EXCEL公式里面的SUBSTITUE这个函数。具体用法是：

```vbscript
=SUBSTITUTE(单元格, "旧文本", "新文本")
```

我们选中，D2单元格，在里面打入：

```vbscript
=SUBSTITUTE(A2, "DSC_", "PIC")
```

回车：

![1534183229290](/assets/post-images/1534183229290.png)

然后选中D2单元格，把光标移到右下角，光标变成实心十字时，双击，这样整列到最后一行都会应用这个公式。

![1534183386533](/assets/post-images/1534183386533.png)

这个是替换原文件字符的做法。

### 用序号对文件进行命名

在这里会用到EXCEL的几个公式的函数：

- 行号，表示目前是EXCEL表的第几行：

```vbscript
ROW()
```

- 取单元格或者字符串结束部分，因为文件的扩展名有可能不同，我们要保留原来的扩展名：

```vbscript
RIGHT(单元格，长度)
```

- 替换单元格或者字符串的中间一段字符：

```vbscript
REPLACE(单元格, 开始位置, 结束位置, "字符串")
```

- 格式化数字的输出，比如我们想文件名呈现0001、0002这样的编号，而不是1、2这样。

```vbscript
text(数值, 格式)
```

- 将将各个单元格或者字符串连接起来。

```vbscript
CONCATENATE(文本1, 文本2, ... )
```



取A2的扩展名（包含”.“）就是：

```vbscript
REPLACE(RIGHT(A2,5),1,SEARCH(".",RIGHT(A2,5))-1,"")
```

格式化行号（第一行是标题，所以是：row()-1）的公式就是：

```vbscript
text(row()-1,"0000") 
```

合起来就是

```vbscript
CONCATENATE("PIC",TEXT(ROW()-1,"0000"),REPLACE(RIGHT(A2,5),1,SEARCH(".",RIGHT(A2,5))-1,""))
```



在EXCEL中的D2写入以上公式，并打回车，再双击D2的右下角，把公式复制到整列直到最后一行。

![1534186923297](/assets/post-images/1534186923297.png)

这样就完成了按序号的新文件名的生成。



## 生成更改文件名的语句

利用Windows命令行中的命令：

```
ren 旧文件名 新文件名
```

为每个文件生成一个改名的语句，并将其放入一个批处理文件运行。

在E2中，打入以下公式（注意ren后面的空格和中间第三个参数的空格：

```vbscript
CONCATENATE("ren ",A2," ",D2)
```

同样，回车后，双击右下角，使公式应用到每一行中：

![1534188909868](/assets/post-images/1534188909868.png)



然后选中E列，复制，打开记事本，粘贴进去。并另存为文件所在目录下的rename.bat文件。

![1534189029688](/assets/post-images/1534189029688.png)

**备份好原来的文件**，在批量文件所在的目录中，双击运行此文件：

![1534189184859](/assets/post-images/1534189184859.png)

如果一切正常，所有的文件都将按excel的设置改好名字：

![1534189292886](/assets/post-images/1534189292886.png)