---
layout: post
title: "正则表达式贪婪、非贪婪模式"
tagline: "regular expression"
categories: blog
description: "regular expression -- 贪婪、非贪婪"
tags: 贪婪、非贪婪
---

####贪婪与懒惰
  当正则表达式中包含能接受重复的限定符时，通常的行为是（在使整个表达式能得到匹配的前提下）匹配尽可能多的字符，也就是说整个匹配过程的中的`\D`一直在匹配，直到发现最后一个`'`才结束。这在正则中被叫做贪婪模式！ 当然有了贪婪模式，就有非贪婪（懒惰）模式，也就是匹配尽可能少的字符，只要在它后面加上一个`?`。

	String TSQL = "update DsptVO set CNewFlg='0' where id.CRptId=':rptId' and id.CTaskNo=':taskNo';

  如上SQL，我们的本意是替换`':rptId'` 为`:rptId`,就是去掉'而已，一般会写如下正则：

	TSQL = TSQL.replaceAll("':(\\D+)'", ":$1");
  表示从`':`,开始,之后是任意多个字符，最后是一个`'`结束,替换结果为：

	update DsptVO set CNewFlg='0' where id.CRptId=:rptId' and id.CTaskNo=':taskNo' and CNewFlg=1'
  我们发现单引号的结束竟然匹配到最后一个，这是怎么回事呢？

  

  	TSQL = TSQL.replaceAll("':(\\D+?)'", ":$1");
  表示由从`':`,开始,之后是任意多个字符，最后是一个`'`结束，但是是懒惰模式，找到最近的`'`后就不结束了。最后：

  	System.out.println(TSQL);
  这应该是我们想要的结果吧：
        
  	update DsptVO set CNewFlg='0' where id.CRptId=:rptId and id.CTaskNo=:taskNo and CNewFlg='1'

