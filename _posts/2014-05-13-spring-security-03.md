---
layout: post
title: "spring security CAS集成"
tagline: "spring securiy自我理解，及如何解决实际问题"
categories: spring
description: "&emsp;系统中用到spring security框架，集成开源cas实现单点登录 -- CAS集成"
tags: spring 
---
##开篇
&emsp;接上篇，`spring security`如何和`cas`集成？spring-security自己实现了cas的整合，建spring-security-cas jar源码

##先看一个完整的配置
	
	//指定cas的拦截切入点，配置拦截的URL请求地址,引用切入点拦截过滤
	<sec:http entry-point-ref="casProcessingFilterEntryPoint">
    	<sec:intercept-url pattern="/core/login/**" filters="none" />
		<sec:intercept-url pattern="/core/skin/**" filters="none" />
		<sec:intercept-url pattern="/core/common/**" filters="none" />
		<sec:intercept-url pattern="/core/js/**" filters="none" />		
		<sec:intercept-url pattern="/**" access="ROLE_EVERYONE" />
		//指定登出地址，不明白为什么这么设计，应该指定到切入点才对
        <sec:logout logout-success-url="http://localhost:8008/cas/logout"/>
    </sec:http>

    //切入点拦截过滤配置，需要指定登录URL及serviceProperties两个属性
    <bean id="casProcessingFilterEntryPoint" class="org.springframework.security.ui.cas.CasProcessingFilterEntryPoint">
        <property name="loginUrl" value="http://localhost:8008/cas/login"/>
        <property name="serviceProperties" ref="serviceProperties"/>
    </bean>

    //需要配置认证管理器，默认即可，`spring security 3`之后的版本，权限管理及提供者的配置更直观些
    <sec:authentication-manager alias="authenticationManager"/>

    <bean id="casProcessingFilter" class="org.springframework.security.ui.cas.CasProcessingFilter">
        <sec:custom-filter after="CAS_PROCESSING_FILTER"/>
        <property name="authenticationManager" ref="authenticationManager"/>
        <property name="authenticationFailureUrl" value="/casfailed.jsp"/>
        <property name="defaultTargetUrl" value="/"/>
        <property name="proxyGrantingTicketStorage" ref="proxyGrantingTicketStorage" />
        <property name="proxyReceptorUrl" value="/proxy/receptor" />
        <property name="filterProcessesUrl" value="/j_spring_security_check"/>
    </bean>
    //cas权限提供者，由spring实现
    <bean id="casAuthenticationProvider" class="org.springframework.security.providers.cas.CasAuthenticationProvider">
        <sec:custom-authentication-provider /> //标识是自定义来的
        <property name="userDetailsService" ref="userDetailsService"/> //注入userDetailsService实现
        <property name="serviceProperties" ref="serviceProperties" /> //注入serviceProperties属性实现
        //票据验证，由cas-client实现
        <property name="ticketValidator">
            <bean class="org.jasig.cas.client.validation.Cas20ServiceTicketValidator">
                <constructor-arg index="0" value="http://localhost:8008/cas" />
                <property name="proxyGrantingTicketStorage" ref="proxyGrantingTicketStorage" />
                <property name="proxyCallbackUrl" value="http://localhost:8080/leo/proxy/receptor" />
            </bean>
        </property>
        <property name="key" value="an_id_for_this_auth_provider_only"/>
    </bean>

    <bean id="proxyGrantingTicketStorage" class="org.jasig.cas.client.proxy.ProxyGrantingTicketStorageImpl" />

    //切入点需要的serviceProperties属性实现，
    <bean id="serviceProperties" class="org.springframework.security.ui.cas.ServiceProperties">
        <property name="service" value="http://localhost:8008/leo/j_spring_security_check"/>
        <property name="sendRenew" value="false"/>//敏感配置，表示客户端已经存在票据也要去cas-server上重新登录
    </bean>
    <!-- 用户信息 实现了UserdetailsService接口，实际上继承自己JdbcDaoImpl类-->
    <bean id="userDetailsService"
        class="org.leo.security.SecurityJdbcDaoImpl"  lazy-init="true">
        <property name="dataSource">
            <ref bean="dataSource" />
        </property>
        <property name="usersByUsernameQuery">
            <value>
                SELECT op.C_OPER_ID,op.C_OPER_CNM,opdpt.C_DPT_CDE,dpt.C_DPT_ABR,op.C_PASSWD,op.C_IS_VALID,op.C_OP_DIFF,op.C_DPT_DIFF,op.C_PRD_DIFF,op.C_DPT_PERM,dpt.C_DPT_REL_CDE,dpt.N_DPT_LEVL,op.C_REL_CDE,op.C_SRC,dpt.C_DPT_DISP_CDE,dpt.C_DPT_OUT_CDE
                FROM WEB_ORG_OPER op,WEB_GRT_USR_OP_DPT opdpt,WEB_ORG_DPT dpt
                WHERE  opdpt.C_DPT_CDE = dpt.C_DPT_CDE
                AND op.C_OPER_ID = opdpt.C_OPER_ID
                AND op.C_OPER_ID = ?
            </value>
        </property>
        <property name="authoritiesByUsernameQuery">
            <value>
                SELECT DISTINCT op.C_OPER_ID,r.c_opgrp_cde
				FROM WEB_GRT_ROLE r,WEB_GRT_USR_ROLE ur,WEB_ORG_OPER op,WEB_GRT_USR_OP_DPT opdpt
				WHERE r.c_opgrp_cde =ur.c_opgrp_cde AND ur.C_OPER_ID =opdpt.C_OPER_ID  AND ur.C_OPER_ID =op.C_OPER_ID AND ur.C_DPT_CDE =opdpt.C_DPT_CDE 
				AND op.C_OPER_ID = ?
            </value>
        </property>
    </bean>

&emsp;以上都是spring 提供的基于cas-server的安全认证实现配置，照搬即可，注释里有说明。

&emsp;为了能让spring security和客户端结合，我们需要在`web.xml`里加载`springSecurityFilterChain`,指定拦截请求标识，默认配置如下：  
    
    <filter>
        <filter-name>springSecurityFilterChain</filter-name>
        <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>springSecurityFilterChain</filter-name>
        <url-pattern>/j_spring_security_check</url-pattern>
    </filter-mapping>
    <filter-mapping>
        <filter-name>springSecurityFilterChain</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

&emsp;OK，启动你的web工程，看看`spring security`基于开源SSO的实现cas认证有没有正确运行呢？