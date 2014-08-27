---
layout: post
title: "如何在IE浏览器中正确的使用MSXML 版本"
tagline: "项目中的MSXML parser版本太低?总是提示下载最新版本，安装之后还是提示！"
categories: technology
description: "&emsp;项目中使用MSXML Parser解析引擎解析xml，浏览器总是提示版本不对！之前的方式就是提示客户安装系统提供的即可，事隔几年貌似不行了，IE的版本自带的都已经大于当年提供的MSXML4.0了。拖了好几年的问题到现在才暴露出来！"
tags: MSXML 
---

##引言
&emsp;V6系统这几年来一直提示XML解析器版本低，请升级，默认就下载系统提供的XML4.0版本，装来装去貌似能解决一部分问题，终归无法根本解决。由于要拿部分系统去演示，在虚拟机里配置时遇到问题了，我自己的主机是IE11的版本，无法正常访问系统，问题出来了，自己不能不解决啊。
##资料收集
&emsp;这是来自Microsoft XML Tream	Blog的一篇文章
[Using the right version of MSXML in Internet Explorer](http://blogs.msdn.com/b/xmlteam/archive/2006/10/23/using-the-right-version-of-msxml-in-internet-explorer.aspx)，文章说的很明确，针对性MSXML6 （最新的）和MSXML3 （部署最广泛的）版本的MSXML ，做了最大的优化和调整，并且用于浏览器内置。并且此blog里给出了最佳实践：
	
	var progIDs = [ 'Msxml2.DOMDocument.6.0', 'Msxml2.DOMDocument.3.0'];
	for (var i = 0; i < progIDs.length; i++) {
		try {
	        		var xmlDOM = new ActiveXObject(progIDs[i]);
	       		return xmlDOM;
	       	}
		catch (ex) {

	    	}
	}
	return null;

&emsp;回过头来看看系统中如何使用的：
	
	function getXMLActiveObj(){
		try{
	    	return new ActiveXObject("MSXML2.DOMDocument.4.0");
	    }catch(e){
	        if(confirm("您的机器上的XML解析器版本太低，您是否打算现在升级？")){
	            document.location = global.WEB_APP_NAME + "/core/jsp/common/MsXML4.0.jsp";
	        }
	        return null;
	    }
	}
&emsp;来看看MSXML4.0.jsp页面中是如何加载的：
	
	<object id="MSXML4"
	classid="clsid:88d969c0-f192-11d4-a65f-0040963251e5"
	codebase="MsXML4.0.cab#version=4,00,9004,0"
	type="application/x-oleobject"
	STYLE="display: none">
	</object>

&emsp;貌似最开始的加载就是建立在一种假设的基础上，导致后来操作系统、浏览器升级带来问题。

##调整`getXMLActiveObj`方法解决问题？
&emsp;引用官方的例子，重新调整`getXMLActiveObj`方法
	
	function getXMLActiveObj(){
		var progIDs = ['MSXML2.DOMDocument.6.0','MSXML2.DOMDocument.4.0','MSXML2.DOMDocument.3.0'];
		for (var i = 0; i < progIDs.length; i++) {
		    try {
		        var xmlDOM = new ActiveXObject(progIDs[i]);
		        return xmlDOM;
		    }
		    catch (ex) {
		    }
		}
	}

&emsp;由于我们最初使用的4.0版本，而且系统提供4.0版本的默认安装，还是保留4.0吧（官方貌似推荐3.0版本）。这样按照官方的解释，总是能获取到一个正确版本`ActiveXObject`对象。
##是否解决问题？
&emsp;系统可以在IE11下正常访问了，不用关注插件版本问题，而且插件默认总是能获取到。其实内置的也不算插件吧！
##遗留问题
&emsp;按照官方的解释 MSXML6.0 在XP中已经被系统支持，而且性能较优，版本低的还是由实施项目组自行决定是否升级吧！