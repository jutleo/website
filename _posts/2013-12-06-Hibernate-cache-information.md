---
layout: post
title: "Hibernate缓存机制"
tagline: "Hibernate Cache"
categories: ORM
description: "&emsp;Hibernate缓存机制 -- 这篇文字是我之前学习时的记录，已word文档的形式在电脑里放了n多年，现在贴出来分享下。"
tags: Hibernate
---

###`Hibernate`缓存说明
&emsp;`Hibernate`在查询数据时，首先到缓存中去查找，如果找到就直接使用，找不到的时候就会从物理数据源中检索，
所以把频繁使用的数据加载到缓存区后，就可以大大减少应用程序对物理数据源的访问，使得程序的运行性能明显的提升。
###`Hibernate`缓存分类
####一级缓存
&emsp;一级缓存也叫`session`级缓存或事务级缓存，一级缓存的生命周期和`Session`的生命周期一致，
也就是当 `session`关闭时缓存即被清除，一级缓存在`Hibernate`中是不可配置的，即不能被卸载。
####二级缓存
&emsp;二级缓存也称进程级缓存或`SessionFactory`级缓存，二级缓存可以被所有的`session`共享，二级缓存的生命周期和`SessionFactory`的生命周期一致。  
&emsp;二级缓存在`Hibernate`中是可以配置的，可以通过`class-cache`配置类粒度级别的缓存(`class-cache`在`class`中数据发生任何变化的情况下自动更新)，同时也可通过`collection-cache`配置集合粒度级别的缓存(`collection-cache`仅在 `collection`中增加了元素或者删除了元素的情况下才自动更新，也就是当`collection`中元素发生值的变化的情况下它是不会自动更新的)。
####查询缓存
&emsp;查询缓存，查询的结果集也可以被缓存。只有当经常使用同样的参数进行查询时，这才会有些用处。要使用查询缓存，
首先你必须打开它： `hibernate.cache.use_query_cache true`。该设置将会创建两个缓存区域 - 
一个用于保存查询结果集`(org.hibernate.cache.StandardQueryCache)`； 
另一个则用于保存最近查询的一系列表的时间戳`(org.hibernate.cache.UpdateTimestampsCache)`。   
&emsp;请注意：在查询缓存中，它并不缓存结果集中所包含的实体的确切状态；它只缓存这些实体的标识符属性的值、以及各值类型的结果。 
所以查询缓存通常会和二级缓存一起使用。绝大多数的查询并不能从查询缓存中受益，所以`Hibernate`默认是不进行查询缓存的。
如若需要进行缓存，请调用 `Query.setCacheable(true)`方法。这个调用会让查询在执行过程中时先从缓存中查找结果， 
并将自己的结果集放到缓存中去。 查询缓存在`Hibernate`同样是可配置的，默认是关闭的，查询缓存的配置：
在`hibernate.cfg.xml`文件中启用查询缓存，如：
	
    <property name="hibernate.cache.use_query_cache">true</property>
&emsp;在程序中必须手动启用查询缓存，如：

	query.setCacheable(true);

**查询缓存是依赖二级缓存的**
###Session.load
&emsp;在执行`session.load`时，`hibernate`认为该id对应的对象在数据库中一定存在。`load`方法在创建时首先会查询`session`，
看看该`id` 对应的对象是否存在，不存在则创建代理，实际用到该对象中的其他属性时才查询数据库，万一数据库中不存在该记录，才抛出异常。
`Load`方法抛出异常是指在使用该对象的数据时，而不是在创建该对象的数据时。  
*一级缓存的管理:*  
&emsp;`evit(Object obj)`  将指定的持久化对象从一级缓存中清除,释放对象所占用的内存资源,指定对象从持久化状态变为脱管状态,从而成为游离对象;  
&emsp;`clear()`  将一级缓存中的所有持久化对象清除,释放其占用的内存资源  
&emsp;`contains(Object obj)` 判断指定的对象是否存在于一级缓存中.  
&emsp;`flush()` 刷新一级缓存区的内容,使之与数据库数据保持同步.  
&emsp;如何避免一次性大量实体数据入库导致内存溢出：先`flush`，然后`clear`    

*二级缓存的管理:*  
&emsp;`evict(Class arg0)`  将指定类的所有持久化对象从二级缓存中清除,释放其占用的内存资源.  
&emsp;`evict(Class arg0, Serializable arg1)`  将某个类的指定ID的持久化对象从二级缓存中清除,释放对象所占用的资源.  
&emsp;`evictCollection(String arg0)`  将指定类的所有持久化对象的指定集合从二级缓存中清除,释放其占用的内存资源.  
###Session.get
&emsp;在执行`Session.get`时，`hibernate`会确认一下该`id`对应的数据是否存在。
  `get`方法首先在`session`缓存中查询，没有的话然后在二级缓存中查询，最后查询数据库。
###Query.list
  &emsp;在执行`Query.list`时，`Hibernate`的做法是首先检查是否配置了查询缓存，如配置了则从查询缓存中查找key为查询语句+查询参数+分页条件的值，
如获取不到则从数据库中进行获取，从数据库获取到后`Hibernate`将会相应的填充一级、二级和查询缓存，如获取到的为直接的结果集，则直接返回，
如获取到的为一堆`id`的值，则再根据`id`获取相应的值`(Session.load)`，最后形成结果集返回，可以看到，在这样的情况下，`list`也是有可能造成N次的查询的。
查询缓存在数据发生任何变化的情况下都会被自动的清空。
###Query.iterator
&emsp;在执行`Query.iterator`时，和`Query.list`的不同的在于从数据库获取的处理上，`Query.iterator`向数据库发起的是 `select id from`这样的语句，也就是它是先获取符合查询条件的`id`，之后在进行`iterator.next`调用时才再次发起`session.load`的调用获取实际的数据。可见，在拥有二级缓存并且查询参数多变的情况下，`Query.iterator`会比`Query.list`更为高效。


