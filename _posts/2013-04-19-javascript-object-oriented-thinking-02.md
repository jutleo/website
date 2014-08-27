---
layout: post
title: "javascript 对象化的思考续"
tagline: "Javascript Object Oriented Thinking"
description: "javascript对象化的思考,学习javascript 要学习它的精髓， 面向对象的javascript！ "
category: javascript
tags: [javascript]
---

### javascript 面向对象的编程  
  
还是那句话：**学习javascript 要学习它的精髓， 面向对象的javascript！**  
  
接上篇，看如下代码：  
	
	function Person(name, sex) {
		this.name = name;
		this.sex = sex;
	}
	
	Person.prototype.email = 'jutleo@gmail.com';
	Person.prototype.company = 'NP';
	Person.prototype.doSomeThing = function() {
		// to do some thing
	};
	
如果对于`email`属性，`company`属性要私有化，并且对象内部也要共享（指针指向同一个）该如何去做呢？  

对于这个问题，我想好了久，从两个方面考虑：  

1. 这么做有必要么？直接私有化让所有对象都copy一份没啥错误，至于说那些小的可怜的内存占有完全可以忽略。  
2. 如果非要这么做该如何实现？  
  
第一种完全没有必要想其它了 直接这么修改就可以：  
	
	function Person(name, sex) {
		this.name = name;
		this.sex = sex;
		var email = 'jutleo@gmail.com';
		var company = 'NP';
		this.contactWay = function() {
			return company + ";" + email;
		}
	}
	
	Person.prototype.doSomeThing = function() {
		// to do some thing
	};
	
	var personA = new Person('jutleo_01', '男');
	var personB = new Person('jutleo_02', '女');

`personA`和`personB`两个对象分别都有一份`email`,`company`, `contactWay`的copy；  
    
第二种才是我想思考的，如果不对当前对象累加内存，那应该这么做：  
	
	var Person = (function(){
		var email = 'jutleo@gmail.com';
		var company = 'NP';
		
		function contactWay() {
			return company + ";" + email;
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

我们来分析下，以上代码。使用一个匿名函数包裹`Person`类，并立即执行。匿名函数返回'Person'类的引用。应该这么调用  
	
	var personA = new Person('jutleo_01', '男');
	var personB = new Person('jutleo_02', '女');
	
此时就共享了`email`, `company`, `contactWay`， 而且加快了浏览器的响应。  

以上是我个人的想法，算是对上篇遗留问题的一个解决吧，不代表正确的想法！
  