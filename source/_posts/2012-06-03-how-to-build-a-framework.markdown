---
layout: post
title: "How to build a framework"
date: 2012-06-03 15:40
comments: true
categories: [thinking in JS]
---

If let u to build a javascript framework,how will you do?  
对比几个比较流行的jQuery等框架，不难发现，有以下几个基础部分：  
* 浏览器的兼容性检测  
* 干净，可重用的API设计  
* 有统一的基准和性能  
* 编写有好的轻量的javascript  
* 使用Github  

为了便于合作，我们必须订立好项目的目标和开发的风格,以下是一种尝试：
>
复杂性Verbose: 变量和方法名必须是有意义的，易懂和易理解  
移植性Portable: 浏览器和控制台  
显式的Explicit: 代码应该是可以快速理解的  
注释Comments: 注释需要简洁明了的。TODO 和 FIXME 可以详细一点.  
简单Simple:   保持代码简单  
缩进Indentation: 两个空格  
分号Semicolons:  添加分号以便人们可以方便压缩  
质量Quality: JsLint和读者留言!  
测试Testing: 在浏览器和控制台中都要测试  
版本控制Versioning: 使用GitHub  

高级框架结构  
* prototype  
* namespace  
* traits  
* CommonJS    

Helper Methods  
为什么?  
>reduce the effort required to call commonly used functions  
how?  
> should be succinct but clear  

初始化
* version  
* metadata  

模块和插件
* define your own global namespace,like jQuery,Mooltools etc.  

选择测试框架  
* riot.js  
* jasmine  

面向对象的javascript  
为什么？  
1. 便于管理自己的代码  
2. 对于Java或Ruby程序员来说很习惯这种方式  

原型vs类继承  

原型的优点：  
1. it’s useful to abstract it  
2. offer extra features beyond what JavaScript has as standard

类继承  

优点： 
1. keeps code simpler
2. it easer for people to navigate your code.

缺点：  
1. 创建对象需要用一个包装器，如：Class.create  

创建自己的继承方法：  
1. The ability to extend classes with new methods by copying them  
2. Class creation: use of apply and prototype.constructor to run the constructors  
3. The ability to determine if a parent class is being passed for inheritance  
4. Mixins  

继承：  
	for (var property in source )
		destination [property ] = source [property ];

创建类：  
	// This would be defined in our "oo" namespace
	create : function (methods ) {
		var klass = function () { this.initialize .apply (this, arguments ); };
		// Copy the passed in methods
		extend (klass .prototype , methods );
		// Set the constructor
		klass .prototype .constructor = klass ;
		// If there's no initialize method, set an empty one
		if (!klass .prototype .initialize )
		klass .prototype .initialize = function (){};
		return klass ;
	}

更深层次的类：  
initialize 方法是我们的构造函数。使用Class.create的方法来创建新类。  

语法糖  
Extend === Mixin ?  

Mixin是通过简单的规则将对象的属性进行组合：   
• Methods should be included from the specified classes   
• The initialize method should not be overwritten   
• Multiple includes should be possible  

	var MixinUser = Root.Class ({
		include : User,
		initialize : function (log) {
			this.log = log;
		}
	});

Super  
	var SuperUser = Root.Class(User, {
		initialize : function () {
			this.$super('initialize' , arguments );
		},
		toString : function () {
			return "SuperUser: " + this.$super ('toString' );
		}
	});

函数式编程：  
why?  
1. use ideas from functional programming to simplify common programming tasks  
2. we need define our own method like iterator to cause the browser support is inconsistent  
what?  
• Describing problems rather than focusing on the mechanics of their solution   
• Treating functions as first class citizens, and manipulating them like variables  
• Avoiding state and mutable data   

迭代器Iterators  
>Enumerable uses each to create lots of other methods that are inspired by functional languages. Any collection-style object can mixin Enumerable to get all those methods for free.  

Underscore's each  
	// The cornerstone, an each implementation.
	// Handles objects implementing forEach, arrays, and raw objects.
	// Delegates to JavaScript 1.6's native forEach if available.
	var each = _.forEach = function (obj, iterator , context ) {
	try {
	if (nativeForEach && obj.forEach === nativeForEach ) {
	obj.forEach (iterator , context );
	} else if (_.isNumber (obj.length )) {
	for (var i = 0, l = obj.length ; i < l; i++) iterator .call(context , obj[i], i, obj);
	} else {
	for (var key in obj) {
	if (hasOwnProperty .call(obj, key)) iterator .call(context , obj[key], key, obj);
	}
	}
	} catch (e) {
	if (e != breaker ) throw e;
	}
	return obj;
	};

基准Benchmarks  
>The native method performs very well than defined by yourself  

API设计  
why?  
* easy to use  
* avoid conflict with third party libs   
* safely namespacing  

为each定义更多函数方法  
Filter  
1. Check if there’s a native filter method and use it if possible  
2. Else use Root.enumerable.each  
3. Filter objects into multi-dimensional arrays if required  
Detect  
>Detect simply uses each with the user-supplied callback, until a truthy value is returned. Then it throws a Break.  
Chaining  
>Chained functions are possible when each function returns an object that can be used to call the next one.  
1. Store temporary values  
2. Runs appropriate methods from Root.enumerable by mapping the temporary value into the first argument  
3. After running the method, return this so the chain can continue  

实现：  
	// store temporary values in this.results
	Root.enumerable .Chainer = Root.Class ({
		initialize : function (values ) {
		this.results = values ;
		},
		values : function () {
		return this.results ;
		}
	});
	// Map selected methods by wrapping them in a closure that returns this each time
	Root.enumerable .each(['map' , 'detect' , 'filter' ], function (methodName ) {
		var method = Root.enumerable [methodName ];
		Root.enumerable .Chainer .prototype [methodName ] = function () {
			var args = Array .prototype .slice .call(arguments );
			args.unshift (this.results );
			this.results = method .apply (this, args);
			return this;
		}
	});



选择器和引擎(Selector and Selector Engines)  
1. XPath  
2. CSS  

浏览器支持   
Sizzle的做法：  
>It creates a fake element to probe browser behaviour. Supporting browsers is a black art beyond the patience of most well-meaning JavaScript hackers.  
	// Check to see if the browser returns elements by name when
	// querying by getElementById (and provide a workaround)
	(function (){
		// We're going to inject a fake input element with a specified name
		var form = document .createElement ("div" ),
		id = "script" + (new Date()).getTime ();
		form.innerHTML = "<a name='" + id + "'/>" ;
		// Inject it into the root element, check its status, and remove it quickly
		var root = document .documentElement ;
		root.insertBefore ( form, root.firstChild );
性能


