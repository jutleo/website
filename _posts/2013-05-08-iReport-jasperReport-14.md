---
layout: post
title: "iReport+jasperReport之客户端打印 (续)"
tagline: "Series of articles of iReport and JasperReport "
description: "接着上篇，jasperReport 实现客户端主要是依靠applet，但是我们所有的操作不可能在applet中实现吧，这样也不算一个好的应用。"
category: iReport
tags: iReport、jasperReport
---

接着上篇，jasperReport 实现客户端主要是依靠applet，但是我们所有的操作不可能在applet中实现吧，这样也不算一个好的应用。  
**考虑以下几点：**  
1. javascript 和applet互相通信。applet和前台界面交互，可以让客户感觉不到有applet的存在。  
2. applet和后台相互通信。applet既可以接受后台转递的参数、对象流等等 还可以把信息返回到后台。  
3. applet只实现打印和预览，主要的业务操作需要在后台完成。  
上篇中提到print.js：  
新建一jsp页面 PrintReportApplet.jsp，此jsp页面就一个按钮  

	<input type="submit" value="预览" onclick="reportViewer()" /> 
	
这样我们就可以在当前页面中引用到applet了， `document.applets.myApplet.reportViewer()` 拿到`applet`的窗口句柄后调用`applet`的`reportViewer()`方法  
我们也可以在此jsp页面写一个初始化的方法传递参数到`applet`中，当然也可以直接传递参数到`reportViewer（parameter String）;`
	
	function getParameters() { 
        return "&pid=0001"
	}
	
在`print.js`中指定 `<PARAM NAME="CODE" VALUE="com.isoftstone.pcis.report.print.applet.PrinterApplet" />`
**PrinterApplet中reportViewer** 
	
	// javascript预览报表
    public void reportViewer() {
        try {
            win = JSObject.getWindow(this);
            paraStr = win.eval("getParameters()").toString();

            if ("".equals(paraStr) || paraStr == null) {
                return;
            }
            System.out.println("paraStr=======>" + paraStr);

        } catch (Exception e1) {
            e1.printStackTrace();
        }
        if (url == null) {
            if (requestUrl != null) {
                try {
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
                                    .getAppletContext(), jasperPrint);
                            
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
                } catch (Exception e) {
                    StringWriter swriter = new StringWriter();
                    PrintWriter pwriter = new PrintWriter(swriter);
                    e.printStackTrace(pwriter);
                    JOptionPane.showMessageDialog(this, swriter.toString());
                } finally {// 全打套打URL不一样释放对象 by laoshulin
                    url = null;
                    jasperPrint = null;
                    win = null;
                }
            }
        }
    }
	
`JSObject` 对象的使用`google`一下有很多哦，`requestUrl = getParameter("REPORT_URL");`为`print.js`中配置的请求，之后我们`new URL`带上我们的参数去请求远程资源。  
	
	<servlet-mapping>
        <servlet-name>ReportServlet</servlet-name>
        <url-pattern>/report.view</url-pattern>
    </servlet-mapping>
	
	public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        try {

            /*
             * Servlet中可这样取得打印或是预览操作，添加此功能针对套打预览 String
             * view=request.getParameter("printView"); boolean
             * isView="true".equalsIgnoreCase(view)?true:false; String
             * print=request.getParameter("print"); boolean
             * isPrint="true".equalsIgnoreCase(print)?true:false;
             */

            // 拿参数
            String pid = request.getParameter("pid");
            String form = request.getParameter("format");
            // 编译报表耗时，修改为编译好的报表
            /*
             * JasperReport jasperReport = JasperCompileManager
             * .compileReport("D:\\workspace\\report\\AppletTest.jasper");
             */
            // 直接使用编译好的文件
			//JasperReport jasperReport = (JasperReport) JRLoader.loadObject("D:\\workspace\\report\\AppletTest.jasper");
            JasperReport jasperReport = (JasperReport) JRLoader
                    .loadObjectFromLocation("D:\\workspace\\report\\AppletTest.jasper");
            HashMap mapParam = new HashMap();
            mapParam.put("imageParam", "D:\\workspace\\report\\eg_smile.gif");
            /*
             * 此参数用来控制是否显示图片
             * 第二个参数在报表中设置为String类型
             */
            mapParam.put("isShowImage", "true");
            // 生成jasperPrint对象
            JasperPrint jasperPrint = JasperFillManager.fillReport(jasperReport,
                    mapParam, new JREmptyDataSource());
            System.out.println(jasperPrint.getPageHeight()+"$$$$$$$$$$$$"+jasperPrint.getPageWidth()+"**********"+jasperPrint.getPages());
            
			response.setContentType("application/octet-stream");
			response.setBufferSize(8986000);
			ServletOutputStream outStream = response.getOutputStream();
			ObjectOutputStream oos = new ObjectOutputStream(outStream);
			oos.writeObject(jasperPrint);
			// outStream.flush();
			// oos.flush();
			
			// outStream.close();
			// oos.close();
            
            //outStream.flush();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
	
请求到远程资源完全可以按照业务需求实现自己的`jasperReport` 了，最终我们只要把一个`JasperPrint`对象输出到客户端就可以了。  
客户端通过打开`InputStream in = url.openStream();`实现还原`jasperPrint`对象，从而实现打印或者预览。  
客户端打印可以前的方法是一样的：  
编译、填充、生成JasperPrint对象、预览或打印  
上篇有人提到[打印和预览实现动态控制套打背景][images_jasper],建议你多看看 iReport+jasperReport之图片控件。  
实现原理是一样的，只是把JasperPrint在客户端处理了一下哦。

[images_jasper]: http://jutleo.github.io/2013/05/03/iReport-jasperReport-11/