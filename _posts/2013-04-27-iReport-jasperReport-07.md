---
layout: post
title: "iReport+jasperReport之NoXML"
tagline: "Series of articles of iReport and JasperReport "
description: "说说jasperReport使用纯API如何构建jasper的"
category: iReport
tags: iReport、jasperReport
---

jasperReport的这个包net.sf.jasperreports.engine.design 是这样描述的：  
`Contains design time implementations for the library's main interfaces as well as report compiling tools.`  
因此我们完全可以使用它的API构建自己的报表，还是和前几篇使用同一个数据库表。  

***构建JasperDesign对象：***   
设置一些对应的属性：  
	
	// JasperDesign
	JasperDesign jasperDesign = new JasperDesign();
	jasperDesign.setName("NoXmlDesignReport");
	jasperDesign.setPageWidth(595);
	jasperDesign.setPageHeight(842);
	jasperDesign.setColumnWidth(515);
	jasperDesign.setColumnSpacing(0);
	jasperDesign.setLeftMargin(40);
	jasperDesign.setRightMargin(40);
	jasperDesign.setTopMargin(50);
	jasperDesign.setBottomMargin(50);
	
字体：  
	
	// Fonts
	JRDesignStyle normalStyle = new JRDesignStyle();
	normalStyle.setName("Arial_Normal");
	normalStyle.setDefault(true);
	normalStyle.setFontName("Arial");
	normalStyle.setFontSize(12);
	normalStyle.setPdfFontName("Helvetica");
	normalStyle.setPdfEncoding("Cp1252");
	normalStyle.setPdfEmbedded(false);
	jasperDesign.addStyle(normalStyle);

	JRDesignStyle boldStyle = new JRDesignStyle();
	boldStyle.setName("Arial_Bold");
	boldStyle.setFontName("Arial");
	boldStyle.setFontSize(12);
	boldStyle.setBold(true);
	boldStyle.setPdfFontName("Helvetica-Bold");
	boldStyle.setPdfEncoding("Cp1252");
	boldStyle.setPdfEmbedded(false);
	jasperDesign.addStyle(boldStyle);

	JRDesignStyle italicStyle = new JRDesignStyle();
	italicStyle.setName("Arial_Italic");
	italicStyle.setFontName("Arial");
	italicStyle.setFontSize(12);
	italicStyle.setItalic(true);
	italicStyle.setPdfFontName("Helvetica-Oblique");
	italicStyle.setPdfEncoding("Cp1252");
	italicStyle.setPdfEmbedded(false);
	jasperDesign.addStyle(italicStyle);
	
定义报表的参数，并添加到报表设计器上  
	
	// Parameters
	JRDesignParameter parameter = new JRDesignParameter();
	parameter.setName("ReportTitle");
	parameter.setValueClass(java.lang.String.class);
	jasperDesign.addParameter(parameter);  
	
定义报表的字段，这些字段是最终显示到报表上的域  
	
	// Fields
	JRDesignField field = new JRDesignField();
	field.setName("userid");
	field.setValueClass(java.lang.Integer.class);
	jasperDesign.addField(field);

	field = new JRDesignField();
	field.setName("name");
	field.setValueClass(java.lang.String.class);
	jasperDesign.addField(field);

	field = new JRDesignField();
	field.setName("sex");
	field.setValueClass(java.lang.String.class);
	jasperDesign.addField(field);

	field = new JRDesignField();
	field.setName("age");
	field.setValueClass(java.lang.String.class);
	jasperDesign.addField(field);

	field = new JRDesignField();
	field.setName("password");
	field.setValueClass(java.lang.String.class);
	jasperDesign.addField(field);

	field = new JRDesignField();
	field.setName("department");
	field.setValueClass(java.lang.String.class);
	jasperDesign.addField(field);

	JRDesignBand band = null;
	JRDesignTextField textField = null;
	JRDesignExpression expression = null;  
	
开始画报表主体的title部分，不需要的部分可以不用代码标识出来，title部分放置了报表标题字段，只会出现在第一页的上方  
	
	// title
	band = new JRDesignBand();
	band.setHeight(50);
	textField = new JRDesignTextField();
	textField.setBlankWhenNull(true);
	textField.setX(0);
	textField.setY(10);
	textField.setWidth(500);
	textField.setHeight(30);
	textField.setHorizontalAlignment(JRAlignment.HORIZONTAL_ALIGN_CENTER);
	textField.setStyle(normalStyle);
	textField.setFontSize(22);
	expression = new JRDesignExpression();
	expression.setValueClass(java.lang.String.class);
	expression.setText("$P{ReportTitle}");
	textField.setExpression(expression);
	band.addElement(textField);
	jasperDesign.setTitle(band);  
	
detail部分：排列域的位置，并设置它的值，没一个域的值会最为一个 JRDesignExpression出现  
	
	// Detail
	// pid
	band = new JRDesignBand();
	band.setHeight(20);

	textField = new JRDesignTextField();
	textField.setX(5);
	textField.setY(4);
	textField.setWidth(100);
	textField.setHeight(15);
	textField.setHorizontalAlignment(JRAlignment.HORIZONTAL_ALIGN_RIGHT);
	textField.setStyle(normalStyle);
	expression = new JRDesignExpression();
	expression.setValueClass(java.lang.Integer.class);
	expression.setText("$F{userid}");
	textField.setExpression(expression);
	band.addElement(textField);
	// name
	textField = new JRDesignTextField();
	textField.setStretchWithOverflow(true);
	textField.setX(120);
	textField.setY(4);
	textField.setWidth(80);
	textField.setHeight(15);
	textField.setPositionType(JRElement.POSITION_TYPE_FLOAT);
	textField.setStyle(normalStyle);
	expression = new JRDesignExpression();
	expression.setValueClass(java.lang.String.class);
	expression.setText("$F{name}");
	textField.setExpression(expression);
	band.addElement(textField);

	// sex
	textField = new JRDesignTextField();
	textField.setStretchWithOverflow(true);
	textField.setX(200);
	textField.setY(4);
	textField.setWidth(30);
	textField.setHeight(15);
	textField.setPositionType(JRElement.POSITION_TYPE_FLOAT);
	textField.setStyle(normalStyle);
	expression = new JRDesignExpression();
	expression.setValueClass(java.lang.String.class);
	expression.setText("$F{sex}");
	textField.setExpression(expression);
	band.addElement(textField);
	// age
	textField = new JRDesignTextField();
	textField.setStretchWithOverflow(true);
	textField.setX(240);
	textField.setY(4);
	textField.setWidth(30);
	textField.setHeight(15);
	textField.setPositionType(JRElement.POSITION_TYPE_FLOAT);
	textField.setStyle(normalStyle);
	expression = new JRDesignExpression();
	expression.setValueClass(java.lang.String.class);
	expression.setText("$F{age}");
	textField.setExpression(expression);
	band.addElement(textField);
	// password
	textField = new JRDesignTextField();
	textField.setStretchWithOverflow(true);
	textField.setX(270);
	textField.setY(4);
	textField.setWidth(80);
	textField.setHeight(15);
	textField.setPositionType(JRElement.POSITION_TYPE_FLOAT);
	textField.setStyle(normalStyle);
	expression = new JRDesignExpression();
	expression.setValueClass(java.lang.String.class);
	expression.setText("$F{password}");
	textField.setExpression(expression);
	band.addElement(textField);
	// department
	textField = new JRDesignTextField();
	textField.setStretchWithOverflow(true);
	textField.setX(350);
	textField.setY(4);
	textField.setWidth(80);
	textField.setHeight(15);
	textField.setPositionType(JRElement.POSITION_TYPE_FLOAT);
	textField.setStyle(normalStyle);
	expression = new JRDesignExpression();
	expression.setValueClass(java.lang.String.class);
	expression.setText("$F{department}");
	textField.setExpression(expression);
	band.addElement(textField);  
	
JasperReport对象可以使用下面这句产生：  
	
	JasperCompileManager.compileReport(jasperDesign);
	
至此一个完整的报表就可以显示出来了。