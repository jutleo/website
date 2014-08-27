---
layout: post
title: "iReport+jasperReport之BEAN数据源(续)"
tagline: "Series of articles of iReport and JasperReport "
description: "继上篇bean数据源，如果我们自己定义自己的数据源该如何去写呢？"
category: iReport
tags: iReport、jasperReport
---

继[上篇bean数据源][BeanDataSource]，如果我们自己定义自己的数据源该如何去写呢？  
jasperReport提供了很多的便利去实现自己的DataSource，简单的有三种方式：  
1. **直接实现bean的方式**  
2. **bean工厂**  
3. **表格模型**  

还是使用前面的person.jasper文件，和Person.java VO类  

#####直接实现bean的方式  
此种方式需要实现JRDataSource接口，定义一个二维对象数组用来存放数据，通过遍历数组的数据实现getFieldValue()和next()方法  

**PersonDataSource.java**  
	
	package org.bulktree.ireport.customdata;

	import javax.activation.DataSource;

	import net.sf.jasperreports.engine.JRDataSource;
	import net.sf.jasperreports.engine.JRException;
	import net.sf.jasperreports.engine.JRField;

	/**
	 * 
	 * @author bulktree Email: jutleo@gmail.com @ Nov 7, 2008
	 */
	public class PersonDataSource implements JRDataSource {

		public PersonDataSource() {

		}

		private Object[][] data = {
				{ "001", "bulktree1", "Man1", "21", "001111", "jutleo1" },
				{ "002", "bulktree2", "Man2", "22", "002222", "jutleo2" },
				{ "003", "bulktree3", "Man3", "23", "003333", "jutleo3" },
				{ "004", "bulktree4", "Man4", "24", "004444", "jutleo4" },
				{ "005", "bulktree5", "Man5", "25", "005555", "jutleo5" },
				{ "006", "bulktree6", "Man6", "26", "006666", "jutleo6" },
				{ "007", "bulktree7", "Man7", "27", "007777", "jutleo7" },
				{ "008", "bulktree8", "Man8", "28", "008888", "jutleo8" },
				{ "009", "bulktree9", "Man9", "29", "009999", "jutleo9" },
				{ "001", "oakertree1", "Man1", "21", "001111", "jutleo1" },
				{ "002", "oakertree2", "Man2", "22", "002222", "jutleo2" },
				{ "003", "oakertree3", "Man3", "23", "003333", "jutleo3" },
				{ "004", "oakertree4", "Man4", "24", "004444", "jutleo4" },
				{ "005", "oakertree5", "Man5", "25", "005555", "jutleo5" },
				{ "006", "oakertree6", "Man6", "26", "006666", "jutleo6" },
				{ "007", "oakertree7", "Man7", "27", "007777", "jutleo7" },
				{ "008", "oakertree8", "Man8", "28", "008888", "jutleo8" },
				{ "009", "oakertree9", "Man9", "29", "009999", "jutleo9" },
				{ "001", "laoshulin1", "Man1", "21", "001111", "jutleo1" },
				{ "002", "laoshulin2", "Man2", "22", "002222", "jutleo2" },
				{ "003", "laoshulin3", "Man3", "23", "003333", "jutleo3" },
				{ "004", "laoshulin4", "Man4", "24", "004444", "jutleo4" },
				{ "005", "laoshulin5", "Man5", "25", "005555", "jutleo5" },
				{ "006", "laoshulin6", "Man6", "26", "006666", "jutleo6" },
				{ "007", "laoshulin7", "Man7", "27", "007777", "jutleo7" },
				{ "008", "laoshulin8", "Man8", "28", "008888", "jutleo8" },
				{ "009", "laoshulin9", "Man9", "29", "009999", "jutleo9" } };

		private int index = -1;

		public Object getFieldValue(JRField jrField) throws JRException {

			Object value = null;

			String fieldName = jrField.getName();

			if ("pid".equals(fieldName)) {
				value = data[index][0];
			}
			if ("name".equals(fieldName)) {
				value = data[index][1];
			}
			if ("sex".equals(fieldName)) {
				value = data[index][2];
			}
			if ("age".equals(fieldName)) {
				value = data[index][3];
			}
			if ("password".equals(fieldName)) {
				value = data[index][4];
			}
			if ("department".equals(fieldName)) {
				value = data[index][5];
			}

			return value;
		}

		public boolean next() throws JRException {
			index++;

			return (index < data.length);
		}

	}
	
这个和遍历数据库Result的游标是一样的道理，刚开始的时候是指向第一条数据的前面，next一下才会指向第一条数据，相信这个不是很难懂吧！
通过`jasperPrint = JasperFillManager.fillReport(jasperReport,getReportParameter(), personDataSource);` 产生JasperPrint对象。  
personDataSource为PersonDataSource的对象，预览效果如下：  
![显示效果](/static/img/20130426002.jpg)  

#####bean工厂
此种方式不需要实现JRDataSource接口，定义bean数组存放数据，只需要简单的提供两个方法即可：返回一个对象数组和返回一个bean集合  

**PersonBeanFactory.java**  
	
	package org.bulktree.ireport.customdata;

	import java.util.Arrays;
	import java.util.Collection;

	/**
	 * 
	 * @author bulktree Email: jutleo@gmail.com @ Nov 7, 2008
	 */
	public class PersonBeanFactory {
		private static Person[] data = {
				new Person("001", "bulktree1", "Man1", "21", "001111", "jutleo1"),
				new Person("002", "bulktree2", "Man2", "22", "002222", "jutleo2"),
				new Person("003", "bulktree3", "Man3", "23", "003333", "jutleo3"),
				new Person("004", "bulktree4", "Man4", "24", "004444", "jutleo4"),
				new Person("005", "bulktree5", "Man5", "25", "005555", "jutleo5"),
				new Person("006", "bulktree6", "Man6", "26", "006666", "jutleo6"),
				new Person("007", "bulktree7", "Man7", "27", "007777", "jutleo7"),
				new Person("008", "bulktree8", "Man8", "28", "008888", "jutleo8"),
				new Person("009", "bulktree9", "Man9", "29", "009999", "jutleo9"),
				new Person("001", "oakertree1", "Man1", "21", "001111", "jutleo1"),
				new Person("002", "oakertree2", "Man2", "22", "002222", "jutleo2"),
				new Person("003", "oakertree3", "Man3", "23", "003333", "jutleo3"),
				new Person("004", "oakertree4", "Man4", "24", "004444", "jutleo4"),
				new Person("005", "oakertree5", "Man5", "25", "005555", "jutleo5"),
				new Person("006", "oakertree6", "Man6", "26", "006666", "jutleo6"),
				new Person("007", "oakertree7", "Man7", "27", "007777", "jutleo7"),
				new Person("008", "oakertree8", "Man8", "28", "008888", "jutleo8"),
				new Person("009", "oakertree9", "Man9", "29", "009999", "jutleo9"),
				new Person("001", "laoshulin1", "Man1", "21", "001111", "jutleo1"),
				new Person("002", "laoshulin2", "Man2", "22", "002222", "jutleo2"),
				new Person("003", "laoshulin3", "Man3", "23", "003333", "jutleo3"),
				new Person("004", "laoshulin4", "Man4", "24", "004444", "jutleo4"),
				new Person("005", "laoshulin5", "Man5", "25", "005555", "jutleo5"),
				new Person("006", "laoshulin6", "Man6", "26", "006666", "jutleo6"),
				new Person("007", "laoshulin7", "Man7", "27", "007777", "jutleo7"),
				new Person("008", "laoshulin8", "Man8", "28", "008888", "jutleo8"),
				new Person("009", "laoshulin9", "Man9", "29", "009999", "jutleo9") };

		public static Object[] getPersonArray() {
			return data;
		}

		public static Collection getPersonCollection() {
			return Arrays.asList(data);
		}
	}
	
产生JasperPrint对象的语句如下：  
	
	jasperPrint = JasperFillManager.fillReport(jasperReport,
                    getReportParameter(), new JRBeanArrayDataSource(personBeanFactory.getPersonArray()));
	
我们没有实现JRDataSource接口,JasperReport是通过JRBeanArrayDataSource这个类来完成JRDataSource接口的封装，我们只要提供给他简单的bean工厂数据即可，预览效果和上面的一样。  

#####表格模型  
此种模型也是需要一个二维对象数组，通过数据的下标去遍历数据，不需要实现JRDataSource接口，但是要继承AbstractTableModel这个抽象类。
为此我们要在提供了对象数组后需要定义一个变量和下面几个方法：定义一个报表要显示的列名字数组，和模板文件的Field对应、二维对象数组对应；  

1. getColumnCount()获得列数；  
2. getColumnName()获得列名；  
3. getRowCount()获得所有数据的行数也就是报表要显示数据的行数；  
4. getValueAt()获得某行某列的值作为对象返回。  

**PersonTableModel.java**  
	
	package org.bulktree.ireport.customdata;

	import javax.swing.table.AbstractTableModel;

	public class PersonTableModel extends AbstractTableModel {

		public PersonTableModel() {

		}

		// 定义报表列
		private String[] columnNames = { "pid", "name", "sex", "age", "password",
				"department" };

		// get columnCount
		public int getColumnCount() {
			return this.columnNames.length;
		}

		// ColumnName
		public String getColumnName(int columnIndex) {
			return this.columnNames[columnIndex];
		}

		// get Row Number
		public int getRowCount() {
			return this.data.length;
		}

		private Object[][] data = {
				{ "001", "bulktree1", "Man1", "21", "001111", "jutleo1" },
				{ "002", "bulktree2", "Man2", "22", "002222", "jutleo2" },
				{ "003", "bulktree3", "Man3", "23", "003333", "jutleo3" },
				{ "004", "bulktree4", "Man4", "24", "004444", "jutleo4" },
				{ "005", "bulktree5", "Man5", "25", "005555", "jutleo5" },
				{ "006", "bulktree6", "Man6", "26", "006666", "jutleo6" },
				{ "007", "bulktree7", "Man7", "27", "007777", "jutleo7" },
				{ "008", "bulktree8", "Man8", "28", "008888", "jutleo8" },
				{ "009", "bulktree9", "Man9", "29", "009999", "jutleo9" },
				{ "001", "oakertree1", "Man1", "21", "001111", "jutleo1" },
				{ "002", "oakertree2", "Man2", "22", "002222", "jutleo2" },
				{ "003", "oakertree3", "Man3", "23", "003333", "jutleo3" },
				{ "004", "oakertree4", "Man4", "24", "004444", "jutleo4" },
				{ "005", "oakertree5", "Man5", "25", "005555", "jutleo5" },
				{ "006", "oakertree6", "Man6", "26", "006666", "jutleo6" },
				{ "007", "oakertree7", "Man7", "27", "007777", "jutleo7" },
				{ "008", "oakertree8", "Man8", "28", "008888", "jutleo8" },
				{ "009", "oakertree9", "Man9", "29", "009999", "jutleo9" },
				{ "001", "laoshulin1", "Man1", "21", "001111", "jutleo1" },
				{ "002", "laoshulin2", "Man2", "22", "002222", "jutleo2" },
				{ "003", "laoshulin3", "Man3", "23", "003333", "jutleo3" },
				{ "004", "laoshulin4", "Man4", "24", "004444", "jutleo4" },
				{ "005", "laoshulin5", "Man5", "25", "005555", "jutleo5" },
				{ "006", "laoshulin6", "Man6", "26", "006666", "jutleo6" },
				{ "007", "laoshulin7", "Man7", "27", "007777", "jutleo7" },
				{ "008", "laoshulin8", "Man8", "28", "008888", "jutleo8" },
				{ "009", "laoshulin9", "Man9", "29", "009999", "jutleo9" } };

		public Object getValueAt(int rowIndex, int columnIndex) {
			return this.data[rowIndex][columnIndex];
		}
	}
	
和bean工厂方式一样，通过JRTableModelDataSource类的构造器传入数据模型  
	
	jasperPrint = JasperFillManager.fillReport(jasperReport,
                    getReportParameter(), new JRTableModelDataSource(personTableModel));
	
至此bean方式的数据源全部介绍完毕，写这些东西很仓促，文字太少都是贴代码啦！不过都是本人测试通过的哦^_^ 。 


[BeanDataSource]:http://jutleo.github.io/2013/04/26/iReport-jasperReport-04/