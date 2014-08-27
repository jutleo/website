---
layout: post
title: "iReport+jasperReport之CSV、XML数据源"
tagline: "Series of articles of iReport and JasperReport "
description: "再来说说jasperReport数据源的事情"
category: iReport
tags: iReport、jasperReport
---

jasperReport支持多种格式的数据源，CSV(Comma Separated values),是一种用来存储数据的纯文本,文件格式，通常用于电子表格或数据库软件。  

**规则**  

0. 开头是不留空，以行为单位。  
1. 可含或不含列名，含列名则居文件第一行。  
2. 一行数据不垮行，无空行。  
3. 以半角符号，作分隔符，列为空也要表达其存在。  
4. 列内容如存在，，则用“”包含起来。  
5. 列内容如存在“”则用“”“”包含。  
6. 文件读写时引号，逗号操作规则互逆。  
7. 内码格式不限，可为ASCII、Unicode或者其他。  

jasper文件和前面几篇用到的是一样的(person.jasper),准备数据的文本文件Person.txt其中文件的格式对应模板文件的字段【 "pid", "name", "sex", "age", "password", "department"】。  
	
	"20000000001","bulktree1","man","21","1111111111","pcisv61"
	"20000000002","bulktree2","man","22","2222222222","pcisv62"
	"20000000003","bulktree3","man","23","3333333333","pcisv63"
	"20000000004","bulktree4","man","24","4444444444","pcisv64"
	"20000000005","bulktree5","man","25","5555555555","pcisv65"
	"20000000006","bulktree6","man","26","6666666666","pcisv66"
	"20000000007","bulktree7","man","27","7777777777","pcisv67"
	"20000000008","bulktree8","man","28","8888888888","pcisv68"
	"20000000009","bulktree9","man","29","9999999999","pcisv69"
	
下来我们看看jasperReport的API是怎么识别这些数据的，但是做这个之前我们还要指定数据到底是怎么对应的,定义String类型数组存放模板对应的列名和文本数据对应  
	
	String[] columNames = new String[] { "pid", "name", "sex", "age", "password",
                "department" };
	
识别这些数据并不难，下面这一句就可以搞定：  
	
	JRCsvDataSource jrcsvDataScource = new JRCsvDataSource(JRLoader
                .getLocationInputStream("D:\\workspace\\Person.txt"));
	
我们怎么才能让它识别对应的列呢？查看API看看对应的set方法吧！  
	
	// set record delimiter
        jrcsvDataScource.setRecordDelimiter("\r\n");
        // set columnName
        jrcsvDataScource.setColumnNames(columNames);

至此数据源就准备完了，jasperReport封装了底层的实现，简单吧！下来看看完整的代码：  
	
	package org.bulktree.ireport.csvdata;

	import java.util.HashMap;

	import net.sf.jasperreports.engine.JasperFillManager;
	import net.sf.jasperreports.engine.JasperPrint;
	import net.sf.jasperreports.engine.JasperReport;
	import net.sf.jasperreports.engine.data.JRCsvDataSource;
	import net.sf.jasperreports.engine.util.JRLoader;

	import org.bulktree.ireport.ViewReport;

	public class CSVDataSource {
		
		public static void main(String[] args) {
			try {
				new CSVDataSource().reportView();
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}

		private void reportView() throws Exception {
			JasperReport jasperReport = (JasperReport) JRLoader
					.loadObject("D:\\workspace\\Person.jasper");

			JasperPrint jasperPrint = JasperFillManager.fillReport(jasperReport,
					getReportParameter(), getDateSource());
			
			new ViewReport().viewer(jasperPrint);
		}

		private HashMap getReportParameter() {
			HashMap parameters = new HashMap();
			parameters.put("reportTitle", "laoshulin");
			return parameters;
		}

		private JRCsvDataSource getDateSource() throws Exception {
			String[] columNames = new String[] { "pid", "name", "sex", "age", "password",
					"department" };
			// get csvdata
			JRCsvDataSource jrcsvDataScource = new JRCsvDataSource(JRLoader
					.getLocationInputStream("D:\\workspace\\test\\src\\org\\bulktree\\ireport\\csvdata\\Person.txt"));
			// set record delimiter
			jrcsvDataScource.setRecordDelimiter("\r\n");
			// set columnName
			jrcsvDataScource.setColumnNames(columNames);

			return jrcsvDataScource;
		}
	}

还是这样的效果吧！^_^  
![显示效果](/static/img/20130427001.jpg)  

至于XML数据源也是很简单，通过读取xml文件获得数据  
	
	Document document = JRXmlUtils.parse(JRLoader.getLocationInputStream(xmlFileName));
	
不同的是document会作为一个参数传递给报表，fillReport方法就不会出现第三个参数，完整代码如下：  
	
	package org.bulktree.ireport.xmldata;

	import java.util.HashMap;
	import java.util.Locale;

	import net.sf.jasperreports.engine.JRException;
	import net.sf.jasperreports.engine.JRParameter;
	import net.sf.jasperreports.engine.JasperFillManager;
	import net.sf.jasperreports.engine.JasperPrint;
	import net.sf.jasperreports.engine.JasperReport;
	import net.sf.jasperreports.engine.query.JRXPathQueryExecuterFactory;
	import net.sf.jasperreports.engine.util.JRLoader;
	import net.sf.jasperreports.engine.util.JRXmlUtils;

	import org.bulktree.ireport.ViewReport;
	import org.w3c.dom.Document;

	public class XMLDataSource {

		private static final String JASPER_FILE_NAME = "D:\\workspace\\Person.jasper";
		private static final String XML_FILE_NAME = "D:\\workspace\\person.xml";

		private void viewerReport() throws JRException {
			JasperReport jasperReport = (JasperReport) JRLoader.loadObject(JASPER_FILE_NAME);

			JasperPrint jasperPrint = JasperFillManager.fillReport(jasperReport,
					getReportParameter(XML_FILE_NAME));

			new ViewReport().viewer(jasperPrint);
		}

		private HashMap getReportParameter(String xmlFileName) {
			HashMap parameters = new HashMap();
			try {
				parameters.put("reportTitle", "laoshulin");
				Document document = JRXmlUtils.parse(JRLoader.getLocationInputStream(xmlFileName));

				parameters.put(JRXPathQueryExecuterFactory.PARAMETER_XML_DATA_DOCUMENT,
						document);
				parameters.put(JRXPathQueryExecuterFactory.XML_DATE_PATTERN, "yyyy-MM-dd");
				parameters.put(JRXPathQueryExecuterFactory.XML_NUMBER_PATTERN, "#,##0.##");
				parameters.put(JRXPathQueryExecuterFactory.XML_LOCALE, Locale.CHINESE);
				parameters.put(JRParameter.REPORT_LOCALE, Locale.CHINA);

			} catch (JRException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}

			return parameters;
		}

		public static void main(String[] args) throws Exception {
			new XMLDataSource().viewerReport();
		}
	}
	
预览效果就不看了吧！哈哈 都是一样的道理，现在看看jasperReport的API有多强大了吧！  



[BeanDataSource]:http://jutleo.github.io/2013/04/26/iReport-jasperReport-04/