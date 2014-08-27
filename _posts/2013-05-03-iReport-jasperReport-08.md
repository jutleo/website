---
layout: post
title: "iReport+jasperReport之scriptlet"
tagline: "Series of articles of iReport and JasperReport "
description: "jasperReport如何扩充逻辑scriptlet"
category: iReport
tags: iReport、jasperReport
---

提起scriptlet就不能不联想到它的强大功能，jasperReport也是支持scriptlet的哦，先分析一下JasperReport的API吧！
在填充报表时scriplet是一个非常有力的工具，JRAbstractScriptlet.java位于net.sf.jasperreports.engine包下是一个抽象类。  

![显示效果](/static/img/20130502001.jpg)   

`beforeReportInit()`, `afterReportInit()`, `beforePageInit()`, `afterPageInit()`, `beforeColumnInit()`, `afterColumnInit()`, 
`beforeGroupInit(String groupName)`, `afterGroupInit(String groupName)`   

看看这些名字就知道你能完成那些功能，这几个方法是要求我们实现的，jasperReport给我们提供了一个实现类JRDefaultScriptlet.java，
默认的空实现了上面几个方法，它只是很便利的为我们提供了所需的八个方法的空实现。
我们写自己的scriptlet时需要继承JRDefaultScriptlet.java这个类实现自己的相应的功能即可。  

先来看一个简单的例子：  
![显示效果](/static/img/20130502002.jpg)   

先看看模板文件的处理：  

新建时填写的这个类是下面我们要介绍的继承自JRDefaultScriptlet.java类，也就是在模板文件中我们要加上如下代码  
	
	scriptletClass="org.bulktree.ireport.scriptlet.ScriptletReportDemo"  
	
完整的模板文件如下：scriptletDemo.jrxml  
	
	<?xml version="1.0" encoding="UTF-8"  ?>
	<!-- Created with iReport - A designer for JasperReports -->
	<!DOCTYPE jasperReport PUBLIC "//JasperReports//DTD Report Design//EN" "http://jasperreports.sourceforge.net/dtds/jasperreport.dtd">
	<jasperReport
			 name="scriptletDemo"
			 columnCount="1"
			 printOrder="Vertical"
			 orientation="Portrait"
			 pageWidth="595"
			 pageHeight="842"
			 columnWidth="535"
			 columnSpacing="0"
			 leftMargin="30"
			 rightMargin="30"
			 topMargin="20"
			 bottomMargin="20"
			 whenNoDataType="NoPages"
			 scriptletClass="org.bulktree.ireport.scriptlet.ScriptletReportDemo"
			 isTitleNewPage="false"
			 isSummaryNewPage="false">
		<property name="ireport.scriptlethandling" value="2" />
		<property name="ireport.encoding" value="UTF-8" />
		<import value="java.util.*" />
		<import value="net.sf.jasperreports.engine.*" />
		<import value="net.sf.jasperreports.engine.data.*" />

		<parameter name="ReportTitle" isForPrompting="true" class="java.lang.String"/>

			<background>
				<band height="0"  isSplitAllowed="true" >
				</band>
			</background>
			<title>
				<band height="20"  isSplitAllowed="true" >
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							mode="Opaque"
							x="193"
							y="0"
							width="134"
							height="18"
							backcolor="#FFCC33"
							key="textField"/>
						<box></box>
						<textElement textAlignment="Center" verticalAlignment="Middle">
							<font pdfFontName="Helvetica-Bold" size="12" isBold="true"/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA[$P{ReportTitle}]]></textFieldExpression>
					</textField>
				</band>
			</title>
			<pageHeader>
				<band height="0"  isSplitAllowed="true" >
				</band>
			</pageHeader>
			<columnHeader>
				<band height="0"  isSplitAllowed="true" >
				</band>
			</columnHeader>
			<detail>
				<band height="264"  isSplitAllowed="true" >
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="85"
							y="20"
							width="329"
							height="61"
							forecolor="#FF0099"
							key="textField-1"/>
						<box></box>
						<textElement textAlignment="Center" verticalAlignment="Middle">
							<font pdfFontName="Helvetica-Bold" size="20" isBold="true"/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA[$P{REPORT_SCRIPTLET}.showInfor()]]></textFieldExpression>
					</textField>
				</band>
			</detail>
			<columnFooter>
				<band height="0"  isSplitAllowed="true" >
				</band>
			</columnFooter>
			<pageFooter>
				<band height="0"  isSplitAllowed="true" >
				</band>
			</pageFooter>
			<summary>
				<band height="50"  isSplitAllowed="true" >
				</band>
			</summary>
	</jasperReport>
	
下来看看怎么实现我们自己的scriplet    
	
**ScriptletReportDemo.java**
	
	package org.bulktree.ireport.scriptlet;

	import net.sf.jasperreports.engine.JRDefaultScriptlet;
	import net.sf.jasperreports.engine.JRScriptletException;

	/**
	 * 
	 * @author bulktree Email: jutleo@gmail.com
	 * @Nov 26, 2008
	 */
	public class ScriptletReportDemo extends JRDefaultScriptlet {

		@Override
		public void afterColumnInit() throws JRScriptletException {
			System.out.println("**************************************afterColumnInit()**************************************");
		}

		@Override
		public void afterDetailEval() throws JRScriptletException {
			System.out.println("**************************************afterDetailEval()**************************************");
		}

		@Override
		public void afterGroupInit(String groupName) throws JRScriptletException {
			System.out.println("**************************************afterDetailEval()**************************************");
		}

		@Override
		public void afterPageInit() throws JRScriptletException {
			System.out.println("**************************************afterPageInit()**************************************");
		}

		@Override
		public void afterReportInit() throws JRScriptletException {
			System.out.println("**************************************afterReportInit() begin**************************************");
			
			String str = (String) this.getParameterValue("ReportTitle");
			System.out.println("report title=====>>>>"+str);
			
			str += str.subSequence(0, str.length()-2);
			this.setParameterValue("ReportTitle", str);
			System.out.println("**************************************afterReportInit() end**************************************");
		}

		@Override
		public void beforeColumnInit() throws JRScriptletException {
			System.out.println("**************************************beforeColumnInit()**************************************");
		}

		@Override
		public void beforeDetailEval() throws JRScriptletException {
			System.out.println("**************************************beforeDetailEval()**************************************");
		}

		@Override
		public void beforeGroupInit(String groupName) throws JRScriptletException {
			System.out.println("**************************************beforeGroupInit()**************************************");
		}

		@Override
		public void beforePageInit() throws JRScriptletException {
			System.out.println("**************************************beforePageInit()**************************************");
		}

		@Override
		public void beforeReportInit() throws JRScriptletException {
			System.out.println("**************************************beforeReportInit()**************************************");
		}

		public String showInfor() throws JRScriptletException {
			return "the is scriptlet scriptlet scriptlet the,sscriptlet report the is ascriptlet report this is a scriptlet report this is a scriptlet report";
		}
	}
	
这段代码最后一个方法是我们自己的加的，用来在报表上显示一段文本。
我们知道对于一个Field、Parameter、Variable，JasperReport分别采用$F{FieldName}、$P{Parametername}、$V{VariableName}来引用，
而如果要引用ScriptletReportDemo.java类的showInfor()返回字符串显示在报表上，看看这个就知道了   

![显示效果](/static/img/20130502003.jpg)  

在afterReportInit方法中我们把parameter字段取出来前后添上五个*号再set进去   
下来写一个test类测试一下：  
	
	package org.bulktree.ireport.scriptlet;

	import java.io.File;
	import java.io.FileInputStream;
	import java.io.InputStream;
	import java.util.HashMap;

	import net.sf.jasperreports.engine.JREmptyDataSource;
	import net.sf.jasperreports.engine.JasperCompileManager;
	import net.sf.jasperreports.engine.JasperFillManager;
	import net.sf.jasperreports.engine.JasperPrint;
	import net.sf.jasperreports.engine.JasperReport;
	import net.sf.jasperreports.view.JasperViewer;

	/**
	 * 
	 * @author bulktree Email: jutleo@gmail.com
	 * @Nov 27, 2008
	 */
	public class ScriptletTestMain {

		public static void main(String[] args) {
			String path = "D:/workspace/scriptletDemo.jrxml";

			File file = new File(path);
			InputStream in;
			try {

				HashMap parameters = new HashMap();
				parameters.put("ReportTitle", "LAOSHULIN");
				in = new FileInputStream(file);
				JasperReport jasperReport = JasperCompileManager.compileReport(in);
				JasperPrint jasperPrint = JasperFillManager.fillReport(jasperReport,
						parameters, new JREmptyDataSource());
				JasperViewer viewer = new JasperViewer(jasperPrint);
				viewer.setVisible(true);

			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}

		}
	}
	
效果不错吧：  

![显示效果](/static/img/20130502004.jpg)