---
layout: post
title: "javascript 对象化的思考续二"
tagline: "Javascript Object Oriented Thinking"
description: "javascript对象化的思考,学习javascript 要学习它的精髓， 面向对象的javascript！ "
category: javascript
tags: [javascript]
---

### javascript 面向对象的编程  
  
还是那句话：**学习javascript 要学习它的精髓， 面向对象的javascript！**  

还是再来看上篇，看如下代码：  
	
	function Person(name, sex) {
		this.name = name;
		this.sex = sex;
	}
	
	Person.prototype.email = 'jutleo@gmail.com';
	Person.prototype.company = 'NP';
	Person.prototype.doSomeThing = function() {
		// to do some thing
	};
	
上篇提到使用闭包的匿名函数实现私有化、对象共享。 其实还可以效率更高、更简单！来看看这么一段代码 ：  
	
	var email = 'jutleo@gmail.com';
	var company = 'NP';
	var doSomeThing = function() {
		// to do some thing
	};
	
貌似没什么问题， 我们再来看看以下改写：  
	
	var PersonExt = {
		email : 'jutleo@gmail.com',
		company : 'NP',
		doSomeThing : function(){
			// to do some thing
		}
	};
	
哪个更优一点呢？ 从面向对象的角度，第二种更偏向封装和结构的合理。从js安全的角度来看，第二种将`email`, `company`, `doSomeThing`
都包裹在PersonExt里，有效的控制了变量的范围。从效率的角度将，js引擎处理第二种的速度会优于第一种。

OK，再来看看我们如何去改写上文提到的私有化、共享。  
	
	var Person = (function(){
		var personExt = {
			email : 'jutleo@gmail.com',
			company : 'NP',
			contactWay : function() {
				return company + ";" + email;
			}
		};
		function Person(name, sex) {
			this.name = name;
			this.sex = sex;
		}
		
		Person.prototype.doSomeThing = function() {
			//to do some thing
		};
		
		return Person;
	})();
	
	var personA = new Person('jutleo_01', '男');
	var personB = new Person('jutleo_02', '女');

`email`, `company`, `contactWay`全部被包裹在变量`personExt`中，在整个Person类中私有化，并且所有对象公用一个。 
	
上篇写完之后又发现可以再优化一点，就记录下来了，纯属个人思考，不代表正确的想法！
  