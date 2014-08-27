---
layout: post
title: "spring security 基础"
tagline: "spring securiy自我理解，及如何解决实际问题"
categories: spring
description: "&emsp;系统中用到spring security框架，集成开源cas实现单点登录"
tags: spring 
---
##开篇
&emsp;如果你对`spring security`有疑问，请先阅读官方文档，由于本文提到的`spring security`是基于2.0.8版本的，可能跟最新3的版本有出入，请自行查阅文档，思路都是想通的。

##说明
&emsp;`spring security`是基于`spring AOP` 和 `Servlet`的安全过滤框架，基于Web请求访问和方法调用处理身份验证和授权。试想它出现在系统中的原因及作用？

##原理
&emsp;`spring security`分为三部分：安全拦截、身份认证、授权资源

&emsp;`AbstractSecurityInterceptor` 负责拦截所有用户请求，`FilterSecurityInterceptor`负责使用过滤用户的权限，在过滤用户权限时，`spring security`使用认证管理器(`AuthenticationManager`)
&emsp;调用认证提供者('AuthenticationProvider')去认证用户是否合法，认证提供者通过调用`UserDetailsService`接口的`loadUserByUsername`方法实现用户验证。	  
&emsp;`spring`默认为我们提供了，权限提供者`DaoAuthencationProvider`，实现从数据库进行密码验证。  
&emsp;`spring`默认为我们提供了`UserDetailsService`的实现`JdbcDaoImpl`，实现用户信息、权限信息加载。  

&emsp;当认证通过后，受保护资源才可以访问。用户通过认证后`spring security`将会写入认证标识到本地线程。  

##具体配置
	
	<security:http auto-config="true" access-denied-page="/core/login/loginError.jsp?login_error=1">
		<security:form-login login-page="/core/login/login.jsp" authentication-failure-url="/core/login/loginError.jsp?login_error=1" default-target-url="/core/pcis_main.jsp" />
        <security:logout logout-success-url="/core/login/login.jsp" />
		<security:intercept-url pattern="/core/login/**" filters="none" />
		<security:intercept-url pattern="/core/skin/**" filters="none" />
		<security:intercept-url pattern="/core/js/**" filters="none" />
		<security:intercept-url pattern="/**" access="ROLE_EVERYONE" />
	</security:http>

`auto-config="true"` 表示使用默认的过滤器链条，也可自定义，请参阅官方文档    
`access-denied-page` 无权限时的跳转页  
`security:form-login` 控制登录页面  
`login-page` 登录页  
`authentication-failure-url` 登录失败页  
`default-target-url` 认证且有权限后的默认跳转页  

&emsp;以下是认证管理配置  

	<authentication-manager>  
	    <authentication-provider>  
	        <password-encoder hash="md5">  
	            <salt-source user-property="username"/>  
	        </password-encoder>  
	        <jdbc-user-service data-source-ref="dataSource"  
	            users-by-username-query="select username,password,enabled from tb_user where username = ? and enabled = 1"  
	            authorities-by-username-query="select u.username,r.name from t_user u join user_role ur on u.uid = ur.uid join t_role r on r.rid = ur.rid where u.username = ?" />  
	    </authentication-provider>  
	</authentication-manager>

&emsp;认证提供者默认使用的是DaoAuthenticationProvider，可选配置 密码加密方式，提供数据源 用户信息sql、权限sql 

结合spring framework 将会提供默认的登录提示。不在赘述。  

