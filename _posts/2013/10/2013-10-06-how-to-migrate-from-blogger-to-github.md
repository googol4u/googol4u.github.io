---
layout: post
title: "如何从blogger迁移到GitHub pages"
description: ""
category: 技术
tags: [blogger,互联网,github]
---

博客已经没有以前那么热门了，但是博客这种形式还是有存在的价值，确实像它原来的名字Web Logger所表示那样，它带有一种类似日记的功能，多年后回去翻看的时候，可以回想起自己当年在做什么。

Blogger的服务一直被屏蔽，当年选择它的原因是因为它支持生成全静态的网页，并可以FTP到一部服务上，这样可以把服务放在墙内，但后来Google宣布不支持这个功能了。所以一直没找到好的发布blog的方式。直到发现了GitHub pages，也是通过生成全静态的方式发布，比Blogger更好的地方是还有版本管理功能。暂时没有被墙。

找了个还不算丑的模板后，终于设置好了自己的GitHub Pages了，剩下的问题就如何把Blogger上的内容迁移过来了。研究了一下，发现Google takeout有包括Blogger的内容，试了一下，发现导出的是一个atom文件，内容和标签都保留了，但是图片没有，希望Google能改进一下把图片也导出就好多了。因为之前是全静态发布的，所以图片的目录我有备份，而且是和正文的链接对应的。

迁移的过程主要解决以下三个问题：  

1. 读取和分析Blogger takeout出来的atom文件
    这个部分主要是借助Rome这个Java库。难点在于Google Takeout把博客的一些设置也做成了atom的entry，所以要区分entry的内容是设置还是正文，这个主要是通过entry下的category的一个特殊schema来区分的。除此之外，正文的标签也是保存在entry的category下，也必须读出来再转换成markdown的标签。  
    模板部分的category是这个样子：  
    ```    
    <category scheme='http://schemas.google.com/g/2005#kind' term='http://schemas.google.com/blogger/2008/kind#template'/>
    ```    
     配置部分的category是这样的：  
     ```
    <category scheme='http://schemas.google.com/g/2005#kind' term='http://schemas.google.com/blogger/2008/kind#settings'/>
    ```    
    帖子部分的category是这样的：  
    ```
    <category scheme='http://schemas.google.com/g/2005#kind' term='http://schemas.google.com/blogger/2008/kind#post'/>
    <category scheme='http://www.blogger.com/atom/ns#' term='STL'/>
    <category scheme='http://www.blogger.com/atom/ns#' term='VC'/>
    ```
2. 将正文内容转换成markdown文件  
这个部分主要是借助remark这个Java库。atom导出的正文是一个HTML文件，remark的作用就是将HTML转换成markdown格式 ，会自动转换一些格式，例如图片或者超链接。
3. 其他的一些转换问题  
由于原来的博客是有域名的，导出的正文的图片链接是加上了域名的，所以必须将域名替换成Github pages的域名。另外，Jekyll对于post的格式有一定的要求，在前面会加上一段yaml格式的标识，来加入分类、标签、摘要等一些信息，其中摘要要求是纯文本的，所以从正文取的摘要需要进行一些处理。图片链接的转换，是直接用查找替换完成的，post的格式使用了模板的方式实现，正文摘取了纯文本的摘要用了jsoup这个库。如果要更加完美的话，其实还需要对正文中指向自己域的一些链接也做转换，不过时间关系，就没有去写这部分的代码了。如果要对链接进行完美的替换，就要用到完整的jsoup的功能，个人非常喜欢jsoup这个html的parser，它支持用jquery的select语法来选择一个页面的元素，这对于抓取一些结构化的网页非常有帮助，例如：抓取x-art网站上列出的所有视频的详细资料。

好吧代码在这里：

{% highlight java %}
	package test;
	
	import java.io.File;
	import java.io.FileInputStream;
	import java.io.FileWriter;
	import java.io.IOException;
	import java.net.URL;
	import java.nio.MappedByteBuffer;
	import java.nio.channels.FileChannel;
	import java.nio.charset.Charset;
	import java.text.SimpleDateFormat;
	import java.util.Iterator;
	import java.util.List;
	
	import org.jsoup.Jsoup;
	import org.jsoup.nodes.Document;
	import org.jsoup.nodes.Element;
	import org.jsoup.select.Elements;

	import com.overzealous.remark.Remark;
	import com.sun.syndication.feed.synd.SyndCategoryImpl;
	import com.sun.syndication.feed.synd.SyndContentImpl;
	import com.sun.syndication.feed.synd.SyndEntry;
	import com.sun.syndication.feed.synd.SyndFeed;
	import com.sun.syndication.io.SyndFeedInput;
	import com.sun.syndication.io.XmlReader;

	public class TestMd {

		/**
		 * @param args
		 */
		public static String readFile(String path) throws IOException {
			  FileInputStream stream = new FileInputStream(new File(path));
			  try {
			    FileChannel fc = stream.getChannel();
			    MappedByteBuffer bb = fc.map(FileChannel.MapMode.READ_ONLY, 0, fc.size());
			    /* Instead of using default, pass in a decoder. */
			    return Charset.forName("UTF-8").decode(bb).toString();
			  }
			  finally {
			    stream.close();
			  }
			}
		public static void main(String[] args) {
			// TODO Auto-generated method stub
			XmlReader reader = null;
			String dstPath = "./_post/";
			String srcImg = "http://blog.yypig.net/uploaded_images/";
			String dstImg = "/images/";
	
			try {
				String template = readFile("post.tpl");
				reader = new XmlReader(new File("blog-05-05-2013.xml"));
				SyndFeed feed = new SyndFeedInput().build(reader);
				System.out.println("Feed Link: " + feed.getLink());
				StringBuffer tags = new StringBuffer();
				
				for (Iterator i = feed.getEntries().iterator(); i.hasNext();) {
					SyndEntry entry = (SyndEntry) i.next();
					List<SyndCategoryImpl> list = entry.getCategories();
					boolean isPost = false;
					boolean isFirst = true;
					tags.setLength(0);
					for (Iterator<SyndCategoryImpl> i1=list.iterator();i1.hasNext();){
						SyndCategoryImpl cat = i1.next();
						if (cat.getTaxonomyUri().contains("#kind")){
							if (cat.getName().contains("#post")){
								isPost=true;
							}
						}
						else if (cat.getTaxonomyUri().contains("ns#")){
							if (isFirst){
								tags.append(cat.getName());
								isFirst=false;
							}
							else{
								tags.append(", "+cat.getName());
							}
						}
						
					}
					if (isPost){
						String title = entry.getTitle();
						//title = title.replace("[", "(");
						//title = title.replace("]", ")");
						String tags1= tags.toString();
						String fileName;
						String published;
						SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd");
						String publishDate = sdf1.format(entry.getPublishedDate());
						if (entry.getLink()!=null){
							System.out.println(entry.getLink());
							URL url = new URL(entry.getLink());
							File f1 = new File(dstPath+url.getPath());
							int index = f1.getName().lastIndexOf(".");
	
							if (-1 == index){
								fileName = f1.getName()+".md";
							}
							else{
								fileName = f1.getName().substring(0, index)+".md";
							}
							fileName = f1.getParent()+File.separator+publishDate+"-"+fileName;
							System.out.println(fileName);
							published = "true";
						}
						else{
							SimpleDateFormat sdf = new SimpleDateFormat("yyyy/MM/");
							fileName = dstPath+sdf.format(entry.getPublishedDate())+publishDate
							+"-"+Integer.toHexString(entry.getTitle().hashCode())+".md";
							System.out.println(fileName);
							published = "false";
						}
						boolean hasPost = false;
						String content1="";
						String summary="";
						List<SyndContentImpl> list1=entry.getContents();
						for (Iterator<SyndContentImpl> i2 = list1.iterator();i2.hasNext();){
							SyndContentImpl content = i2.next();
							if (content.getType().equals("html")){
								//StringWriter writer = new StringWriter();
								Remark remark = new Remark();
								content1 = remark.convertFragment(content.getValue());
								content1 = content1.replace(srcImg, dstImg);
								System.out.println(content1);
								summary = Jsoup.parse(content.getValue()).text();
								if (summary.length()>120){
									summary = summary.substring(0,120);
									summary = summary.replace("\\", "\\\\");
									summary = summary.replace("\"", "\\\"");
								}
	//							Document doc = Jsoup.parse(content.getValue());
	//							Elements elements = doc.select("img");
	//							for (Iterator<Element> it2 = elements.iterator();it2.hasNext();){
	//								Element element = it2.next();
	//								System.out.println(element.attr("src"));
	//							}
								//content1 = writer.toString();
								hasPost = true;
								break;
							}
						}
						if (hasPost){
							String finalContent = template;
							finalContent = finalContent.replace("${title}", title);
							finalContent = finalContent.replace("${published}", published);
							finalContent = finalContent.replace("${tags}", tags1);
							finalContent = finalContent.replace("${summary}", summary);
							finalContent = finalContent.replace("${content}", content1);
							File newFile = new File(fileName);
							if (!newFile.getParentFile().exists()){
								newFile.getParentFile().mkdirs();
							}
							FileWriter writer2 = new FileWriter(newFile);
							writer2.write(finalContent);
							writer2.close();
						}
					}
				}
			}
			catch (Exception e){
				e.printStackTrace();
			}
			finally {
				if (reader != null){
					try {
						reader.close();
					}
					catch (Exception e1){
						e1.printStackTrace();
					}	
				}
					
			}
		}
	
	}
{% endhighlight %} 
用到的jar的列表：

rome-1.0.jar  
jdom-1.1.3.jar  
remark-0.9.3.jar  
jsoup-1.6.1.jar  
commons-cli-1.2.jar  
commons-lang3-3.1.jar  


