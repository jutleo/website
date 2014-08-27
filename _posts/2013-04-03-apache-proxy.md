---
layout: post
title: "apache反向代理"
tagline: "apche proxy"
description: "代理的作用就是将客户机的请求提交到目的服务器，等到目的服务器响应后再返给客户机。代理可以做哪些事情？通常我们说的正向代理和反向代理有啥区别？"
category: server
tags: [apache proxy]
---

###代理的功能 

  1. 突破自身的访问限制。比如访问国外的站点时需要翻墙、内网通过代理上网、公司的内网限制上QQ等。  
  2. 访问某些内部资源。 比如将某些内部资源暴露在外部区域。  
  3. 提高访问的速度。代理服务器一般都会有较大的磁盘缓冲区，可以将客户机的请求缓存起来。
  4. 隐藏。 这个都知道了，可以隐藏真实的客户机地址。  
  
  其它的暂时想不起来了，代理的作用用的时候才会发现，可以解决很多实际问题，转发、隐藏端口、跨域等等，很happy。
  
###正向代理、反向代理

  ***正向代理*** 是需要设置在客户机上的，客户机需要配置代理，请求将会通过代理进行中转到目标机上，并通过代理服务器返回给客户机。一般来说代理机就是替代客户机去访问目标机。
客户机和正向代理机像是位于同一个局域网内。正向代理服务器的主要目的是缓存数据来响应客户端的 HTTP 请求，一般都会进行用户访问控制。  
 
  ![正向代理](/static/img/20130403001.jpg)  
  
  1. 访问无法访问的目标机；  
  2. 加速访问速度；  
  3. Cache；
  4. 客户机访问授权；  
  5. 隐藏客户机访问行踪；  
  
  *正向代理是介于客户机和目标机中间的机器，需要知道代理机的IP和端口，安全考虑需要用户和密码认证，目标机对于客户机是透明的，客户机访问目标机时，由代理机完成请求的中间转发，并返回给客户机*  
  
  ![反向代理](/static/img/20130403002.jpg)  
  
  ***反向代理*** 和正向代理相反，目标机对于客户机是不知的。对于客户机来说，代理机就是目标机，由代理机去判断目标机在何处，并且客户机不需要任何设置。客户机像代理机发送请求，代理机将请求转向到目标机，并将返回
内容返给客户机。  一般反向代理应用最常见的是做负载均衡。

  现在来说一下apache如何做代理 ：  
  
  首先安装apache：安装基本next  
  修改conf/下的httpd.conf文件：    
  
  去掉如下四项前面的`#`，
  
    LoadModule proxy_module modules/mod_proxy.so  
	LoadModule proxy_connect_module modules/mod_proxy_connect.so
    LoadModule proxy_ftp_module modules/mod_proxy_ftp.so
    LoadModule proxy_http_module modules/mod_proxy_http.so  
	
  在文件最底下输入如下代码： 	

	ProxyRequests Off
	<Proxy *>
	Order deny,allow
	Allow from all
	</Proxy>

	#以下为反向代理配置
	ProxyPass /xxx http://jutleo.github.com
	ProxyPassReverse /xxx http://jutleo.github.com  
	
  当然反向代理可以配置多个
  
    #以下为反向代理配置
	ProxyPass /xxx http://jutleo.github.com
	ProxyPassReverse /xxx http://jutleo.github.com
	ProxyPass /yyy http://weibo.com/jutleo
	ProxyPassReverse /yyy http://weibo.com/jutleo
		
  客户机请求反向代理机，反向代理机找到实际的目标机地址将客户机请求转发给目标机，目标机返回给代理机、代理机返回给客户机。`ProxyPass`表示转发，如果
目标机定向后会绕过代理机将请求直接返回给客户机，`ProxyPassReverse`表示反向代理，目标机返回后，代理机会重新调整地址为代理机的地址。

  ok, 重启apache，输入http://localhost/xxx试试吧！注意看地址栏变化。
  
  
  

