---
layout: post
title: "javascript高级特性 -- 作用域"
tagline: "学习任何语言都离不开变量的作用域"
categories: javascript
description: "&emsp;学习任何语言都离不开作用域的概念。作用域规范了变量的可见范围及生命周期。正确的理解作用域可以减少程序出错的几率，使代码更优雅，可读，易懂。而且还会减少命名冲突，有利于垃圾回收。总之好处多多啦，还是来看看javascript中的作用域吧！"
tags: javascript 
---

&emsp;作用域在javascript中分为全局作用域和局部作用域，而局部作用域其实指得就是函数作用域，javascript将函数作为作用域的最小范围。
######全局作用域 Global Scope
&emsp;在代码的任何地方都可以访问到的对象，比如window对象及window对象的属性，就拥有全局作用域。  
1、在最外层定义的变量，默认都是window对象的属性；  
2、未定义的变量默认都是全局的，默认也都是window对象的属性； 这一条是经常犯的错误,但是新版浏览器已经修复了这个问题！   
举例如下：
    
    var firstName = "leo";
    function changeName() {
        var secondName = "jut";
        fullName = "jutleo";
        function getName() {
            console.log(secondName);
        }
        getName();
    }
    console.log(firstName);
    console.log(fullName);//报错
    // console.log(secondName);//报错
    changeName();
    // getName();//报错

######局部作用域/函数作用域(Local Scope/Function Scope)
&emsp;在函数中定义一个变量时，这个变量只对当前函数可见,javascript会搜索当前函数的作用域，如果没有找到，则继续向上层搜索，直到在全局作用域也没有找到会返回undefined；
    
    var version = "version_1";
    var f1 = function() {
        console.log(version);//version_1
    }
    f1(); 
&emsp;在f1函数中没有找到，继续往上查找  

    var version = "version_1";
    var f2 = function() {
        var version = "version_2";
        console.log(version);//version_2
    }
    f2();
&emsp;在f2函数中直接找到
    
    var socpe = "scope_01";
    var f3 = function() {
        console.log(scope); //undefined
        var scope = "scope_02";
    }
    f3();
&emsp;这又是为什么呢？原来javascript在f3函数内搜索scope变量，并且找到了，这个时候外层的scope就被忽略了，但是执行到`console.log(scope)`时，scope还没有被初始化，所以返回`undefined`
    
    var f4 = function() {
        var scope = "scope_02";
        (function(){
            var scope = "scope_03";
            (function(){
                console.log(scope); //scope_03
            })();
        })();
    }
    f4();
&emsp;嵌套也是要遵循同样的规则的，先在函数内部查找，没有找到继续往上层找！
    
    var x_scope = "leo";
    var f5 = function() {
        console.log(x_scope); //leo
    }
    var f6 = function() {
        var x_scope = "jut";
        f5();
    }
    f6();
&emsp;这又是为什么呢？原来javascript函数嵌套时，作用域是由嵌套关系决定的，调用的顺序忽略不记！

&emsp;说了这么多，这个作用域如何使用呢？我们从以上例子可以看出，javascript在查找变量时，顺序往上找的这个链条就作用域链。在实际应用中变量的位置越深，读写的速度就越慢，直到找到全局作用域中。  

看例子：  

    function setLabelBgColor(){
        document.getElementById("btn").onclick=function(){
            document.getElementById("label").style.backgroundColor="red";
        };
    }

&emsp;根据作用域链的查找逻辑，查找document变量必须遍历整个作用域链，直到最后在全局对象中才能找到，这个函数引用了两次。优化如下：  

    function setLabelBgColor(){
        var doc = document;
        doc.getElementById("btn").onclick=function(){
            doc.getElementById("label").style.backgroundColor="red";
        };
    }
&emsp;这个只是个例子，这么做并不会大幅提升性能哦！但是在实际使用中要避免全局变量被大量访问的情况。  
    
    (function(exports){
        //do some thing ...
    })(window);

&emsp;思考一下，为什么看到的库、框架都是这么开始的？  