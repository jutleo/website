---
layout: post
title: "javascript 对象化的思考再议 -- 全局导入导出"
tagline: "Javascript Object Oriented Thinking"
description: "javascript对象化的思考,全局导入和全局导出 "
tags: [javascript]
---

### javascript 面向对象的编程  
  
还是那句话：**学习javascript 要学习它的精髓， 面向对象的javascript！**  

**全局导入**     

匿名自执行函数内部的变量都是局部变量，在全局的命名空间里是无法访问到局部变量的。但是在匿名函数内部却是可以使用
全局变量的，而且很容易的操作它们。这个时候就会显得很复杂，尤其是匿名函数多的时候。    
	
	var name = 'jutleo_01';
	(function(){
		name = 'jutleo_02';
	})();
	alert(name); //jutleo_02
	
这样做完全没有问题，但是js解释器会遍历作用域链，让程序变的更慢。改进一下看看：  
	
	var name = 'jutleo_01';
	
	(function(_name){
		name = _name;
	})('jutleo_02');
	alert(name); //jutleo_02  
	
js解释器在局部变量的反应上会读取的更快、更高效！  

这种例子很多，尤其是各大类库里经常会看到， 看看jQuery是如何做的？  
	
	(function($){
		//to do some thing
	})(jQuery);

将jQuery对象传递进匿名函数内部作为局部变量`$`使用，不易产生冲突、简洁、高效。  

**全局导出**  

上面一步步的引入全局导入。全局导出也是类似的原理， 导出和导入是相对的，举一个相对常见的例子来说明什么是全局导出吧：  
	
	(function(exports){
		exports.getName = function(){
			return 'jutleo';
		};
	})(window);
   
将全局的window对象导入匿名函数内部，使用变量名exports暴露全局变量。其实就是将getName方法优雅的暴露出去，代码整洁易懂！   

全局导入、全局导出就这么点原理，一般我们写自己的类库、模块时经常会用到。 提到模块（封装逻辑、避免命名污染），其实匿名自执行就是一个模块原型。
这也是我推荐的一种写法，可以很好的将自己的逻辑封装在匿名自执行函数内部，通过全局导入、导出的方式暴露给外部使用。  

以下改口叫模块了。  
在实际中，模块中的上下文(`this`)是怎样的呢？  
	
	(function(){
		alert(this);
	})();
	
这里的`this`其实就是window对象，也就是说模块中的上下文都是全局的，this指向的是window对象。一不小心就创建了全局变量了。  
那我们如何定义我们自己的作用域的上下文呢？  
	
	(function(){
		var person = {};
		
		person.getName = function(){
			alert(this);
			return 'jutleo';
		};
		
		person.getName();
	})();
	
此时的`this`指向了person对象，使用this就不会担心创建全局变量了。  

javascript中的this和java中的this是不一样的，javascript中的this总是指向调用方，而java中的this总是指向当前对象！ 如果你还不了解，google吧,
不在此做详细解释。
