---
layout: post
title: "async javascript"
date: 2012-06-22 14:33
comments: true
categories: [thinking in js]
---

异步的javascript

Ways to do this:

Browser Ways: 
1. use timeout/interval/requestAnimationFrame
2. web worker 
3. promise mode
4. nesting function

Node ways: 1. use process.nexttick

[alternative js](http://altjs.org/)

Rules:
* avoid more than two levels of function nesting 
* Exceptionslive strictly in the land ofsynchronicity. When you reach the event queue,the stack is reset. Everystack trace is an unbroken chain of blocking code

Exceptions in callbacks  
1. 在定时器中异常发生时只有发生出的堆栈信息  
2. 定时器中的异常在外部不能捕获到，可以使用window.onerror来捕获所有的异常  
	setTimeout(function A() {
	setTimeout(function B(){
		try{
			setTimeout(function C(){
				throw new Error('Something ter-rible has happened!');
			},0);	
		}catch(e){
			console.log(e);//定时器中的异常捕获不到
		}
	},0);
	},0);

Tools  
1. ES6  
   *  iterators and generators, yield x
		function simpleGenerator(){  
	      yield "first";  
	      yield "second";  
	      yield "third";  
	      for (var i = 0; i < 3; i++)  
	        yield i;  
	    }  
	      
	    var g = simpleGenerator();  
	    print(g.next()); // prints "first"  
	    print(g.next()); // prints "second"  
	    print(g.next()); // prints "third"  
	    print(g.next()); // prints 0  
	    print(g.next()); // prints 1  
	    print(g.next()); // prints 2  
	    print(g.next()); // StopIteration is thrown  
2. [task.js](http://taskjs.org/)
3. [TameJS](https://githubcom/maxtaco/tamejs)
	* >This package is a source-to-source translator that outputs JavaScript. The input dialect looks a lot like JavaScript, but introduces the await primitive, which allows asynchronous callback style code to work more like straight-line threaded code. tamejs is written in JavaScript.
	One of the core powers of the tamejs rewriting idea is that it's fully compatible with existing vanilla-JS code (like node.js's libraries). That is, existing node.js can call code that's been output by the tamejs rewriter, and conversely, code output by the tamejs rewriter can call existing node.js code. Thus, tamejs is incrementally deployable --- you can keep all of your old code and just write the new bits in tamejs! So try it out and let us know what you think.
4. [StratifiedJS](http://onilabs.com/stratifiedjs)
	* StratifiedJS extends the JavaScript language with a small number of constructs for concurrent programming. It allows you to express asynchronous control flow in a straightforward sequential style:   
	var news;  
	waitfor {  
	  news = http.get("http://news.bbc.co.uk");  
	}  
	or {  
	  hold(1000);  
	  news = http.get("http://news.cnn.com");  
	}  
	or {  
	  hold(1000*60);  
	  throw "sorry, no news. timeout";  
	}  
	show(news);  
5. [Kaffeine](http://weepy.github.com/kaffeine/)
   * >Kaffeine is a set of extensions to the Javascript syntax that attempt to make it nicer to use. It compiles directly into javascript that is very similar, readable and line for line equivalent to the input (you can beautify if you like). 
6. [Streamline.js](https://github.com/Sage/streamlinejs)
7. [node-fibers](https://github.com/laverdet/node-fibers) Fiber/coroutine support for v8 and node

##Distribute Events  
> where a single incident can trigger
reactions throughout our application.

1. PubSub/EventEmitter
2. evented models
	* BackBone.js
3. JQuery custom events
	* Callbacks
	* Namespaced Events
4. [LucidJS](LucidJS)
	* >Lucid is an uber simple and easy to use event emitter library. Lucid allows you to create your own event system and even pipe in events from any number of DOM elements.

> Some applications, particularly Node servers,**use a single global PubSub object to connect otherwise isolated modules,enabling loose coupling and easy testing.** Thegreat thing about PubSubis that when you’re not subscribing tothe events that are being published, it’s completely unobtrusive.

##Promises and Deferreds
>A Promiseis an object that represents a
task with two possible outcomes(successor
failure) and holds callbacks that fire when
one outcome or the other has occurred.
But the biggest advantage of using Promises
is that you can easily derive new Promises
from existing ones.You might ask two Promises representing parallel tasks to give you a
Promise that will inform you of their mutual
completion.Or you might ask a Promise representing the first task in aseries to give you
a Promise representing the final task in the
series. 
	var promise = $.get('/mydata');
	promise.done(onSuccess);
	promise.fail(onFailure);
	promise.fail(onAlways);

###[Promise/A spec](http://wiki.commonjs.org/wiki/Promises/A)
* [KrisKowal’sQ.js](https://github.com/kriskowal/q)
>Qis a fairly straightforward
implementation of the Promises/Aspec
* [AJONeal’s Futures](https://github.com/coolaj86/futures)
>Futures is a broader toolkit, incorporating
manyofthe flow control features found in
libraries like Async.js
* [jQuery 1.5’s Promises](http://api.jquery.com/category/deferred-object/)
	* Every Deferred has a single Promise,and every Promise represents a Deferred.
	* You can trigger a Deferred directly.
	* jQuery 1.6+ provide one more source of Promises: ordinary jQuery objects. 
	* Promises let us see concurrent tasks as booleans.
	  * The most common use case for logically combining Promisesis finding out when a set of async tasks have finished. 
	  * $.when is logical AND
	  * Implements NOT  
		  	function not(promise) {
		  	  var result = new	$.Deferred(); 
		  	  promise.done(result.reject);
			  promise.fail(result.resolve); 
			  return result; 
			}
	  * Implements OR  
		 	function or() {
				var promises=Array.prototype.slice.call(arguments);	
				var negatedPromises=promises.map(not);
				return not($.when.apply(this,negatedPromises));
			}
	  * Why?
	  	1. When a Promise passed to or is resolved, its negation will be rejected,
		as will the Promise returned by
		$.when, and thus the Promise returned by or willbe immediately
		resolved.
		2. When all of the Promises passed to
		and are rejected,their negations will
		all be resolved, as will the Promise
		returned by $.when, and thus the
		Promise returned by or will be
		rejected.
	* Deferred VS Promise 
	  * The most obvious differenceis that the then
		method in faithful Promises/A implementations like Q.js returns a new Promise that
		represents the eventual return value of the
		success/failure handler. It’s a lot like
		jQuery’s pipe method. 
	  * Another big difference, previously mentioned,is that jQuery uses the term “resolve” as he opposite of “fail,”whereasPromises/A		uses “fulfill.” Under Promises/A,a Promise	is said to be “resolved” when it’s either fulfilled or failed.
	  * There are other,subtler differences as well.
		For instance: In Promises/A, whether a
		Promise returned then is fulfilled or rejected depends on whether the invoked callback
		returns a value or throws an error.(Throwing errors from jQuery Promise callbacksis a
		bad idea, as they’ll go uncaught.)
	* When to use?
	  * Ajax
	  * animation
	  * notification
	  * progress
	  * anthing need callback(s)

