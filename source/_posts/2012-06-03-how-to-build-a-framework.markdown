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

##为了便于合作，我们必须订立好项目的目标和开发的风格,以下是一种尝试：
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

##高级框架结构  
* prototype  
* namespace  
* traits  
* CommonJS    

###Helper Methods  
为什么?  
>reduce the effort required to call commonly used functions  
how?  
> should be succinct but clear  

###初始化
* version  
* metadata  

###模块和插件
* define your own global namespace,like jQuery,Mooltools etc.  

###选择测试框架  
* riot.js  
* jasmine  

##面向对象的javascript  
为什么？  
1. 便于管理自己的代码  
2. 对于Java或Ruby程序员来说很习惯这种方式  

###原型vs类继承  

####原型的优点：  
1. it’s useful to abstract it  
2. offer extra features beyond what JavaScript has as standard

####类继承  

优点： 
1. keeps code simpler
2. it easer for people to navigate your code.

缺点：  
1. 创建对象需要用一个包装器，如：Class.create  

###创建自己的继承方法：  
1. The ability to extend classes with new methods by copying them  
2. Class creation: use of apply and prototype.constructor to run the constructors  
3. The ability to determine if a parent class is being passed for inheritance  
4. Mixins  

###继承：  
	for (var property in source )
		destination [property ] = source [property ];

###创建类：  
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

###更深层次的类：  
initialize 方法是我们的构造函数。使用Class.create的方法来创建新类。  

###语法糖  
Extend === Mixin ?  

Mixin是通过简单的规则将对象的属性进行组合：    Methods should be included from the specified classes    The initialize method should not be overwritten    Multiple includes should be possible  

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

##函数式编程：  
why?  
1. use ideas from functional programming to simplify common programming tasks  
2. we need define our own method like iterator to cause the browser support is inconsistent  
what?   Describing problems rather than focusing on the mechanics of their solution    Treating functions as first class citizens, and manipulating them like variables   Avoiding state and mutable data   

###迭代器Iterators  
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

###基准Benchmarks  
>The native method performs very well than defined by yourself  

###API设计  
why?  
* easy to use  
* avoid conflict with third party libs   
* safely namespacing  

###为each定义更多函数方法  
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

###实现：  
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



##选择器和引擎(Selector and Selector Engines)  
1. XPath  
2. CSS  

###浏览器支持   
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

###性能  
1. 缓存
2，使用native的方法，如：querySelectorAll

###tools  
1. [slickspeed](http://mootools.net/slickspeed/)

###选择器引擎  
1. [Peppy](http://jamesdonaghue.com/static/peppy/docs/)  
2. [sly](http://digitarald.de/journal/89737433/rolling-out-sly-the-javascript-selector-engine/)  
3. [Sizzle](http://sizzlejs.com/)  

###API 设计  
1. 使用函数包装器，链式调用
2. 返回并扩展选择的元素，如prototype使用$()使用getElementById.$$()使用CSS或XPath
 
###目标  
1. CSS selectors only (XPath could be a future upgrade or plugin)  
2. Limited pseudo-selectors. Implementing one or two would be nice just to establish the API for them   
3. Defer to native methods as quickly as possible   
4. Cache for performance   
5. Expose the parsing results like Sly does  
6. Reuse the wisdom of existing selector engines for patching browser nightmares   

###CSS 选择器  
> [CSS2 selectors](http://www.w3.org/TR/CSS2/selector.html),[CSS2 grammer](http://www.w3.org/TR/CSS21/grammar.html)  
1. E – Matches any element named E  
2. E F – Matches any element F that descends from E  
3. .class – Matches any element with the class class  
4. E.class – Matches any element named E with the class class  
5. #id – Matches any element with the id id  
6. E#id – Matches any element named E with the id id  

###CSS的解析规则 
1. ID Rules  
2. Class Rules  
3. Tag Rules  
4. Universal Rules  
[Writing_Efficient_CSS](https://developer.mozilla.org/en/Writing_Efficient_CSS)  

1. Avoid universal rules  
2. Don’t qualify ID rules with tag names or classes   
	>BAD
	button#backButton {…}  
	BAD  
	.menu-left#newMenuIcon {…}  
	GOOD  
	#backButton {…}  
	GOOD  
	#newMenuIcon {…}   
3. Don’t qualify class rules with tag names  
	>BAD  
	treecell.indented {…}  
	GOOD  
	.treecell-indented {…}  
	BEST  
	.hierarchy-deep {…}  
4. Use the most specific category possible  
	>BAD  
	treecell.indented {…}  
	GOOD  
	.treecell-indented {…}  
	BEST  
	.hierarchy-deep {…}  
5. Avoid the descendant selector  
	>BAD  
	treehead treerow treecell {…}  
	BETTER, BUT STILL BAD (see next guideline)    
	treehead > treerow > treecell {…}  
6. Tag category rules should never contain a child selector  
	>BAD  
	treehead > treerow > treecell {…}  
	GOOD  
	.treecell-header {…}  
7. Question all usages of the child selector  
	>BAD  
	treeitem[IsImapServer="true"] > treerow > .tree-folderpane-icon {…}  
	GOOD  
	.tree-folderpane-icon[IsImapServer="true"] {…}	  
8. Rely on inheritance  
	>BAD  
	#bookmarkMenuItem > .menu-left { list-style-image: url(blah) }  
	GOOD  
	#bookmarkMenuItem { list-style-image: url(blah) }    
9. Use -moz-image-region for ff!  
10. Use [scoped stylesheets](http://updates.html5rocks.com/2012/03/A-New-Experimental-Feature-style-scoped) to reduce inefficiency of universal rules and child selectors  

###分词器(Tokenizer)  
###给你一个selector，我们需要做：  
1. 去掉额外的空格  
2. 用某种方法处理并转化为一系列的指令解析器处理  
3. Run the tokens through the parser against the DOM  

	function Token (identity , finder ) {
		this.identity = identity ;
		this.finder = finder ;
	}
	Token .prototype .toString = function () {
		return 'identity: ' + this.identity + ', finder: ' + this.finder ;
	};
	//The finder property is one of those keys in findMap. The identity is the original rule from the selector.  
###扫描器(Scanner)  
> CSS grammer specification
	macros = {
		'nl': '\n|\r\n|\r|\f' ,
		'nonascii' : '[^\0-\177]' ,
		'unicode' : '\\[0-9A-Fa-f]{1,6}(\r\n|[\s\n\r\t\f])?'
		'escape' : '#{unicode}|\\[^\n\r\f0-9A-Fa-f]' ,
		'nmchar' : '[_A-Za-z0-9-]|#{nonascii}|#{escape}'
		'nmstart' : '[_A-Za-z]|#{nonascii}|#{escape}' ,
		'ident' : '[-@]?(#{nmstart})(#{nmchar})*' ,
		'name' : '(#{nmchar})+'
	};
	rules = {
		'id and name' : '(#{ident}##{ident})' ,
		'id': '(##{ident})' ,
		'class' : '(\\.#{ident})' ,
		'name and class' : '(#{ident}\\.#{ident})' ,
		'element' : '(#{ident})' ,
		'pseudo class' : '(:#{ident})'
	};

###扫描器将做以下事情：
* Expanding #{} in the macros  
* Expanding #{} in the rules based on the expanded macros  
* Escaping the backslashes  
* Joining each of the patterns with |  
* Building a global regular expression with the RegExp class  

###搜索器(Searcher)  
搜索器使用分词器的处理结果来搜索dom。初始化时需要指定搜索的root元素和一个token数组。
	find = {
	byId: function (root, id) {
		return [root.getElementById (id)];
	},
	byNodeName : function (root, tagName ) {
		var i, results = [], nodes = root.getElementsByTagName (tagName );
		for (i = 0; i < nodes .length ; i++) {
			results .push(nodes [i]);
		}
		return results ;
	},
	byClassName : function (root, className ) {
		var i, results = [], nodes = root.getElementsByTagName ('*');
		for (i = 0; i < nodes .length ; i++) {
			if (nodes [i].className .match ('\\b' + className + '\\b' )) {
				results .push(nodes [i]);
			}
		}
		return results ;
	}
	};
	findMap = {
	'id': function (root, selector ) {
		selector = selector .split ('#')[1];
		return find.byId(root, selector );
	},
	'name and id' : function (root, selector ) {
		var matches = selector .split ('#'), name, id;
		name = matches [0];
		id = matches [1];
		return filter .byAttr (find.byId(root, id), 'nodeName' , name.toUpperCase ());
	}
	// ...
	};

##Events
###基本的事件绑定方式  
1. inline  
2. element.onXXX   
###访问事件  
event或者window.event，虽然直接访问window.event有潜在的危险，因为window.event是保存在上一次的事件对象，但javascript是单线程的，所以他是安全的。  
中断事件(Stopping Events)  
1. return false
###事件捕获和冒泡  
why？  
1. 同时绑定给两个父子级元素绑定click事件，很难确定事件的执行顺序  
2. 不同的浏览器有不同的处理方法  
How？  
	function handler (event ) {
		if (!event ) var event = window .event ;
		event .cancelBubble = true;
		if (event .stopPropagation ) event .stopPropagation ();
	}
###多个事件处理器  
eg：
	element .onclick = function () { alert ('Hello World!' ); return false ; };
	element .onclick = function () { alert ('This was the best example I could think of'); return false;}
	//这里第二个事件会将第一个事件覆盖

###事件api的定义
1. Normalise event names, so onClick becomes click  
2. Easy event registration and removal  
3. Simple cross-browser access to the event object in handlers  
4. Mask the complexities of event bubbling  
5. Provide cross-browser access to keyboard and mouse interaction  
6. Patch browser incompetency like IE memory leaks  
jQuery的做法   
>$('a').click (function (event ) {  
alert ('A link has been clicked' );  
});  
The Event’s element is in event.target  
The current event within the bubbling phase is in event.currentTarget — also found in the
this object in the function    
The default action can be prevented with event.preventDefault()  
Bubbling can be stopped with event.stopPropagation();  
Events can be removed with $('a').unbind('click');  
Events can be fired with $('a').trigger('click');  
 

Prototype的做法  
>$('id').observe ('click' , function (event ) {  
var element = Event .element (event );  
});  
The Event’s element can be accessed with Event.element(event) or event.element();  
Events are stopped with Event.stop()  
Events can be removed with Event.stopObserving(element, eventName, handler);  
Events can be fired with Event.fire(element)  

Glow的做法	
>glow.events .addListener ('a', 'click' , function (event ) {    
alert ('Hello World!' );  
});  
The Event’s element is in event.source  
The default action can be prevented by returning false  
Events can be removed with glow.events.removeListener(handler) where handler is  
the returned value from glow.events.addListener  
Events can be fired with glow.events.fire()  

Dojo的做法  
>dojo.connect (dojo.byId('a#hello' ), 'onclick' , function (event ) {  
	alert ('Hello World!' );  
});  
Notice that the event names aren’t mapped. Like the other frameworks, the event object is normalised.  
The Event’s element is in event.target  
The current event within the bubbling phase is in event.currentTarget — this is also this in
the function  
The default action can be prevented with event.preventDefault()  
Bubbling can be stopped with event.stopPropagation();  
Events can be removed with dojo.disconnect()   

###总结  
在以上的framework中，jquery的event handler是最具有魅力的，首先，它统一使用W3C的事件系统在各个浏览器中，较好的名称空间，完全解决了事件冒泡问题，这主要是由于Micorosoft的API在冒泡时的问题。

###实现   
1. event.srcElement , event.target  
2. 事件绑定  attachEvent,addEventlistener  
3. 验证元素是否有效，确保元素非文本和注释节点    
4. 阻止默认和冒泡事件  
5. 其他的浏览器修复  
6. NodeList
7. 事件代理  
	//Safari’s handling of text nodes  
	//Missing values for event.pageX/Y  
	//Key events get event.which and event.metaKey is corrected  
	//event.which is also added for mouse button index
	function stop(event ) {
		event .preventDefault (event );
		event .stopPropagation (event );
	}
	function fix(event , element ) {
		if (!event ) var event = window .event ;
			event .stop = function () { stop(event ); };
		if (typeof event .target === 'undefined' )
			event .target = event .srcElement || element ;
		if (!event .preventDefault )
			event .preventDefault = function () { event .returnValue = false ; };
		if (!event .stopPropagation )
			event .stopPropagation = function () { event .cancelBubble = true; };
		return event ;
	}
	var _isButton ;
	if (Prototype .Browser .IE) {
		// IE doesn't map left/right/middle the same way.
		var buttonMap = { 0: 1, 1: 4, 2: 2 };
		_isButton = function (event , code) {
		return event .button === buttonMap [code];
	} else if (Prototype .Browser .WebKit ) {
		// In Safari we have to account for when the user holds down
		// the "meta" key.
		_isButton = function (event , code) {
		switch (code) {
			case 0: return event .which == 1 && !event .metaKey ;
			case 1: return event .which == 1 && event .metaKey ;
			default : return false ;
		}
	} else {
		_isButton = function (event , code) {
		return event .which ? (event .which === code + 1) : (event .button === code);
	}

##Ajax  

[XMLHttpRequest](http://www.w3.org/TR/XMLHttpRequest/)  
###how to cross domain ?  
1. use flash or Silverlight  
2. use XMLHttpRequest Level2 withCredentials attribute  
3. using dynamically inserted script tags, image tags, and iframes.  
4. JSONP  
5. Cross-origin resource sharing ,eg: Access-Control-Allow-Origin: *  
6. use XDomain object for IE  

###创建兼容的XMLHttpRequest对象  
1. Differences between Microsoft’s implementation and W3C have to be dealt with  
2. Request headers must be set for the type of data and HTTP methpd  
3. State changes must be handled through callbacks  
4. Browser bugs must be compensated for  

	function xhr() {
	if (typeof XMLHttpRequest !== 'undefined' && (window .location .protocol !== 'file:' || !window.ActiveXObject)){
	return new XMLHttpRequest ();
	} else {
	try {
	return new ActiveXObject ('Msxml2.XMLHTTP.6.0' );
	} catch (e) { }
	try {
	return new ActiveXObject ('Msxml2.XMLHTTP.3.0' );
	} catch (e) { }
	try {
	return new ActiveXObject ('Msxml2.XMLHTTP' );
	} catch (e) { }
	}
	return false ;
	}

###发送请求  
1. Set a callback for onreadystatechange  
2. Call open on the request object  
3. Set the request headers  
4. Call send on the object  

>readystate
0: Uninitialized: open has not been called
1: Loading: send has not been called
2: Loaded: send has been called, headers and status are available
3: Interactive: Downloading, responseText holds the partial data
4: Completed: Request finishe 

###ajax API设计  
jQuery  
	$.ajax({
	url: url,
	data: data,
	success : success ,
	dataType : dataType
	});
Prototype  
	new Ajax.Request ('/your/url' , {
		onSuccess : function (response ) {
		// Handle the response content...
		}
	});
Glow  
	if (response .wasSuccessful ) {
		events .fire(request , "load" , response );
	} else {
		events .fire(request , "error" , response );
	}

##动画(Animations)  
###JavaScript动画  
Methods to work with CSS properties and animate them  
A queuing system, for scheduling animations, and animating each “frame” CSS colour parsing  
Helpers that make common web effects easy to use alongside events  
###动画框架  
1. 最流行的框架[aculo](http://madrobby.github.com/scriptaculous/combination-effects-demo/)  
2. Mootools [FX](http://mootools.net/docs/core/Fx/Fx)   
3. jQuery [animate](http://api.jquery.com/animate/)  
4. Glow [animation](http://www.bbc.co.uk/glow/docs/1.7/api/glow.anim.animation.shtml)  
###动画队列  
setInterval 和 clearInterval 来处理事件序列   
Easing function  
>“Ease-in” and “ease-out” in digital animation typically refer to a mechanism for defining the
‘physics’ of the transition between two animation states, eg. the linearity of a tween.  

###编写 easing 函数
	var easing = {};
	easing .linear = function (position ) {
		return position ;
	};
	easing .sine = function (position ) {
		return (-Math.cos(position * Math.PI) / 2) + 0.5;
	};
	easing .sine = function (position ) {
		return (-Math.cos(position * Math.PI) / 2) + 0.5;
	};

###动画helper函数 -- 一些比较常用的特效的封装  
* Fade — fade an element by changing its opacity  
* Highlight — rapidly change an element’s background colour to draw attention to it  
* Movement — move an element  
	root.anim.fade(element , duration , { 'from' : '8em' , 'to': '100px' , 'easing' : easing });
	anim.fadeIn = function (element , duration , options ) {
						options = options || {};
						options .from = options .from || 0.0;
						options .to = options .to || 1.0;
						return anim.fade(element , duration , options );
					};
	anim.fadeOut = function (element , duration , options ) {
						var from;
						options = options || {};
						options .from = options .from || 1.0;
						options .to = options .to || 0.0;
						// Swap from and to
						from = options .from;
						options .from = options .to;
						options .to = from;
						// This easing function reverses the position value and adds from
						options .easing = function (p) { return (1.0 - p) + options .from; };
						return anim.fade(element , duration , options , { 'easing' : options .easing });
					};
###使用CSS3
>
CSS Transitions Module Level 3  
CSS 2D Transforms Module Level 3  
CSS 3D Transforms Module Level 3  
CSS Animations Module Level 3  

	.example {
		-webkit -transition : all 1s ease-in-out;
		-moz-transition : all 1s ease-in-out;
		-o-transition : all 1s ease-in-out;
		-webkit -transition : all 1s ease-in-out;
		transition : all 1s ease-in-out;
	}  

CSS3 动画  
use keyframe  
	@keyframes 'wobble' {
	0% {
	left: 100px;
	}
	40% {
	left: 150px;
	}
	60% {
	left: 75px;
	}
	100% {
	left: 100px;
	}
	}
###动画性能  
1. Animating margin, padding, background, outline, and border will all result in relatively
slow animations.  
2. Hardware Acceleration  
###特性检测
是否支持硬件加速   
	function isHWAcceleratedSafari () {
		var ua = navigator .userAgent , av = navigator .appVersion ;
		return (!ua.include ('Chrome' ) && av.include ('10_6' )) ||
		Prototype .Browser .MobileSafari ;
	}
是否支持WebKitTransitionEvent 或 MozTransition  

	var div = document .createElement ('div' );
	try {
		document .createEvent ("WebKitTransitionEvent" );
		supported = true;
		hardwareAccelerationSupported = isHWAcceleratedSafari ();
	} catch (e) {
	if (typeof div.style .MozTransition !== 'undefined' ) {
		supported = true;
	}
	}
	div = null;
查询浏览器厂商支持
	// CSS3 vendor detection
	vendors = {
	// Opera Presto 2.3
	'opera' : {
		'prefix' : '-o-' ,
		'detector' : function () {
			try {
				document .createEvent ('OTransitionEvent' );
				return true;
			} catch (e) {
				return false ;
			}
		}
	},
	// Chrome 5, Safari 4
	'webkit' : {
		'prefix' : '-webkit-' ,
		'detector' : function () {
			try {
				document .createEvent ('WebKitTransitionEvent' );
				return true;
			} catch (e) {
				return false ;
			}
		}
	},
	// Firefox 4
	'firefox' : {
		'prefix' : '-moz-' ,
		'detector' : function () {
			var div = document .createElement ('div' ),
			supported = false ;
			if (typeof div.style .MozTransition !== 'undefined' ) {
				supported = true;
			}
				div = null;
				return supported ;
			}
		}
	};
	function findCSS3VendorPrefix () {
		for (var detector in vendors ) {
			detector = vendors [detector ];
			if (detector ['detector' ]()) {
				return detector ['prefix' ];
			}
		}
	}
###移动动画的实现	
Detect when a CSS property is being used to move an element  
Get the vendor prefix  
Set up CSS3 style properties instead of using JS animation engine  

##支持TouchScreen  
###目前支持的framework：
1. [jQTouch](http://www.jqtouch.com/) : iPhone, iPad and Android phones  
2. Sencha Touch  

###Debugging Touch
1. [Android Debug Bridge](http://developer.android.com/guide/developing/tools/adb.html)   
2. Iphone debug console  
3. [Weinre](http://people.apache.org/~pmuellr/weinre/docs/latest/)  

###旋转  Orientation Property  
	root.touch.orientation = function () {
		var orientation = window .orientation ,
		orientationString = '';
		switch (orientation ) {
			case 0:
			orientationString += 'portrait' ;
			break ;
			case -90:
			orientationString += 'landscape right' ;
			break ;
			case 90:
			orientationString += 'landscape left' ;
			break ;
			case 180:
			orientationString += 'portrait upside-down'
			break ;
		}
		return [orientation , orientationString ];
	};
Touch Events  
touchstart  
touchmove  
touchend  
touchcancel   

###Touch 事件对象
event.touches: All touches on the page  
event.targetTouches: Touches for the target element  
event.changedTouches: Changed touches for this event  

###Touch state  
If there’s just one event and the position hasn’t changed, it’s a tap event  
If touchmove fired, work out the distance of the movement and how long it took for a swipe  


##链式调用API 
[fakeQuery](http://code.google.com/p/fakequery/)  

###总结  
A “container function” is used to create new objects without having to type new  
It returns objects based on a CSS selector  
A class is created and copied so usage of methods like find can be called in a chain  

###Event Handler Shortcuts and Loop Scoping

	events .addDOMethods = function () {
		if (typeof root .domChain === 'undefined' ) return ;
		root .domChain .bind = function (type, handler ) {
		var element = this.first ();
		if (element ) {
			root .events .add(element , type, handler );
			return this;
		}
	};
	var chainedAliases = ('click dblclick mouseover mouseout mousemove ' +
	'mousedown mouseup blur focus change keydown ' +
	'keypress keyup resize scroll' ).split (' ');
		for (var i = 0; i < chainedAliases .length ; i++) {
			(function (name) {
			root .domChain [name] = function (handler ) {
			return this.bind(name, handler );
			};
			})(chainedAliases [i]);
		}
	};

##特性检测  eg：querySelectorAll    
###实现
	var testCache = {},
	detectionTests = {};
	root.addDetectionTest = function (name, fn) {
		if (!detectionTests [name])
		detectionTests [name] = fn;
		};
		root.detect = function (testName ) {
		if (typeof testCache [testCache ] === 'undefined' ) {
		testCache [testName ] = detectionTests [testName ]();
		}
		return testCache [testName ];
	};
	//检测querySelectorAll
	turing .addDetectionTest ('querySelectorAll' , function () {
		var div = document .createElement ('div' );
		div.innerHTML = '<p class="TEST"></p>' ;
		// Some versions of Safari can't handle uppercase in quirks mode
		if (div.querySelectorAll ) {
			if (div.querySelectorAll ('.TEST' ).length === 0) return false ;
			return true;
		}
		// Helps IE release memory associated with the div
		div = null;
		return false ;
	});