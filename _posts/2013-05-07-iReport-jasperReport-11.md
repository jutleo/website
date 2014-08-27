---
layout: post
title: "iReport+jasperReport之图片控件"
tagline: "Series of articles of iReport and JasperReport "
description: "iReport+jasperReport之JFreeChart（图表报表） 中提到图片控件，下面就看看jasperReport怎样处理图片吧！"
category: iReport
tags: iReport、jasperReport
---

说完[iReport+jasperReport之JFreeChart（图表报表）][images_jasper]中提到图片控件，下面就看看jasperReport怎样处理图片吧！
新建一空白报表，分别画两个静态文本字段和图像控件上去。
![显示效果](/static/img/20130507001.jpg) 

新建两个参数分别为：`imageParam`和`isShowImage`，其中`imageParam`用来指定图片路径，`isShowImage`用来指定是否显示图片的。  

右击查看图片控件属性：分别设置图片参数和控制打印的表达式：
![显示效果](/static/img/20130507002.jpg)  
![显示效果](/static/img/20130507003.jpg)  

ok！这个就不用解释了吧！试试就知道啦  
	
	JasperReport jasperReport = (JasperReport) JRLoader
                    .loadObject("D:\\workspace\\AppletTest.jasper");
            HashMap mapParam = new HashMap();
            mapParam.put("imageParam", "D:\\workspace\\eg_smile.gif");
            /*
             * 此参数用来控制是否显示图片
             * 第二个参数在报表中设置为String类型
             */
            mapParam.put("isShowImage", "true");
            // 生成jasperPrint对象
            JasperPrint jasperPrint = JasperFillManager.fillReport(jasperReport,
                    mapParam, new JREmptyDataSource());
					
关于jasperReport图片处理很简单google一下很多啊，最近又开始忙了突然想起上篇遗留这个问题，
所以抽空写下来，算是对上篇的一个补充吧！以后有时间会继续写下去，国际化、corsstab、web端打印、纯java定制复杂报表等。
	
[images_jasper]: http://jutleo.github.io/2013/05/07/iReport-jasperReport-11/