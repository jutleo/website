---
layout: post
title: "javascript 对象化的思考"
tagline: "Javascript Object Oriented Thinking"
description: "javascript对象化的思考,学习javascript 要学习它的精髓， 面向对象的javascript！ "
category: javascript
tags: [javascript]
---

### javascript 面向对象的编程  
  
  学习javascript 要学习它的精髓， 面向对象的javascript！  
  
  对象化javascript可以使你的js更清晰、专业！ 下来看一段代码    
	var Person = {
		name : '',
		sex : ''
	};  
	var personA = {};//生成一个空对象
	personA.name = 'jutle01';
	personA.sex = '男';
	var personB = {};
	personB.name = 'jutleo02';
	personB.sex = '女';
  OK, 这就是最简单的对象， 我们发现有几个缺点：    
1. 写起来很麻烦，如果多个对象就很烦人了；  
2. 多个对象没有任何联系；    

我们可以改进一下以上代码：
	function Person(name, sex) {
		this.name = name;
		this.sex = sex;
	}
	var personA = new Person('jutleo01', '男');  
	var personB = new Person('jutleo02', '女');  
  OK， 看起来我们少写了几行代码，而且有了Person构造函数可以初始化一些变量； 而且personA和personB之间貌似是有联系的(同用一个构造函数)，
  如果你还不知道javascript构造函数的概念，你可以自己google一下了；    
  来验证一下：  
	alert(personA.constructor === Person) // true
	alert(personB.constructor === Person) // true
  如果你还不死心，可是使用instanceof检测一下  
	alert(person instanceof Person) //true  
	alert(person instanceof Person) //true  
到目前为止，一切都happy， 但是constructor function有一个问题， 如果我们一直往构造函数里添加属性、方法，是不是会有问题？看看下面的代码

	function Person(name, sex) {
		this.name = name;
		this.sex = sex;
		this.email = 'jutleo@gmail.com';
		this.company = 'NP';
		this.doSomeThing(){
			// to do some thing
		};
	}
	var personA = new Person('jutleo01', '男');  
	var personB = new Person('jutleo02', '女');   
	
貌似也没啥问题，我们来仔细分析一下， `new` 在javascript里会产生一个js对象，每个js对象都有一个指针(对象引用)，都有自己的内存地址；
相当于`personA`和`personB`会在内存里都有一个各自的对象，每个对象都有`name`、`sex`、`email`、`company`、 `doSomeThing`,
而我们最初的想法是想让`Person`类的`email`、`company`、`doSomeThing`是一样的，只有`name`、`sex`大家伙是共享的；问题来了，对于我new出来的对象能不能只保留name、sex的个性化呢？
而其它属性、方法大家伙共享呢？这不是白白的消耗内存么？效率也不高！  

再来改进一下： 
	
	function Person(name, sex) {
		this.name = name;
		this.sex = sex;
	}
	
	Person.prototype.email = 'jutleo@gmail.com';
	Person.prototype.company = 'NP';
	Person.prototype.doSomeThing = function() {
		// to do some thing
	};
  
`prototype`是javascript的一个关键字，每一个`constructor function` 都有个 `prototype`属性，这个属性指向了另外一个对象，指向的这个对象的所有属性和方法
都会被这个构造函数的实例所继承。现在清楚了吧， 这样`Person`类的所有实例都会继承子`prototype`所指向的对象，这样大家伙都共享了对象的所有方法。  

  再来看上面的代码，`personA`对象和`personB`对象精简了不少吧！ 但是！ 全是public的，如果 private、还想节省内存该如何呢？  

  
  ![既要节省内存，又要对象化](/static/img/20130419001.jpg)
  