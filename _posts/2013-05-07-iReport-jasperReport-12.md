---
layout: post
title: "iReport+jasperReport之i18n国际化支持"
tagline: "Series of articles of iReport and JasperReport "
description: "jasperReport对国际化的支持是很到位的，但是国内基本不怎么使用，下来看看国际化的使用吧！"
category: iReport
tags: iReport、jasperReport
---

新建一空白报表，还是和以前一致 添加reportTitle参数，添加一个图片控件、几个textField控件用来显示国际化内容：  

![显示效果](/static/img/20130507004.jpg)  
选择report propertys 对话框

![显示效果](/static/img/20130507005.jpg)  

这个一看就明白了，资源文件名字为I18nReportDemo，只要classPath可以找到就可以了；资源文件大致内容如下：  
	
	# Locale i18n_zh_CN for report I18nReportDemo.jrxml
	text.section1=\u8fd9\u662fIreport+JasperReport i18n\u7684\u4e00\u4e2ademo
	text.section2=\u56fd\u9645\u5316\u662f\u4e2a\u4ec0\u4e48\u73a9\u610f\u561b
	text.section3=Struts2-jasperReport-plugin.jar\u4e0d\u77e5\u9053\u662f\u4e0d\u662f\u8fd9\u6837\u7684bug\uff0c\u4f46\u662f\u6211\u4fee\u6539\u540e\u5c31\u53ef\u4ee5\u4f7f\u7528\u4e86
	image.url=eg_smile.gif
	text.showInfor=\u56fd\u9645\u5316\u6211\u89c9\u5f97\u9700\u6c42\u4e0d\u5927\uff0c\u4e0d\u8fc7\u597d\u50cf\u7ed3\u5408scriptlet\u4f7f\u7528\u53ef\u4ee5\u4f7fJasperReport\u7684\u529f\u80fd\u66f4\u52a0\u5f3a\u5927
	text.contributors=Author-bulktree,\nLAOSHULIN,\nwww.blogjava.net/bulktree  
	
有一点很重要，iReport使用$R{key}符号引用资源文件对应的key，前面提到的图片控件已用图片也可以使用这种方式简单的引用，资源文件可以使用\n换行。  
** 还是那几行通用的代码：**  
	
	JasperReport jasperReport = JasperCompileManager.compileReport(path);
        HashMap parameters = new HashMap();
        parameters.put("ReportTitle", "LAOSHULIN");
        JasperPrint jasperPrint = JasperFillManager.fillReport(jasperReport, parameters, new JREmptyDataSource());
        JasperViewer jv = new JasperViewer(jasperPrint);
        jv.setVisible(true);
		
![显示效果](/static/img/20130507006.jpg) 

iReport 给我们提供了很多的便利，只要发掘你会有意想不到的收获。  