---
layout: post
title: "async javascript"
date: 2012-06-22 14:33
comments: true
categories: [thinking in JS]
---

异步的javascript

##Ways to do this:

###Browser Ways: 
1. use timeout/interval/requestAnimationFrame
2. web worker 
3. promise mode
4. nesting function

###Node ways: 1. use process.nexttick

[alternative js](http://altjs.org/)

##Rules
* avoid more than two levels of function nesting 
* Exceptionslive strictly in the land ofsynchronicity. When you reach the event queue,the stack is reset. Everystack trace is an unbroken chain of blocking code

##Exceptions in callbacks  
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
	One of the core powers of the tamejs rewriting idea is that it's fully compatible with existing vanilla-JS code (like node.js's libraries). That is, existing node.js can call code that's been output by the tamejs rewriter, and conversely, code output by the tamejs rewriter can call existing node.js code. Thus, tamejs is incrementally deployable --you can keep all of your old code and just write the new bits in tamejs! So try it out and let us know what you think.
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

##[Flow control](http://en.wikipedia.org/wiki/Flow_control) with [Aync.JS](https://github.com/caolan/async)

	Conditional Statements

	    if statements
	    if ... else ... statements 

	Looping Statements

	    while loops
	    do ... while loops 

	switch Statements

	label Statements

	with Statements 

### The async ordering problem
* Example   
    * Read all files content in a dir,we must make sure all contents is correct readed      

		var fs = require('fs');  
		var concatenation='';  
		fs.readdirSync('recipes')  
		.filter(function(filename){  
			return fs.statSync(filename).isFile();  
		})  
		.forEach(function(filename){  
			concatenation+=fs.readFileSync(filename,'utf8')  
		});  
		console.log(concatenation);  

	Or 

		var fs = require('fs');
		var concatenation='';
		var dirContents=fs.readdirSync('recipes');
		function readFileAt(i){
			if(i===dirContents.length)return 
			 console.log(concatenation);
			fs.stat(filename,function(err,stats){
				if(err)throw err;
			if(!stats.isFile())return readFileAt(i+1);
				fs.readFile(dirContents[i],'ut-f8',function(err,fileContents){
			if(err)throw err;
				concatenation+=fileContents;
				readFileAt(i+1);
			});
			});
		}
		readFileAt(0);
* We need async series method  
	* First,there’s a limit on the number of files 	that Node(or any application process) can try to read simultaneously.
	* Second,if we ran the readFile callback in
	parallel, we couldn’t just write concatenation += fileContents;
	* So we stick to async.forEachSeries for now    
**var async= require('async');   
var fs=require('fs');   
var concatenation='';   
var dirContents=fs.readdirSync('recipes');   
async.filter(								         	dirContents,isFilename,function(filenames){   
	async.forEachSeries(   filenames,readAndConcat,onComplete);   
});   
function onComplete(err){   
	if(err)throw err;   
		console.log(concatenation);   
	}   
function isFilename(filename,callback){   
	fs.stat(filename,function(err,stats){   
		callback(err?false:stats.isFile());   
	});   
}   
function readAndConcat(filename,callback){   
	fs.readFile(filename,'utf8',function(   err,fileContents){   
		if(err)return callback(err);   
		concatenation+=fileContents;   
		callback();   
	});   
}**   
* array utilities in async.js
	* reject/rejectSeries, the inverse of filter
	* map/mapSeries, for 1:1 transformations 
	* reduce and reduceRight, for transforming a value at each step
	* detect/detectSeries, for finding a value matching a filter
	* sortBy, for generating a sorted copy
	* some for testing whetheratleast one value matches the given criterion
	* ever for testing whether all values match the given criterion
* Error-handling in Async.js
	* Async.js uses Node-style callbacks:The first
callback argumentis an error.
* Chaining async functions  
	* async.series provides only the call-back to each task
	* async.waterfall provides the results from the previous task.  
    **
	var async= require('async');  
	var fs=require('fs');  
	var concatenation='';  
	var fileTasks=fs.readdirSync('recipes')  
	.filter(function(filename){  
	   return fs.statSync(filename).isFile();  
	})  
	.map(function(filename,i){  
	  return function(callback){  
	  fs.readFile(file-name,'utf8',callback);  
	};  
	});  
	async.series(fileTasks,func-tion(err,results){
	  if(err)throw err;  
	  console.log(concatenation=results.join(''));  
	});  
	**
	* If any of our readFile attempts did cause an error,the series would terminate and our completion handler would be called with the error immediately.
* Parallelizing async functions
	* async.parallel it takes an array of func-tions of the form function(callback) {...}, plus an(optional) completion handler. 
* All at once, or one at a time?
	* The task array is static. Once you’ve called async.series or async.parallel, you can’t add or remove tasks.
	* There’s also no way to ask, “How many tasks have been completed?”It’s   a black box,unless you dispatch updates from the tasks themselves.
	* You’re limited to either no concurrency or unlimited concurrency.
* Dynamic async queueing
>async.queue’s interface is a bit more complex than that of async.series and async.parallel. It takes a function called the worker(rather than an array offunc-tions) and a concurrency value(the maximum number of simultaneous tasks the work-ercanprocess).Thenit returns a queue that
we can push arbitrary task data onto(along
with an optional callback).  
	* example  

	function worker(data,callback){  
	console.log(data);  
	callback()  
	}  
	var concurrency=4;  
	var queue=async.queue(worker,concurrency);  
	queue.push(1);  
	queue.push(2);  
	queue.push(3);  
	* When it’s all done
		* drain
			>(Picture a tub full of tasks;when the last one has gone down the drain,the callack fires.)
		*	queue.drain=function(){
			(console.log('queue Completed in '+(new Date()-start)+'ms');
			};
	* use queue to search content  
	var async= require('async');
	var fs=require('fs');
	var concatenation='';
	var filenames=fs.readdirSync('recipes')
	.filter(function(filename){
	return fs.statSync(filename).isFile();
	});
	function worker(i,callback){
	fs.readFile(filenames[i],'utf8',function(err,result){
	if(err)throwerr;
	results[i]=result;
	callback();
	});
	}
	var concurrency=10;
	var queue=async.queue(worker,concurrency);
	var results=[];
	for(vari=0;i<file-names.length;i++){
	queue.push(i);
	}
	queue.drain=function(){
	console.log(concatenation=results.join(''));
	};
	* Advanced queue callbacks
		* When  the  last  task  has  started  running,  the  queue  calls  empty.  (When the task finishes, it calls drain.) 
		* When the concurrency limit is              reached, the queue calls saturated. 
	    * If  you provide a function as  the second  argument  in  a  push,  it’ll  be called  when  the  given  task (or  each task in the given array) is finished. 
	* Somting Qeuene can't do  
	    * example  
			1. Read a configuration file
			2. Connect to the database specified by
			the configuration file
			3. Connectto the cache specified by the
			configuration file
			4. Loadthe most used objects from the
			database into the cache
			5. Connect to an external API
			6. Start responding to HTTP requests
		* solution 
			* Promise whens and thens
			* async.auto
				* example   
					async.auto({
					  readConfigFile:readConfigFile,
					  connectToDB:
					  ['readConfigFile',connectToDB],
					  connectToCache:['readConfigFile',connectToCache],
					  cacheObjects:
					  ['connectToDB','connectToCache',cacheObjects],
					  connectToAPI: connectToAPI,
					acceptHTTPReqs:['connectToDB','connectToCache','connectToAPI',
					acceptHTTPReqs]
					});
* alternative
	* [TimCaswell’s Step](https://github.com/creationix/step)
	  >Stepis much lighter
		than Async.js,with an API consisting of a
		single function: Step. Step takes alist of
		functions, e.g.
		Step(task1, task2, task3);

	* [Futures](https://github.com/coolaj86/futures)
	* [nue](https://github.com/nakamura-to/nue)
	* [slide](https://github.com/isaacs/slide-flow-control)
## Multi-threading with Workers
	* Web Workers
		>WebWorkers are part of the living standard
		widely known as HTML5.To create one,you
		call the global Worker constructor with the 
		URL of a script:

			var worker = new Worker('kimptoc.js');
			worker.addEventListener('error',function(){
			console.log('Ch
			failed to load.');
			});   

        * Talking to Web Workers
        	* self.postMessage
        * Restrictions on Web Workers
        	* Performing physics calculations for
			games with CoffeePhysics.
			* Decoding video as it streams in with
			the Broad way implementation of the
			H.264 codec.
			* Encrypting communications with the
			Stanford JavaScript Crypto Library
			* updatethe page.
			* can’tseethe global window object
		* importScripts will(synchronously) load and run the given script(s)
		* browsers support WebWorkers
			* Chrome
			* FF
			* Safari

	* Node Workers with cluster
		* Typically,cluster is used to spin up one
		process per CPU core for optimal perform-ance(though whethe reach process will actu-allygetits own core is entirely upto the un-derlying OS):   

		var cluster = require('cluster');
		if(cluster.isMaster){
		// spin up workers
		var coreCount=require('os').cpus().length;
		for(vari=0;i<coreCount;i++){
		cluster.fork();
		}
		// bind death event
		cluster.on('death',func-tion(worker){
		console.log('Worker
		'+worker.pid+' has died');
		});
		}else{
		// die immediately
		process.exit();
		}   

		* child_process.fork  
		* Talking to Node Workers  

		var  cluster = require('cluster');
		if(cluster.isMaster){
		// spin up workers
		var coreCount=require('os').cpus().length;
		for(var i=0;i<coreCount;i++){
		var worker=cluster.fork();
		worker.send('Hello, Worker!',2);
		worker.on('message',function(message){
		if(message._queryId)return;
		console.log(message);
		});
		}
		}else{
		process.send('Hello, mainprocess!');
		process.on('message',function(message){
		console.log(message);
		});
		}
		* Restrictions on Node Workers
		>For the most part,cluster obeys the same
		rules as WebWorkers:There’s a master,and
		there are Workers;they communicate via
		events with attached strings orserializable objects. However, while Workers are obviously second-class citizens in the	browser, Node’s Workers possess all the rights and privileges of the master except  

			1. The ability to shut down the application,
			2. The ability to spawn more Workers,
			and
			3. The ability to communicate with each other.
		* [Clusterhub](https://github.com/fent/clusterhub)
		>Clusterhubprovides a hub object to each
		process that acts as an inter-process
		EventEmitter and EventVat store  

			* [EventVat](https://github.com/hij1nx/EventVat) 
			* 	// master
				varhub=require('clusterhub');
				hub.set('hitCount',0);
				// Workers
				varhub=require('clusterhub');
				functiononRequest(){
				hub.in-cr('hitCount',func-tion(hitCount){
				// ...
				});
				}
		* Temporary threads
			* single block of synchronous code to be run in a  separate  thread
			* create  and manage a pool of  Web Workers 

##Async Problems and Solutions  

### Problem: Where’d this go?
It’s  a  rite  of  passage  for  JavaScripters:  You pass  a  callback.  You  use  this in  that  callback,not  giving  the matter  much  thought. 
You spend  the  next  several  hours  trying  to figure out why your code is broken and cursing the day you were born. Or, at least, you join  one of the thousands who’ve  posted  examples like this to Stack Overflow:  
		
		// each button has data indicating an "action" to take 
		$('button').click (function (){ 
		$(this).after ('<a href="#" 
		id="confirm">Confirm</a>'); 
		$('#confirm').click (function (){ 

		var action = $(this).data ('action'); 
			 // why is action undefined???
		});
		});
jQuery bears some responsibility for the confusion.  Devs  who  are  unaware  of the call 
and apply methods that can warp it to anything  a function’s caller desires expect  this to scope like an ordinary variable,  albeit one mysteriously  set  in  their  event  handlers  via dark magicks. But the problem also arises in ordinary setTimeouts: 

		var account = { 
		balance :500, 
		showBalance:function (){console.log (this.balance); } 
		}; 
		account.showBalance()
		setTimeout(account.showBalance, 0);// undefined 

account.showBalance() calls  the  method  in  the account context,  but when account.showBalance is  passed  to a  function, the original context is lost. 

* Solution 1: Capture this 

	The  most  common  solution to  this  problem 
	is to simply store the value of this that you 
	want  in  a  separate variable.  Traditionally,  a 
	name like  selfis used, but I prefer a clearer 
	identifier: 

		$('button').click (function () { 
		  var button = this; 
		  $(button).after ('<a href="#" 
		id="confirm">Confirm</a>')
		$('#confirm').click (function (){ 
		 var action = $(button).data ('action'); 
		    // works! 
		   }); 
		 }); 

	This works for our  setTimeout example as 
	well: Just change showBalanceto 

		showBalance: function () { console.log (account.balance); } 
	     
* Solution 2:
Bind callbacks to this JavaScripters have long known that a function can be made impervious to the dark magicks of call and apply by wrapping them in another function.Utility libraries like Underscore.js include a bind method for just such a purpose: 

		 $('button').click (function () { 
		   $(this).after ('<a href="#" 
		 id="confirm">Confirm</a>'); 
		   $('#confirm').click (_ .bind (function (){ 

		 var action = $(this).data ('action'); 
		    // works! 
		   },this)); 
		 }); 
What we’ve done here is forced the inner callback to have the same value of this as the outer one. This is such a common pattern that CoffeeScript makes it as easy as declaring the bound function with  => instead of  ->.  In newer JS runtimes (cutting-edge browsers and Node),the bind method is part of the function prototype itself,  so we could write: 

		$('#confirm').click ((function () { 
		   ... 
		 }).bind (this)); 

Binding is widely used but slightly controversial.  It’s generally less efficient than capturing this, and has a tendency to complicate stack traces. See Patrick Mueller’s blog post “[bind() considered harmful.](http://pmuellr.blogspot.com/2010/06/bind-considered-harmful.html)”  Perhaps more importantly,  when some callbacks are bound  and  others  aren’t,  it  tends  to  exacerbate  confusion about what  this is at a particular line of code.  

* Solution 3: Work around this This solution only applies to jQuery, as most libraries are content to allow this in callbacks to take its default value, the root object (window in browsers, global in Node). 
jQuery is not, and some prominent JavaScript developers (including Jeremy Ashkenas, creator of CoffeeScript and Backbone.js) have called that a mistake. Although most jQuery coders use this to get the DOM element that an event was fired from, you can use the less ambiguous e.currentTarget instead (where e is the event object that jQuery passes as the first argument to the handler): 

		 $('button').click (function (e) { 
		 $(e.currentTarget).after ('<a 
		href="#" 
		id="confirm">Confirm</a>'); 
		  $('#confirm').click (function (){ 

		 var action = $(e.currentTarget).data ('action'); 
		    // works! 
		   }); 
		 }); 
Of course, e is just as prone to overuse as this, so consider using a distinguishing identifier like buttonEvent instead.

###Problem: Retrying async tasks
Often, we’ll want to attempt an async task n times before declaring failure, particularly when the task involves connecting to a remote server.  The basics of this problem are obvious:
1.  We need to store the number of failed attempts
2.  When an attempt fails, we need to increment the number of failed attempts and check whether it exceeds the maximum This translates into a lot of boilerplate code that we don’t want to repeat every time we run into this scenario, e.g. 

		var callback, failedAttempts = 0, asyncTask (callback = function (err) { 
		  if (err) { 
		     failedAttempts++; 

		 if (failedAttempts >=maxAttempts) { 
		       failCallback(err); 
		     }else { 
		      asyncTask (callback); 
		     }; 
		   }else { 

		 successCallback (Array.prototype .slice.call (arguments, 1)); 
		   }; 
		 }); 
	How can we make this simple? 

* Solution 1: 
Callback-style Assuming that we’re using Node-style callbacks, we can translate our boilerplate above into a reusable utility function:
		 function attemptAndRetry (asyncTask, maxAttempts, callback) { 
		  asyncTask (function (err) { 
		    if (err) { 
		      if (maxAttempts <= 1) { 
		        callback (err); 
		      }else { 

		 attemptAndTry (asyncTask,maxAttempts 1,callback); 
		      }; 
		    }else { 

		 callback (null,Array.prototype .slice.call (arguments 
		  }; 
		   }); 
		 } 

Notice that we’re using quasi-recursion here. 
There’s no risk of exceeding the stack limit, as long as asyncTask’s callback runs from the event queue. 

This is the most direct solution, but there are a couple of things I don’t like about it.  For one, calling the function is a tad awkward: 
		attemptAndRetry (function (callback) { 
		  fs.readFile ('locked.txt', 'utf8',callback); 
		 },3,function (err,result) { 
		  // ... 
		 }); 

Another problem is that we don’t get finegrained control over the process.         For instance, what if we want to log the error on each failed attempt? 

* Solution 2: 
PubSub-style As is so often the case in JavaScript, the best solution is an EventEmitter.  It allows us to expose an extremely versatile API from a simple function. To wit: 
		var EventEmitter = require ('events').EventEmitter; 
		function attempt (asyncTask) { 
		  var emitter = new EventEmitter (),results; 
		  emitter.failedAttempts= 0; 
		  function retry = function (){ 
		asyncTask (function (err) { 
		      if (err) { 
		        emitter.failedAttempts++; 

		 emitter.emit ('failure',err,retry); 
		      }else { 

		 results = Array.prototype .slice.call (arguments, 1); 

		 emitter.emit ('success',results); 
		      } 
		     }); 
		   } 
		  process .nextTick (retry); // ensure asynchronicity return emitter; 
		 } 

This may be a little more complicated than our earlier definition, but it makes things extremely easy for the caller: 
		var reading = attempt (function (callback) { 
		  fs.readFile ('locked.txt', 'utf8',callback); 
		 }); 
		reading.on ('failure',function (err,retry) { 
		  console.error (err); 
		  if (reading.failedAttempts< 3)retry (); 
		 }); 
		reading.on ('success',function (contents) { 

		 // ... 
		 }); 

Now we have cleanly exposed state and precise control over when we retry our function. 
Plus, because we can attach multiple handlers for the same event, it’s easy to view the process state from multiple perspectives. We could, for example, map the task onto a Promise for easy monitoring: 
		var deferred = new $.Deferred (); 
		reading.on ('failure',function (err,retry) { 
		  if (reading.failedAttempts >= 3)deferred.reject (err); 
		 }); 
		reading.on ('success',deferred.resolve) 
		
 ###Problem: Testing async code In languages like Java and Ruby, test cases can just be lists of assertions. Unfortunately, that won’t cut it when testing async JavaScript code, because the test runner has no way of knowing when the test case is complete. It’s important to use a JavaScript testing framework that can handle async test cases and produce meaningful output despite the unpredictable order of assertions. 

* Solution: Use Mocha The successor to Expresso, Mocha is a onesize-fits-all testing framework for both the browser and Node.  While async testing was something of a pain in older testing frameworks like QUnit (requiring async test cases to manually start and stop the runner), Mocha makes it  “just work.”  Here’s an example using Mocha’s T        -style interface (which I prefer for its simplicity over the default Bstyle interface): 
		var assert = require ('assert'); 
		suite('gamification',function (){ 
		  setup(gamify); 
		  test ('gamify resets points',function (){ 
		    assert.equal (points, 0); 
		   }); 
		  test ('player points are loaded on login',function (done) { 
		    assert.equal (points, 0); 

		 login (mock .username,mock .password,function (err,success) { 
		      if (err)throw err; 
		      assert.ok (success); 

		 assert.equal (points,mock .points); 
		   done (); 
		     }); 
		   }); 
		 }); 

Assume the assertions all pass.     When this code is run, via the command mocha  -u tdd test.js, the test runner will report the first test case’s success as soon as it returns, and will report the second test case’s success as soon as it calls done.         But wait—how did it know to wait for done to be called in the second case? 

The magic here is in a JavaScript feature called arity.  The arity of a function is the number of declared arguments it takes. That number is exposed as the lengthproperty: 
	(function () 
	 { ... }).length;  // 0 (function (done) { ... }).length; // 
1 Using this to distinguish synchronous functions from async ones is a clever hack, though it comes with a caveat: If you accidentally declare a synchronous test case with an argument, e.g. 

		test ('bedard', function (done) { 
		  assert.ok (true); 
		 }); 

you’ll get the error output x 1 of 1 tests failed: 1)  bedard: Error: timeout of 2000ms exceeded Usually, declaring functions without unnecessary arguments is good style.  In Mocha, it’s a matter of life or death. 

###Problem: 
Aggregating events With the rise of evented models, it’s become easy to oversaturate an application with events, slowing it to a crawl. For instance, in a game, we might represent each of thousands of characters in a level with a Backbone model and perform game logic in response to any changes in those models.  But when the player throws a grenade that blows up 100 monsters at once, we don’t want to perform the same logic 100 times.  How can we avoid this? 

* Solution 1: 
Silence the change events Backbone gives us a special flag that we can use to change models without them emitting a  'change'event: 
		monsters.each (function (damage) { 
		  this.set({life:this.get ('life')age}, {silent:true}); 
		 }); 

Used carefully, this is the most efficient solution to our problem. But it’ll only work if we have absolute knowledge about what’s supposed to happen when each model we’re silently modifying changes. 

Silencing events is like a scalpel. With steady hands and precise planning, it can be an invaluable tool;  otherwise, it’s just going to make a mess. 

* Solution 2: 
Use a simple timeout Remember that even a 0ms delay will ensure that a function runs after all synchronous code.  We can use that to our advantage by having a checkGameState function runs just once after all of the individual monster models have been updated: 
		var checkGameStateTimeout; 
		 function checkGameState (){ 
		   // if no monster allies remain, you lose var isTheGameLost= monsters .filter (function (){ 
		    return this.get ('ally'); 
		   }).length=== 0; 
		   // and so on... 
		} 
		var monsters = new Backbone.Collection; 
		monsters .on ('change',function (){ 
		  clearTimeout (checkGameStateTimeout); 
		  checkGameStateTimeout = setTimeout( 
		GameState, 0); 
		 }); 

Now no matter how many monsters are changed simultaneously, checkGameState will fire just once in response. 

* Solution 3: 
Throttling/Debouncing We’ve looked at one solution that operates from the part of the app that updates the models and another that works from the models’ end.  But the most elegant approach is usually to act on the particular function that we want to prevent from being called too often.  In general terms, that means we want to limit checkGameState to running once every n milliseconds. There are two basic approaches to this: 
 1.   Throttling, where additional calls to the function are ignored if its last run was less than n ms ago, and 
 2.    Debouncing, where any call to the function is delayed until n ms have elapsed since the last call Usually, you want throttling, because debouncing adds some latency to every function call.    Plus, debouncing means that a function will never be called if it’s called too often! (Sometimes debouncing is implemented with a maximum delay to avoid this.) 
However, debouncing is preferable in scenarios where you want to wait until an input source is idle, such as rendering a preview or running a validation only after the user has momentarily stopped typing. 

Both throttling and debouncing are easy to implement by creating a meta-function that returns a wrapped version of the original function.Here are two simple implementations: 
		function throttle (func, minDelay) { 
		  var lastHitDate = 0; 
		  return function (){ 
		    var now = new Date (); 

		 if (now lastHitDate >=minDelay) { 
		      lastHitDate = now; 

		 return func.apply (this,arguments); 
		    }; 
		}; 
		 } 

		function debounce (func, delay) { 
		  var timeout; 
		  return function (){ 
		    var args = arguments; 
		    clearTimeout (timeout); 
		    timeout = setTimeout(function (){ 
		      func.apply (this,args); 
		     },delay); 
		   }; 
		 } 

	(If you’re using Underscore.js—and you should!—you can just use _.throttle and _.debounce.) 

With these utility functions, we can either overwrite checkGameState entirely or just use the wrapped version from our change events.  For our purposes, debounce with a delay of 0 is most appropriate, since throttle with a short delay would allow checkGameState to run more within a block of synchronous code. debouncewith a delay of 0 is effectively equivalent to the setTimeoutwe used in solution 2: 
		var debouncedCheckGameState = debounce (checkGameState, 0); 
		monsters .on ('change',function (){ 
		  debouncedCheckGameState (); 
		 }); 

Note that it’s important to use the same throttled/debounced instance of a function from every source that we want to limit calls from. Calling (debounce (checkGameState, 0))(); 
would just be an overly complicated way of calling setTimeout(checkGameState, 0); 

because calling debounce(checkGameState, 0) again would just create a separate timeout. 

###Problem: 
Handling requests Quick question: 
You’re a web server. You receive a request. What do you do? 

Most likely, lots of unrelated things. At a minimum, you look at the requested route and map it to a file that you send in response. But you also probably log the request, check whether the requester is properly authenticated, and fetch some data. Maybe you cache the response in memory, too. Many of these pieces of functionality will be the same for some or all routes. How do you avoid duplication? 

* Solution: 
The middleware pattern In the Node.js Connect framework, each middleware function takes a request object (req), a response object (res), and a next callback. Every time a new request comes in, it’s passed to the first middleware. The middleware has three (good) options: 
 1. Handle the request itself by sending a response and returning without calling next. 
 2. Call nextwith no arguments. 
 3. Call nextwith an error argument. The next callback Connect generates either passes the request on to the next middleware on the stack, or to an error handler. And that’s basically all Connect does. This simple pattern (the core implementation is less than 200 lines) has proven so successful that just about every Node server framework has been built on top of Connect, most notably Express.js. Consider using the middleware pattern in other contexts where an action needs to be handled in several discrete steps. The only difference between a middleware stack and an ordinary callback stack (like we saw in Chapter 2) is that each function on the stack receives a next function that allows it to declare that it’s finished, making an async series of actions possible. Here’s a minimal implementation for comparison’s sake: 


		// callback stack function trigger (){ 
		  handlers.forEach(function (handler) { 
		    handler (); 
		   }); 
		 } 
		// middleware stack function trigger (){ 
		  invoke (0); 
		 } 
		function invoke (idx) { 
		  if (handlers.length<= idx) { 
		    return; 
		   }else { 
		    handlers [idx](function (err) { 
		      if (err)throw err; 

		 if (handlers.length > idx + 1)invoke (idx + 1); 
		   }; 
	 	} 

Perceptive readers may be reminded of async.series from Chapter 4. Connecting functions this way is an extremely common pattern. Feel free to reinvent the wheel, but be aware that the wheel is already available under the MIT License.   

### Problem:Ajax spaghetti  

Consider this an integrative exercise. How do we turn highly nested code like this into something clearer, better-organized, less repetitive, and easier to modify if we decide to change our application’s behavior? 

		$(function () { 
		  $('#menu').on ('click', 'a',function clickHandler (){ 
		    // we put a spinner on the selected menu item var $clickedLink= $(this).spin(); 
		    // we disable the menu until our Ajax call returns 
		    $('#menu').off ('click', 'li > 
		a'); 

		 $.get ('/content/'+ $clickedLink. success:function successHandler(response) { 
		        $clickedLink.unspin (); 
		        $('#menu').on ('click', 'li > 
		a',clickHandler); 
		        // we fade the content out... 

		 $('#content').fadeOut(function (){ 
		          // ...then we fade it in with the Ajax markup 

		 $('#content').html (response).fadeIn(); 
		        }); 
		      }, error:function (){ 
		        // we make one retry in case the error is just a fluke 
		$.get ('/content/' + $clickedLink.index (),{ 
		          success:successHandler, error:function (){ 
		            $clickedLink.unspin (); 
		            $('#menu').on ('click', 'li > 
		a',clickHandler); 
		           modal ('Error retrieving content from the server.'); 
		          } 
		        }); 
		      } 
		    }); 
		    return false; // prevent default click behavior 
		  }); 
		}); 

* Solution: 
Distribute, distribute, distribute The big problem with the code above is that it’s way too centralized. Everything that happens in response to a menu click goes in the click handler, and everything that happens in response to the Ajax request goes in the Ajax response handler. We need to spread things out in a way that makes sense. There are plenty of ways we could do that here—using custom jQuery events, for example—but the approach I’d recommend is using an evented model to represent the menu. The model will encapsulate the state that we need to capture from the various async events, and the abstraction will make it easy for us to change our event flow in the future. Let’s start by asking, “What state do we need to store?” I can think of three things:   
1. Whether our menu is in its  “ready,” 
clickable state, or in the     “unready”  
state with a spinner over the active link.   
2. Which link is currently active (in the process of having associated content loaded), if any.  
3. How many attempts we’ve made so far to request content from the server for the active link. The first two sound like they should be associated with the menu. So that gives us our menu model: 
		window.menu = new Backbone.Model ({ 
		  ready:false, activeLinkIndex:-1 
		 }); 
Why does our menu start out with ready: 
false? Because the menu element may not exist yet in the document. We want readyto correspond to the click handler being attached. We also want to ensure that there is no active link when the menu becomes ready: 

		menu .on ('change:ready', function (ready) { 
		if (ready) { 
		$('#menu').on ('click', 'li > 
		a',menu .clickHandler); 
		menu .set('activeLinkIndex',-1); 
		}else { 
		$('#menu').off ('click', 'li > 
		a'); 
		} 
		}); 

Notice the reference to menu.clickHandler. For convenient namespacing, we’ll go ahead and attach all of our menu-related event handlers to the menu model:  
		menu .clickHandler = function () { 
		  menu .set({ready:false,activeLinkIndex:$(this).index ()}); 
		  return false; 
		 } 

Declaring the menu ready is the only thing that has to wait for the DOM. So our jQuery callback, which originally wrapped around everything, is just a one-liner: 


		$(function () { menu .set({ready: 
		true}); 
(Note that it’s important for this line to come after menu.clickHandler is defined, because the callback will run synchronously if the document is already ready.) 

The click handler modifies the menu’s activeLinkIndex, so let’s respond to that by fetching the appropriate content and adding the Ajax spinner to the correct menu item: 
		menu .on ('change:activeLinkIndex', function (activeLinkIndex) { 
		  $('#menu').children ().unspin (); 
		     .eq (activeLinkIndex).spin(); 
		  menu .fetchSelectedContent(); 
		 }); 

If activeLinkIndex is changed to  -1 (indicating that no link is active), the eq(activeLinkIndex) selector will match no elements, so no Ajax spinner will appear. And the first line of our fetchSelectedContentwill simply return: 
		menu .fetchSelectedContent = function () { 
		  if (menu .get ('activeLinkIndex')=== -1)return; 
		  $.ajax ({ 

		 url: 
		'/content/'+menu .get ('activeLinkIndex'), tryCount:0 
		   }) 
		   .done ($('#content').fadeHtml) 
		   .done (function (){menu .set({ready:true}); }) 
		   .failure(function (){ 
		    this.tryCount++; 
		    if (this.tryCount >= 2) { 
		      modal ('Error retrieving content from the server.'); 
		}else { 
		      $.ajax (this); 
		     }; 
		   });); 
		 } 

This is our most complex function, but every line serves a clear purpose. When the Ajax call succeeds:   
1. The content element’s fadeHtml method is called with the data retrieved from the server. fadeHtml is a simple plugin that we’ll define shortly.  
2. The menu is restored to its ready state. Recall that this will also reset activeLinkIndexto 1. When the Ajax call fails:  
1. this in a jQuery Ajax callback points to the jqXHR object that, among other things, stores all of the settings passed in to  $.ajax. We use that to store our tryCount.  
2. Because this also contains our url property, we can simply write $.ajax(this) to reattempt the Ajax call. This technique was popularized by a blog post entitled  “Defensive Ajax and Ajax retries in jQuery.” 

If we felt like the fetchSelectedContent method was too large, we could move the failure handler out of it. That’s the great thing about evented code: 
As long as state is stored rationally, you have unlimited flexibility. Now the only gap left to fill in is to define fadeHtml: 

		 $.fn.fadeHtml = function (markup) { 
		  return this.fadeOut(function (){ 
		    $(this).html (markup).fadeIn(); 
		   }); 
		 }; 

And that’s it! We have a little more code than we started with, but it’s many times easier to work with. 