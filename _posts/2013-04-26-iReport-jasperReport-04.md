---
layout: post
title: "iReport+jasperReport之BEAN数据源"
tagline: "Series of articles of iReport and JasperReport "
description: "上篇中，我们提到使用的JDBC数据源，本篇来看看如何使用BEAN数据源！"
category: iReport
tags: iReport、jasperReport
---

JasperFillManager.fillReport()这个方法在使用[JDBC数据源][jdbcDataSource]时采用一个打开的数据库连接(getConn),除此之外jasperReport给我们提供了一个JRDataSource接口，
用以实现我们自己的数据源JRDataSource接口只有两个方法：  
	
	public interface JRDataSource
	{
		/**
		 * 针对当前的数据源返回游标指向的下一个元素的值，
		 * 
		 */
		public boolean next() throws JRException;
	   /**
		 * 返回游标指向的当前值
		 * 
		 */
		public Object getFieldValue(JRField jrField) throws JRException;
	}
	
**JRBeanCollectionDataSource**  
此种方式是最简单的一种，查看API我们就可以发现，JRBeanCollectionDataSource继承JRAbstractBeanDataSource类，
而JRAbstractBeanDataSource是一个抽象类它间接的实现了JRDataSource这个接口，所以我们就可以不用自己去实现next()/getFieldValue()这两个方法了。  
  看到JRBeanCollectionDataSource这个类名大概就知道怎么用了吧！  
  我们的模板文件还是上篇的JDBC数据源的模板，只是没有了查询语句，手工建立所需的几个字段(域)  
  
  新建一vo对象Person.java，最基本的getter、setter方法  
	
	package org.bulktree.ireport.customdata;

	/**
	 * custom data
	 * 
	 * @author jutleo Email: jutleo@gmail.com @ Nov 7, 2008
	 */
	public class Person {
		private String pid;
		private String name;
		private String sex;
		private String age;
		private String password;
		private String department;

		public Person(String pid, String name, String sex, String age, String password,
				String department) {
			this.pid = pid;
			this.name = name;
			this.sex = sex;
			this.age = age;
			this.password = password;
			this.department = department;
		}

		public Person() {

		}
	}
	
下来准备数据  
	
	Person p1 = new Person();
	p1.setAge("23");
	p1.setDepartment("ISoftStone");
	p1.setName("LAOSHULIN");
	p1.setPassword("123456789");
	p1.setPid("2008040516058772hj");
	p1.setSex("man");
	
既然它是一个BeanCollection我们 就来一个从conllection吧！  
	
	List<Person> list = new ArrayList<Person>();
    list.add(p1);
	
查看API，JRBeanCollectionDataSource的构造函数  
	
	public JRBeanCollectionDataSource(Collection beanCollection)
    {
        this(beanCollection, true);
    }
    /**
     *
     */
    public JRBeanCollectionDataSource(Collection beanCollection, boolean isUseFieldDescription)
    {
        super(isUseFieldDescription);
        
        this.data = beanCollection;

        if (this.data != null)
        {
            this.iterator = this.data.iterator();
        }
    }
	
看看描述我们使用第一个  
	
	JRDataSource datesource = new JRBeanCollectionDataSource(list);
	
这个数据源就构造完毕了，很简单吧！这样报表就可以预览了，完整的代码如下：  
	
	package org.bulktree.ireport.customdata;

	import java.io.File;
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.InputStream;
	import java.sql.Connection;
	import java.sql.DriverManager;
	import java.sql.ResultSet;
	import java.sql.Statement;
	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.List;
	import java.util.Map;

	import net.sf.jasperreports.engine.JRDataSource;
	import net.sf.jasperreports.engine.JRException;
	import net.sf.jasperreports.engine.JasperCompileManager;
	import net.sf.jasperreports.engine.JasperExportManager;
	import net.sf.jasperreports.engine.JasperFillManager;
	import net.sf.jasperreports.engine.JasperPrint;
	import net.sf.jasperreports.engine.JasperReport;
	import net.sf.jasperreports.engine.data.JRBeanCollectionDataSource;
	import net.sf.jasperreports.engine.util.JRLoader;
	import net.sf.jasperreports.view.JRViewer;
	import net.sf.jasperreports.view.JasperViewer;

	public class JasperReportDemo {
		public static void main(String[] args) throws Exception {
			JasperReportDemo jrd = new JasperReportDemo();
			jrd.reportMethod(jrd.getMap());
		}

		public void reportMethod(Map map) throws Exception {
			JasperReport jasperReport = null;
			JasperPrint jasperPrint = null;

			try {
				/*
				 * File file = new File("Person.jrxml"); InputStream in = new
				 * FileInputStream(file); // 编译报表 jasperReport =
				 * JasperCompileManager.compileReport(in);
				 */
				// 实际中编译报表很耗时,采用Ireport编译好的报表
				jasperReport = (JasperReport) JRLoader
						.loadObject("D:\\workspace\\Person.jasper");
				// 填充报表
				jasperPrint = JasperFillManager
						.fillReport(jasperReport, map, getDataSource());
				// JasperExportManager.exportReportToHtmlFile(jasperPrint,
				// "test.html");
				JasperViewer jasperViewer = new JasperViewer(jasperPrint);
				jasperViewer.setVisible(true);
			} catch (JRException e) {
				e.printStackTrace();
			} catch (FileNotFoundException e) {
				e.printStackTrace();
			}
		}

		public Map getMap() {

			Map map = new HashMap();
			map.put("reportTitle", "laoshulin");
			return map;
		}

		public JRDataSource getDataSource() throws Exception {

			// 自定义数据源
			Person p1 = new Person();
			p1.setAge("23");
			p1.setDepartment("ISoftStone");
			p1.setName("LAOSHULIN");
			p1.setPassword("123456789");
			p1.setPid("2008040516058772hj");
			p1.setSex("man");
			List<Person> list = new ArrayList<Person>();
			list.add(p1);

			JRDataSource datesource = new JRBeanCollectionDataSource(list);

			return datesource;
		}
	}
	
![显示效果](/static/img/20130426001.jpg)   

看看效果吧！  
简简单单的几行代码就可以完成一个简单的报表，我们看到的这些工具栏图表还有窗口的一些标题啊等等都可以自己的定制哦，下来慢慢介绍！  
	
[jdbcDataSource]: http://jutleo.github.io/2013/04/22/iReport-jasperReport-03/