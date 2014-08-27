---
layout: post
title: "iReport+jasperReport概念的澄清"
tagline: "Series of articles of iReport and JasperReport "
description: "项目中学到的一些jasperReport的东东,先说点基础的概念吧！ "
category: iReport
tags: iReport、jasperReport
---

###基本概念

从[这个网址][jasperReport]就可以得到ireport+jasperReport，注意下载iReport和jasperReport的版本必须一致。  
我们所说的报表指的是JasperReport，iReport只是jasperReport的一个可视化的开发工具，JasperReport通过读取xml文件生成报表，分为三个部分：  
1. 编写xml文件也就是jrxml文件;  
2. 读取并编译jrxml--->jasper文件;  
3. 填充报表;  

而iReport只是帮我们完成了一个可视化的编写jrxml文件，之后在我后面的文章中还会看到直接使用jasperReport的JasperDesign设计器也可以构造出一个没有jrxml文件的复杂报表。
当然iReport这个工具还给我们提供了不少优秀的功能，编译、预览、复杂报表的设计。  

###基本设置  

打开iReport的“Options” -“ 选项“--"General",默认的中文好像并不是我们想象的那样好看，建议使用english。
"Compiler"标签的Default Compilation Directory 设置一个你自己的路径，也可以不设置，
这个主要是存放编译好的jasper文件的路径的，笔者不太喜欢文件的乱存放。"External Programs" 选择使用什么样的程序浏览报表，
默认是使用iReport自身的JRViewer Previwer预览的，我们可以指定自己的pdf，html等其它的预览工具。

###数据源设置

将数据库使用的jdbc驱动包复制到iReport安装路径的lib文件夹下，新建一个connections/DataSource  
![数据源设置](/static/img/20130422001.jpg)  
JaspreReport支持很多中数据源之后在详细介绍每一种数据源的使用  
![数据源设置](/static/img/20130422002.jpg)  

###PDF报表的中文处理  
jasperreport支持中文依赖iText.jar，报表设计时将字体设计为：  
![数据源设置](/static/img/20130422003.png)  
网上找一些这方面的资料很容易就可以利用报表向导做一个简单的报表，
[这位哥们](http://www.blogjava.net/fastunit/archive/2008/01/16/175687.html)写的就很不错，截图都有了
[jasperReport API](http://jasperreports.sourceforge.net/api/index.html)这是jasperReport的api地址，大家可以利用它的源码自己生成api。 




[jasperReport]:http://jasperforge.org/