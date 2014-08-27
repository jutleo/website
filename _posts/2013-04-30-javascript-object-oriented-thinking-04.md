---
layout: post
title: "javascript 对象化的思考再议 -- 自执行匿名函数"
tagline: "Javascript Object Oriented Thinking"
description: "javascript对象化的思考,学习javascript 要学习它的精髓， 面向对象的javascript！ "
category: javascript
tags: [javascript]
---

### javascript 面向对象的编程  
  
还是那句话：**学习javascript 要学习它的精髓， 面向对象的javascript！**  
  
自执行匿名函数其实是两部分，匿名函数和自执行，匿名函数顾名思义没有名字，先来看看代码：  
最开始我们这个定义一个函数

	function person(email) {
		return 'jutleo' + email;
	}
	
其实还可以这么定义：  
	
	var person = function(email) {
		return 'jutleo' + email;
	}
	
其实还有一种不为人知的方式：  
	
	var person = new Function("email", "return 'jutleo' + email"); 
	
以上三种均可以使用`person('jutleo@gmail.com')`调用，如果这么直接定义是不是也可以被认可？  
	
	function(email){
		return 'jutleo' + email;
	}
	
答案是对的，在js里这么直接定义也是一种定义函数的方式，只是脚步编译器会无法识别，我们可以通过使用`()`包裹使编译器不报错  
	
	(function(email){
		return 'jutleo' + email;
	})
	
这样就定义了一个匿名函数，我们如何运行它呢？也就是让他自执行，很简单，只需要再加一个`()`表示执行  
	
	(function(email){
		return 'jutleo' + email;
	})()
	
运行这个函数会发现返回'jutleoundefined'  为什么呢？当然是这个`email`没有赋值，如何赋值呢？  
	
	(function(email){
		return 'jutleo' + email;
	})('jutleo@gmail.com')  
	
这个很容易理解，第一个`()`表示函数，第二个`()`表示执行函数，如果函数有参数，当然是传递进去了。  

这个里面其实还有一些问题， 关于这种匿名自执行函数的变量作用域问题很值得探讨。下一次聊聊吧。

	