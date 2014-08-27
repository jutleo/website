---
layout: post
title: "iReport+jasperReport之JDBC数据源"
tagline: "Series of articles of iReport and JasperReport "
description: "iReport画出来的报表可以满足我们大部分的需要，所以采用iReport 编辑jrxml文件是我们的首选啦！当然掌握jrxml文件的结构也是必须的。采用JDBC数据源必须有数据库支持啊！"
category: iReport
tags: iReport、jasperReport
---


iReport画出来的报表可以满足我们大部分的需要，所以采用iReport 编辑jrxml文件是我们的首选啦！当然掌握jrxml文件的结构也是必须的。  
采用JDBC数据源必须有数据库支持啊！我们新建一个test表，其中有pid、name、sex、password、department、age字段，为了方便期间我们定义这些字段全部为String类型。
在iReport中新建一报表，报表有title、pageHeader、columnHeader、detail、columnFooter、pageFooter、lastPageFooter、summary等部分，被成为`Report section`  

title：顾名思义是指报表的标题哦，它会显示在报表的最上面，如果有多页只会出现在第一页的最上面。  
pageHeader：每页的标题，如果有多页每页的开始都会显示此部分内容。  
columnHeader：显示报表的列头不经常使用。  
detail：这个就不用说了吧！  
有header就会出现footer啦！  
lastPageFooter只会出现在最后一页。  
summay会出现在每一页数据上，主要是为了展示一些统计信息，比如当前的时间，页数信息啦！  

新建一parameter  
![新建parameter](/static/img/20130422005.jpg)   
此参数可作为报表的标题使用，我们在程序中动态的传递给报表。  
document structure－－－》parameter里找到reportTitle参数拖至title区域，右击编辑reportTitle域的属性，
在font选项里找到Markup设置为HTML，TextField选项里设置Text Field Expreesion为  
	
	"<font color='blue' size='5'>"+$P{reportTitle}+"</font>"
	
下来就是设置报表的Field字段了，不需要我们一个个的新建那些字段啦！  
选择Data--->Report Query在Report Query选项里选择Query Language为SQL，写入SqL语句  
	
	select * from test order by pid DESC
	
这时所有的field会出现在下面(SQL是正确的)  
关闭对话框在document structure－－－》field中就会出现我们需要的field，之后拖到相应的位置，关于怎么美化报表这个本人也不是很懂哦！  
如果需要显示一下当前的页数信息也可以自己托动Variables里的PAGE_NUMBER完成页数的显示。完整的jrxml文件如下：  
	
	<?xml version="1.0" encoding="UTF-8"  ?>
	<!-- Created with iReport - A designer for JasperReports -->
	<!DOCTYPE jasperReport PUBLIC "//JasperReports//DTD Report Design//EN" "http://jasperreports.sourceforge.net/dtds/jasperreport.dtd">
	<jasperReport
			 name="Person"
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
			 isTitleNewPage="false"
			 isSummaryNewPage="false">
		<property name="ireport.scriptlethandling" value="0" />
		<property name="ireport.encoding" value="UTF-8" />
		<import value="java.util.*" />
		<import value="net.sf.jasperreports.engine.*" />
		<import value="net.sf.jasperreports.engine.data.*" />

		<parameter name="reportTitle" isForPrompting="true" class="java.lang.String"/>
		<queryString><![CDATA[select * from test order by pid DESC]]></queryString>

		<field name="pid" class="java.lang.String"/>
		<field name="name" class="java.lang.String"/>
		<field name="sex" class="java.lang.String"/>
		<field name="password" class="java.lang.String"/>
		<field name="department" class="java.lang.String"/>
		<field name="age" class="java.lang.String"/>

			<background>
				<band height="6"  isSplitAllowed="true" >
				</band>
			</background>
			<title>
				<band height="29"  isSplitAllowed="true" >
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="138"
							y="0"
							width="258"
							height="29"
							key="textField"/>
						<box></box>
						<textElement textAlignment="Center" verticalAlignment="Middle" markup="html">
							<font pdfFontName="Helvetica-Bold" isBold="true"/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA["<font color='blue' size='5'>"+$P{reportTitle}+"</font>"]]></textFieldExpression>
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
				<band height="22"  isSplitAllowed="true" >
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="0"
							y="0"
							width="100"
							height="18"
							key="textField"/>
						<box></box>
						<textElement verticalAlignment="Top">
							<font/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA[$F{pid}]]></textFieldExpression>
					</textField>
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="100"
							y="0"
							width="93"
							height="18"
							key="textField"/>
						<box></box>
						<textElement verticalAlignment="Top">
							<font/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA[$F{name}]]></textFieldExpression>
					</textField>
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="193"
							y="0"
							width="58"
							height="18"
							key="textField"/>
						<box></box>
						<textElement verticalAlignment="Top">
							<font/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA[$F{sex}]]></textFieldExpression>
					</textField>
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="251"
							y="0"
							width="100"
							height="18"
							key="textField"/>
						<box></box>
						<textElement verticalAlignment="Top">
							<font/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA[$F{password}]]></textFieldExpression>
					</textField>
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="351"
							y="0"
							width="100"
							height="18"
							key="textField"/>
						<box></box>
						<textElement verticalAlignment="Top">
							<font/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA[$F{department}]]></textFieldExpression>
					</textField>
					<textField isStretchWithOverflow="false" pattern="" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="451"
							y="0"
							width="84"
							height="18"
							key="textField"/>
						<box></box>
						<textElement verticalAlignment="Top">
							<font/>
						</textElement>
					<textFieldExpression   class="java.lang.String"><![CDATA[$F{age}]]></textFieldExpression>
					</textField>
					<line direction="TopDown">
						<reportElement
							x="0"
							y="17"
							width="535"
							height="1"
							key="line-1"/>
						<graphicElement stretchType="NoStretch"/>
					</line>
				</band>
			</detail>
			<columnFooter>
				<band height="0"  isSplitAllowed="true" >
				</band>
			</columnFooter>
			<pageFooter>
				<band height="24"  isSplitAllowed="true" >
					<textField isStretchWithOverflow="false" isBlankWhenNull="false" evaluationTime="Now" hyperlinkType="None"  hyperlinkTarget="Self" >
						<reportElement
							x="387"
							y="5"
							width="22"
							height="18"
							key="textField"/>
						<box></box>
						<textElement textAlignment="Center" verticalAlignment="Middle">
							<font/>
						</textElement>
					<textFieldExpression   class="java.lang.Integer"><![CDATA[$V{PAGE_NUMBER}]]></textFieldExpression>
					</textField>
					<staticText>
						<reportElement
							x="373"
							y="5"
							width="14"
							height="17"
							key="staticText-2"/>
						<box></box>
						<textElement textAlignment="Center" verticalAlignment="Middle">
							<font pdfFontName="Helvetica-Bold" size="12" isBold="true"/>
						</textElement>
					<text><![CDATA[第]]></text>
					</staticText>
					<staticText>
						<reportElement
							x="409"
							y="5"
							width="14"
							height="17"
							key="staticText-3"/>
						<box></box>
						<textElement textAlignment="Center" verticalAlignment="Middle">
							<font pdfFontName="Helvetica-Bold" size="12" isBold="true"/>
						</textElement>
					<text><![CDATA[页]]></text>
					</staticText>
				</band>
			</pageFooter>
			<summary>
				<band height="0"  isSplitAllowed="true" >
				</band>
			</summary>
	</jasperReport>
	
我们可以直接使用ireport查看预览效果，但是大部分我们是在应用程序中使用的哦！我们看看我们怎么让这个jrxml模板文件工作呢？  
	
	File file = new File("Person.jrxml");
	InputStream in = new FileInputStream(file); // 编译报表 jasperReport =
	jasperReport = JasperCompileManager.compileReport(in);
		
编译文件是很耗时的工作，通常这个由iReport去做我们使用jasper文件即可，  
	
	jasperReport = (JasperReport) JRLoader
                    .loadObject("D:\\workspace\\Person.jasper");
					
产生了JasperReport对象下来就是要填充数据了，采用JDBC方式我们需要一个打开的connection(数据库连接)，  
还有报表需要的parameter：  

	public HashMap getMap() {

        HashMap map = new HashMap();
        map.put("reportTitle", "laoshulin");
        return map;
    }
	jasperPrint = JasperFillManager
                    .fillReport(jasperReport, getMap, getConn());
	
针对jasperPrint对象JasperReport有很多的API可以提供各种方式的预览或是生成文件，我只说说JasperViewer吧！其它的看看api或是google一下就知道了  

	JasperViewer jasperViewer = new JasperViewer(jasperPrint);
	
JasperViewer 继承自JFrame类，`jasperViewer.setVisible(true);`  
这样就可以预览报表了。  

`JRViewer`这个类继承`Jpanel`，我们可以在web中使用它，后面介绍客户端打印时再详细介绍。  

JDBC数据源很简单主要是SQL的功底，但是它是最基础的，网上一大堆这方面的介绍哦，可以参考别人的多看看哦，
我写的这些都有点语无伦次了，主要是我自己不怎么写东西，多以代码的形式留给自己了，现在写出来和大家交流，
当时做这个的时候找了好多的文章没有一篇写的深刻的，大多都是copy的。  



	
