---
layout: post
title: "iReport+jasperReport之客户端打印"
tagline: "Series of articles of iReport and JasperReport "
description: "jasperReport客户端采用applet，applet和activeX插件的区别大家搜一下，有一堆东西可以借鉴。"
category: iReport
tags: iReport、jasperReport
---

jasperReport客户端采用applet，applet和activeX插件的区别大家搜一下，有一堆东西可以借鉴。
下载jasperreports-3.0.0，在相应的sample OR demo（记得不清了）中可以找到jasperReport 实现的客户端打印demo，下来就来分析下具体实现。

**新建一print.js文件: **  
	
	function loadApplet(applet_URL) {
		var URL = applet_URL + "/applets/jre-1_5_0_18-windows-i586-p.exe";
		document.write('<OBJECT id="myApplet" name="myApplet"');
		document.write('classid="clsid:8AD9C840-044E-11D1-B3E9-00805F499D93" WIDTH="0" HEIGHT="0" MAYSCRIPT name="myApplet"');
		document.write('codebase='+ URL +'>');
		document.write('<PARAM NAME="CODE" VALUE="org.bulktree.report.print.applet.PrinterApplet" />');
		document.write('<PARAM NAME="CODEBASE" VALUE="../applets" />');
		document.write('<PARAM NAME="ARCHIVE" VALUE="jasperreports-3.0.1-applet.jar" />');
		document.write('<PARAM NAME="type" VALUE="application/x-java-applet;version=1.5.0" />');
		document.write('<PARAM NAME="scriptable" VALUE="false" />');
		document.write('<PARAM NAME="REPORT_URL" VALUE="../report.view">');
		document.write('no support java');
		document.write('<comment>');
		document.write('<embed type="application/x-java-applet;version=1.5.0"');
		document.write('CODE="org.bulktree.report.print.applet.PrinterApplet"');
		document.write('JAVA_CODEBASE="applets" ARCHIVE="jasperreports-3.0.1-applet.jar"');
		document.write('scriptable=false');
		document.write('pluginspage='+ URL +'>');
		document.write('<noembed></noembed>');
		document.write('</embed>');
		document.write('</comment>');
		document.write('</OBJECT>');
	}
	
URL为一个固定的路径，是为了下载/applets/jre-1_5_0_18-windows-i586-p.exe（JRE运行环境）而存在的，也可以使用外网从sun公司网站下载。
REPORT_URL 为applet所要访问的地址，在工程的web.xml配置/report.view如下servlet即可。
此servet可接收applet参数，生成jasperPrint对象，并传递到客户端applet中进行打印或者预览。  

**servlet配置如下：**
	
	<servlet>
		<servlet-name>ReportServlet</servlet-name>
        <servlet-class>org.bulktree.report.print.applet.ReportServlet</servlet-class>
    </servlet>
	<servlet-mapping>
		<servlet-name>ReportServlet</servlet-name>
		<url-pattern>/report.view</url-pattern>
	</servlet-mapping>
	
ReportServlet核心代码就是根据业务找到对应的模板文件填充数据生成jasperPrint对象，产生的jasperPrint对象以对象流的形式发送给客户端，核心代码如下：  

	// 生成jasperPrint对象
	JasperPrint jasperPrint = JasperFillManager.fillReport(jasperReport,mapParam, new JREmptyDataSource());

	//组装流
	response.setContentType("application/octet-stream");
	response.setBufferSize(8986000);
	ServletOutputStream outStream = response.getOutputStream();
	ObjectOutputStream oos = new ObjectOutputStream(outStream);
	oos.writeObject(jasperPrint);
	
当然，客户端的applet使用如下：  
	
	/*
	 * applet与Servlet交互 URL传递页面传来的参数请求服务器Servlet
	 * 把applet传递的参数追加到servlet中 2008-10-14 jutleo
	 */

	url = new URL(getCodeBase(), requestUrl + "?printView=true"
			+ paraStr);

	if (url == null) {
		JOptionPane.showMessageDialog(this,
				"Source URL not specified");
	} else {
		InputStream in = url.openStream();
		ObjectInputStream objIn = new ObjectInputStream(in);
		Object obj = objIn.readObject();
		if (obj instanceof JasperPrint) {

		} else {
			JOptionPane.showMessageDialog(this, obj.toString());
			return;
		}

		if (jasperPrint == null) {
			// 根据Servlet返回的URL（ObjectStream）产生JasperPrint对象
			jasperPrint = (JasperPrint) obj;

		}
		// 拦截出现空报表问题
		if (jasperPrint != null
				&& jasperPrint.getPages().size() > 0) {
			/*
			 * 调用JasperReport.jar中JasperViewer绘制报表Frame
			 * JasperViewer继承自JFrame，采用setDefaultCloseOperation响应关闭窗口事件
			 */

			ViewerFrame viewerFrame = new ViewerFrame(this
					.getAppletContext(), jasperPrint,isShowPrintButton);
			
			viewerFrame.setVisible(true);
			//viewerFrame.show();

			// JasperViewer viewer = new
			// JasperViewer(jasperPrint);
			// viewer.setVisible(true);
			// viewer.setDefaultCloseOperation(javax.swing.WindowConstants.DISPOSE_ON_CLOSE);
			// 如果请求为空弹出对话框予以提示
		} else {
			JOptionPane
					.showMessageDialog(this,
							"Please check Your Report SQL! It resulted in empty Report! ");
			return;
		}
	}

其中requestUrl为applet配置中的REPORT_URL，现实中可以采用此种方式传递参数到applet中。
下篇文章会介绍更合适的业务数据传递，?printView=true表示，传递printView参数对应的值为true，用来预览使用。
剩下的工作就是怎么按照自己的要求打印和预览了。  
** jasperReport为我们提供了打印和预览现成的方法： ** 
	
	// 打印报表
	JasperPrintManager.printReport(print, false);
	
	/*  预览报表
	 * 调用JasperReport.jar中JasperViewer绘制报表Frame
	 * JasperViewer继承自JFrame，采用setDefaultCloseOperation响应关闭窗口事件
	 */
	ViewerFrame viewerFrame = new ViewerFrame(this.getAppletContext(), jasperPrint);
	viewerFrame.setVisible(true);
	
最后记着释放对象哦，浏览器会受不了得：  
	
	url = null;
	jasperPrint = null;
	
OK，客户端就出现了，参照demo没有什么难度，下一篇我会介绍一下具体的细节，及套打的实现。  