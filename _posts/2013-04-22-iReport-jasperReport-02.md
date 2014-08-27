---
layout: post
title: "iReport+jasperReport概念的澄清二"
tagline: "Series of articles of iReport and JasperReport "
description: "JasperReport有三个域用来存放、展示数据，Parameter、Field、Variables，这次说说这三个吧！"
category: iReport
tags: iReport、jasperReport
---

**JasperReport有三个域用来存放、展示数据，Parameter、Field、Variables，这次说说这三个吧！**

###Report Parameters


JasperReport 报表的参数是最为一个object类型的参数传递的，通常我们在jrxml文件中会这样定义  
	
	<parameter name="ReportTitle" class="java.lang.String"/>
	
顾名思义，参数是用来传递给报表的，通常我们会动态的传递一个参数给报表作为报表的标题，传递给自报表一个参数用来作为自报表查询的条件等等。
jasperReport内置了几个有用的参数：REPORT_SCRIPTLET引用外部的scriptlet，REPORT_LOCALE引用国际化preperty文件使用的，
REPORT_CONNECTION传递报表JDBC连接参数使用的等等。  
	
	
	public Map getMap() {
        Map map = new HashMap();
        map.put("reportTitle", "laoshulin");
        return map;
    }
	
程序运行的时候就会动态的赋值给ReportTitle这个参数,下面这个是报表运行时需要的connection参数，是动态传递给子报表的。


	<connectionExpression><![CDATA[$P{REPORT_CONNECTION}]]></connectionExpression>
	
我们在程序运行的时候的时候可以把一个打开的连接传给REPORT_CONNECTION参数：  
	
	parameters.put("REPORT_CONNECTION", getConnection());  
	
###Report Field

field是用来展现数据的域，也是最常用的一个，采用JDBC时iReport可以在我们写入SQL时自动检测到所有的field，
有时我们需要自己手动建立一些field，这个字段没有什么好说的，我们可以在iReport设置它的各种属性。以后碰到的时候在说。  

###Report Variables 

提起Variables不能不提expression，report expression是jasperReport一个非常实用的组件，它可以用来执行各种计算，修饰各个字段的数据。如:  

	
	<textFieldExpression>
    $F{FirstName} + " " + $F{LastName}
	</textFieldExpression>

	<textFieldExpression>
		"Max Order ID is : " + $P{MaxOrderID}
	</textFieldExpression>  
	
报表变量是建立在表达式上的一个特殊的用来简化报表设计，一个变量可以执行内置类型的计算以及相应表达式，如：总数，总和、平均数、最低值、最高值、差额等等。  
	
	<variable name="QuantitySum" 
        class="java.lang.Double" calculation="Sum">
		<variableExpression>$F{Quantity}</variableExpression>
	</variable>  
	
JasperReport内置了一些变量  
PAGE_NUMBER 页数  
COLUMN_NUMBER 列数  
REPORT_COUNT 报表总数  
PAGE_COUNT  当前页数  
COLUMN_COUNT 列总数  

值得一提的是JasperReport还有一个比较强大的功能就是parameter/field/variables都支持html语言  
![HTML支持](/static/img/20130422004.jpg)    
设置Markup为HTML时就可以在Text Field Expression 中写入html标记了  
	
	<font color='blue' size='5'>"+$P{reportTitle}+"</font>
	
当报表预览的时候就可以直接看到html的效果了。  
	