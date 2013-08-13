---
layout: post
title: "Troopjs Architecture Analysis"
date: 2013-08-12 17:17
comments: true
categories:  [Decouple TroopJS]
---

### Troopjs framework is seperated by 6 different parts: 

* [troopjs-core][1] is the basic frastrate modules
	- component
		* **factory** is the most basic 
		widget factory based on poly.js
		* **base** is the most common widget which used to manage life-cycle and task.
		* **gadget** is a observable **base** widget
		* **service** is most common service lift-cycle mamager 
	- event
		* **emitter** is a NodeJS style event manager, the event will be excuted by sequence
	- logger
		* **console** is a system **Console** wrapper
		* **pubsub** is a hub wapper for logger topic
		* **service** is a log service which used to wrapper message and output data to different **appender**
	- pubsub
		* **hub** is a wrapper of event emitter
		* proxy
			- **to1x** is troop 1.0 hub proxy
			- **to2x** is troop 2.0 hub proxy
	- resistry
		* **service** is a action service manager
* * * 
* [troopjs-jquery][2]
	- **destory** is a jquery plugin used to handle dom destroy special event
	- **dimensions** is a jquery plugin used to handle widget resize when parent resized
	- **hashchange** is a jquery plugin used to handle has changed event and support mutiple browsers
	- **loom** is weave function wrapper and weave css expression extender
	- **noconflict** is jQuery noconflict wrapper
	- **resize** is a window resize event wrapper
* * * 
* [troopjs-requirejs][3]
	- **multiversion** is used to support require multiple context widget
	- **shadow** is used to load different version of widget at the same time
	- **template** is troop template engine which use function context
* * * 
* [troopjs-utils][4]
	- **getargs**  ?????(todo: find use case)
	- **merge** is a deep merge util method
	- **unique** is used to filter duplicted data and consist comparator 
* * * 
* [troopjs-browser][5]
	- ajax
		* **service** is a ajax request handler
	- application 
		* **widget** is a base browser application life-cycle manager
	- component 
		* **widget** is a base dom component widget life-cycle manager
	- dimensions
		* **widget** is a dom demensions life-cycle manager
	- loom
		* **config** is used to config indicator for weave related stuff
		* **unweave** is unweave promise manager 
		* **weave** is weave promise manager
		* **woven** is woven promise manager
	- route
	 	* **uri** is base url factory widget
	 	* **widget** is listened to "hashchange" event, and manage route life-cycle
	- store
		* **base** is a common storage wrapper
		* **local** is a localStorage wrapper
		* **session** is a seesionStorage wrapper
* * * 
* [troopjs-data][6]
	- cache 
		* **component** is cache life-cycle manager
	- component
		* **widget** is a common query widget wrapper
	- query
		* **component** is a cache manager to support mutiple cache process stuff
		* **service** is a batch query service life-cycle manager
	- store 
		* **component** is storage util wrapper used to process different storage stuff
 
[1]: https://github.com/troopjs/troopjs-core.git
[2]: https://github.com/troopjs/troopjs-jquery.git
[3]: https://github.com/troopjs/troopjs-requirejs.git
[4]: https://github.com/troopjs/troopjs-utils.git
[5]: https://github.com/troopjs/troopjs-browser.git
[6]: https://github.com/troopjs/troopjs-data.git