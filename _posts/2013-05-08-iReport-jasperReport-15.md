---
layout: post
title: "iReport+jasperReport之客户端打印 (续二) ----数字签名"
tagline: "Series of articles of iReport and JasperReport "
description: "接着上篇，jasperReport 实现客户端主要是依靠applet，但是我们所有的操作不可能在applet中实现吧，这样也不算一个好的应用。"
category: iReport
tags: iReport、jasperReport
---

接着上篇，Java沙箱是运行Java小应用程序的一个软件单元。对Java小应用程序的访问权限加以限制，防止它访问计算机的关键部分。
如磁盘驱动器、网络套接口和内存区等。jDK的 security API 提供对小应用程序进行数字签名来达到和application 一样的安全。  
客户端打印采用applet会访问打印机，数字签名就成了必须，下面我们来看看如何制作数字签名：  

**`JDK`的`keytool`命令是安全钥匙和证书的管理工具，我们常用的命令如下：**
	-genkey      产生密钥文件,文件中包含用户的公钥、私钥和证书
	-alias          产生别名
	-keystore    指定密钥仓库名称
	-validity      指定创建的证书有效期多少天
	-storepass   指定密钥库的密码
	-dname       指定证书拥有者信息 例如：  "CN=sagely,OU=atr,O=szu,L=sz,ST=gd,C=cn"
	-list            列出系统证书仓库中存在证书名称列表
	-export      将别名指定的证书导出到文件  keytool -export -alias caroot -file caroot.crt
	-file           参数指定导出到文件的文件名
	-delete       删除系统证书库的证书
	-import      导入证书到密钥仓库   
	
以下是生成客户端证书、并对jasperreports-3.0.1-applet.jar  包做签名的命令：  
	
	
	keytool -genkey -validity 1800 -keystore applet.store -alias applet   
	keytool -export -keystore applet.store -alias applet -file applet.cer
	jarsigner -keystore applet.store jasperreports-3.0.1-applet.jar  applet  
	
根据提示输入相关信息及密码即可完成签名的制作。此时applet在第一次访问时浏览器会提示签名信息，至此整个客户端可以安全的访问了。
时间仓卒，介绍的不是很详细 关于`keytool`工具的时候网上有很多，也比较详细，本文仅是对客户端applet打印的一点补充。