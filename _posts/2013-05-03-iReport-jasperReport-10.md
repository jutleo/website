---
layout: post
title: "iReport+jasperReport之JFreeChart（图表报表）"
tagline: "Series of articles of iReport and JasperReport "
description: "jasperReport的图表完全借助于外部的绘图工具，charts和JFreeChart,其中JFreeChart是目前java最火的一个绘图工具了，还是引用JFreeChart简单使用 来实现我们的图表吧"
category: iReport
tags: iReport、jasperReport
---

说完[iReport+jasperReport之scriptlet](http://jutleo.github.io/2013/05/03/iReport-jasperReport-08/) 下来就得看看图表了。
jasperReport的图表完全借助于外部的绘图工具，`charts`和`JFreeChart`,其中`JFreeChart`是目前`java`最火的一个绘图工具了，
还是引用`JFreeChart`简单使用 来实现我们的图表吧，说到这里澄清一下，本篇写的是`jasperReport`的图表，下来会专门写一篇关于`jasperReport`图片报表。
`jasperReport`不重复发明车轮，在报表中是以一个变量的方式引入外部图表的，下来我们看看是怎么实现的。  
 **新建一变量jfreeChart 如图：**  

 ![显示效果](/static/img/20130502005.jpg) 

紧接着我们在报表上放置一图片控件,下来一篇我会详细说说图片问题，右击设置图片控件属性，如图所示：  

 ![显示效果](/static/img/20130502006.jpg) 
 
`net.sf.jasperreports.engine.JRRenderable`为`jasperReport`一个专门用来处理图表问题公共接口。
一切OK，现在就是这个变量怎么才能吧jfreechart传递到报表中呢，还记得上篇的`scriptlet`吗？
新建一`JChartReportDemo.java`类，继承自`JRDefaultScriptlet`，当然要实现那些方法不过大部分我们都是空实现而已，
我们只要在`afterReportInit`方法内绘制图表然后在`set`进去我们定义的那个变量即可：  
	
	public void afterReportInit() throws JRScriptletException {
        
        //create pie chart dataset
        DefaultPieDataset dateset = new DefaultPieDataset();

        //set dataset value
        dateset.setValue("Chinese", 108);
        dateset.setValue("Math", 110);
        dateset.setValue("English", 74);
        dateset.setValue("Science Department", 226);
        
        /*
         * create jfreeChart object
         * the first parameter is pie chart title
         * the secend parameter is dataset of pie chart
         * the three parameter is boolean value,create chart note
         * the four parameter is boolean value,it's a tooltip of move mouse on
         * configure chart to generate URLs,It's get a PiePlot3D object
         * 
         */
        JFreeChart freeChart = ChartFactory.createPieChart3D("Report Pie Chart", dateset, true, true, false);
        
        PiePlot3D plot3D = (PiePlot3D) freeChart.getPlot();
        plot3D.setNoDataMessage("No data to display");
        
        // set variable "jfreeChart" value
        this.setVariableValue("jfreeChart", new JFreeChartRenderer(freeChart));
    }
	
其中`plot3D.setNoDataMessage("No data to display");`这一句的意思是当没有显示出图表或是图表没有数据不显示时会显示我们定义的这些信息。  
	
	this.setVariableValue("jfreeChart", new JFreeChartRenderer(freeChart));
	
这一句当然是set数据了，JFreeChartRenderer这个类是JRRenderable接口的间接实现，从API上看jasperReport已不推荐我们使用了。OK，我们test一下吧！  
**JChartReportMain.java**  
	
	package org.bulktree.ireport.chart;

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
	 * @author bulktree Email: jutleo@gmail.com
	 * @date Nov 28, 2008
	 */
	public class JChartReportMain {
		public static void main(String[] args) {
			String path = "D:/workspace/JFreeChartReportDemo.jrxml";

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
				e.printStackTrace();
			}
		}
	}
	
![显示效果](/static/img/20130502007.jpg) 