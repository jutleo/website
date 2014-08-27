---
layout: post
title: "spring security 自定义认证实现"
tagline: "spring securiy自我理解，及如何解决实际问题"
categories: spring
description: "&emsp;系统中用到spring security框架，集成开源cas实现单点登录 -- 三方认证"
tags: spring 
---
##开篇
&emsp;接上篇，`spring security`为我们实现了基于cas的认证，但是在实际项目中经常会遇到非cas的情况，这就需要我们基于spring security
实现自定义的认证，所谓自定义认证即可以自由扩展实现和第三方的SSO、SAM等无缝对接认证。  

&emsp;本文的前提是你对`spring security`有一定的理解，请参阅之前提到的`spring security`理论，或者查阅官方文档，记得本系列是在2.0.8基础上
做说明。如果是3.x版本会有小的差异。  

&emsp;第三方认证，其实就是提供一个认证提供者，拿到用户名、密码之后调用第三方验证是否合法用户。下来实际操作一番：  

&emsp;首先，你需要实现`AuthenticationProvider`接口，并实现`authenticate`方法和`supports`方法，完整代码如下： 
    
    public class LeoAuthenticationProvider implements AuthenticationProvider{
    
        private UserDetailsService userDetailsService;

        public void setUserDetailsService(UserDetailsService userDetailsService) {
            this.userDetailsService = userDetailsService;
        }

        @Override
        public Authentication authenticate(Authentication authentication) throws AuthenticationException {
            //1、获取用户名、密码
            String username = authentication.getName();
            String password = authentication.getPrincipal().toString();
            
            //2、调用第三方认证
            boolean isSuccess = thirdPartyAuthenticate(username, username);
            
            //3、通过UserDetailsService构建UserDetails
            UserDetails userDtails = null;
            if(isSuccess) {
                userDtails = userDetailsService.loadUserByUsername(username);
                GrantedAuthorityImpl authority = new GrantedAuthorityImpl("ROLE_EVERYONE");
                userDtails.getAuthorities();
            }
            //4、返回令牌对象
            return new UsernamePasswordAuthenticationToken(username, username, userDtails.getAuthorities());
        }

        @Override
        public boolean supports(Class authentication) {
            //判断authentication是不是一个合法的可支持的对象，一般为实现了Authentication接口的实现类，
            //spring security为我们提供了默认的实现类UsernamePasswordAuthenticationToken
            return true;
        }
        //第三方认证实现，这里判断用户名和密码相等就OK，实际根据三方提供的接口来实现，返回boolean值即可
        private boolean thirdPartyAuthenticate(String username, String password) {
            if(username.equals(password)) {
                return true;
            } else {
                return false;
            }
        }
    }

&emsp;代码很简单，有注释，应该不难理解。下来看看如何配置： 
    
    //认证管理
    <security:authentication-manager alias="authenticationManager" />

    //认证提供者
    <bean id="leoAuthenticationProvider" class="org.leo.security.LeoAuthenticationProvider">
        <property name="userDetailsService" ref="userDetailsService"/>
        <security:custom-authentication-provider />
    </bean>

&emsp;OK，至此，一个完整的三方SSO集成已经实现，有兴趣可以自己动手尝试一下。

