---
layout: post
title: "spring security 自定义UserDetailsService"
tagline: "spring securiy自我理解，及如何解决实际问题"
categories: spring
description: "&emsp;系统中用到spring security框架，集成开源cas实现单点登录 -- 自定义UserDetailsService"
tags: spring 
---
##开篇
&emsp;接上篇，一般系统的权限设计不仅仅是`spring security`为我们提供的那么简单，当需要定制时如何处理？

##原理
&emsp;上篇提到，`spring`默认为我们提供了`UserDetailsService`的实现`JdbcDaoImpl`，用来实现用户信息、权限信息的加载，而加载的过程也不需要我们干预，只需要配置简单的
	SQL即可。现实中，用户的权限信息不仅仅这么简单，这时候需要我们自己去扩展UserDetailsService的实现。
实现`UserDetailsSevice`只需要继承自`JdbcDaoImpl`即可，`JdbcDaoImpl`需要注入一个数据源来通过数据库进行验证及获取用户信息、权限信息。

&emsp;`UserDetailsSevice`需要注入两个SQL分别用来查询用户信息、权限信息，对应的属性名称分别为`usersByUsernameQuery`和`authoritiesByUsernameQuery`，我们需要重写`loadUserByUsername`，
此方法将会返回一个`UserDetails`对象，存储用户的上下文信息。

##配置自己的UserDetailsService实现
	
	<bean id="userDetailsService"
		class="org.leo.security.SecurityJdbcDaoImpl" p:dataSource-ref="dataSource" 	
		p:usersByUsernameQuery="SELECT op.C_OPER_ID,op.C_OPER_CNM,opdpt.C_DPT_CDE,dpt.C_DPT_ABR,op.C_PASSWD,op.C_IS_VALID,op.C_OP_DIFF,op.C_DPT_DIFF,op.C_PRD_DIFF,op.C_DPT_PERM,dpt.C_DPT_REL_CDE,dpt.N_DPT_LEVL,op.C_REL_CDE,op.C_SRC,dpt.C_DPT_DISP_CDE,dpt.C_DPT_OUT_CDE
                FROM WEB_ORG_OPER op,WEB_GRT_USR_OP_DPT opdpt,WEB_ORG_DPT dpt
                WHERE  opdpt.C_DPT_CDE = dpt.C_DPT_CDE
                AND op.C_OPER_ID = opdpt.C_OPER_ID
                AND op.C_OPER_ID = ?"
		p:authoritiesByUsernameQuery="SELECT DISTINCT op.C_OPER_ID,r.c_opgrp_cde
				FROM WEB_GRT_ROLE r,WEB_GRT_USR_ROLE ur,WEB_ORG_OPER op,WEB_GRT_USR_OP_DPT opdpt
				WHERE r.c_opgrp_cde =ur.c_opgrp_cde AND ur.C_OPER_ID =opdpt.C_OPER_ID  AND ur.C_OPER_ID =op.C_OPER_ID AND ur.C_DPT_CDE =opdpt.C_DPT_CDE 
				AND op.C_OPER_ID = ?"/>

&emsp;`UserDetailsService`就是一个普通的spring bean，配置起来比较简单。  
&emsp;`org.leo.security.SecurityJdbcDaoImpl`的实现其实父类已经帮我们处理了，看看`spring security`的`JdbcDaoImpl`类源码基本就知道如何写了。  

&emsp;配置好了自己的实现的`UserDetailsService`,如何让`spring security`认识呢？看看下面的配置： 
	
	<security:authentication-provider user-service-ref="userDetailsService"/>

&emsp;这一句即可告诉`spring security`，对于默认的认证管理(`AuthenticationManager`)，默认的认证提供者(`AuthenticationProvider`),我们使用我们自己定义的信息服务。
