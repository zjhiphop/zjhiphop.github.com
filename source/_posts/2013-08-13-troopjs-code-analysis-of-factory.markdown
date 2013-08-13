---
layout: post
title: "Troopjs code analysis of Factory"
date: 2013-08-13 13:26
comments: true
categories: [Decouple TroopJS]
---

  > The factory method pattern is an object-oriented creational design pattern to implement the concept of factories and deals with the problem of creating objects (products) without specifying the exact class of object that will be created.  

  
### The [factory][1] of troopjs actually is a basic widget **composer**, it is a **decorator** at the same time. It's based on ECMA Script 5 **Object** which enable you todo following staff:
* **[Object.Create][3]** Creates a new object with the specified prototype object and properties.
* **[Object.defineProperty][4]** Defines a new property directly on an object, or modifies an existing property on an object, and returns the object.
	- configurable
	- enumerable
	- value
	- writable
	- get
	- set
* **[Object.defineProperties][5]** Defines new or modifies existing properties directly on an object, returning the object.
* **[propertyIsEnumerable][6]**
* **[Object.keys][7]** Returns an array of a given object's own enumerable properties, in the same order as that provided by a for-in loop (the difference being that a for-in loop enumerates properties in the prototype chain as well).
* **[Object.getOwnPropertyNames][8]** Returns an array of all properties (enumerable or not) found directly upon a given object.
* **[Object.getOwnPropertyDescriptor][9]**
* **[Object.freeze][10]** Freezes an object: that is, prevents new properties from being added to it; prevents existing properties from being removed; and prevents existing properties, or their enumerability, configurability, or writability, from being changed. In essence the object is made effectively immutable. The method returns the object being frozen.
* **[Object.getPrototypeOf][11]** Returns the prototype (i.e. the internal [[Prototype]]) of the specified object.
* **[Object.preventExtensions][12]** Prevents new properties from ever being added to an object (i.e. prevents future extensions to the object).

* But how to does Object-base Inheritance works? See [example][2]. 

### Decouple Factory
* Decorate
	- **Before** is excuted before origin function
	- **After** is excuted after origin function
	- **Around** is mixed with origin function
* Inheritance
	- **create** is used to create a component
	- **extend** is used to extend a component
	- **constructors** are from function arguments passed to factory
* Detail explain
	- **special** is used to handle event like: "dom/[data-action='test']/click.dbclick" from property
	```
	/^(\w+)(?::(.+?))?\/([-_./\d\w\s]+)$/
	```
	- factory descriptors 
		* create
		* before
		* after
		* around

	- constructor descriptors
		* protype
		* constructors
			- default is 
				* "enumerable" : true,
				* "configurable" : true,
				* "writable" : true
		* specials
		* tostring
		* extend
		* create

### It's a good pattern to create Mix-in component. Use case:

<pre><code>
	// component/base.js
	return Factory(
	/**
	 * Creates a new component
	 * @constructor
	 */
	function Component() {
		var me = this;

		// Update instance count
		me[INSTANCE_COUNT] = ++INSTANCE_COUNTER;

		// Set configuration
		me[CONFIGURATION] = {};
	}, {
		"instanceCount" : INSTANCE_COUNTER,

		"displayName" : "core/component/base",

		/**
		 * Configures component
		 * @returns {Object} Updated configuration
		 */
		"configure" : function configure() {
			return merge.apply(this[CONFIGURATION], arguments);
		},

		/**
		 * Signals the component
		 * @param _signal {String} Signal
		 * @return {*}
		 */
		"signal" : function onSignal(_signal) {
			...
		},

		/**
		 * Start the component
		 * @return {*}
		 */
		"start" : function start() {
			...
		},

		/**
		 * Stops the component
		 * @return {*}
		 */
		"stop" : function stop() {
			...
		},

		/**
		 * Creates new task
		 * @param {Function} resolver
		 * @param {String} [name]
		 * @returns {Promise}
		 */
		"task" : function task(resolver, name) {
			...
		},

		/**
		 * Generates string representation of this object
		 * @returns {string} displayName and instanceCount
		 */
		"toString" : function _toString() {
			...
		}
	});
</code></pre>

[1]: https://github.com/troopjs/troopjs-core/blob/develop/component/factory.js
[2]: http://uxebu.com/blog/2011/02/23/object-based-inheritance-for-ecmascript-5/
[3]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FGlobal_Objects%2FObject%2Fcreate
[4]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FGlobal_Objects%2FObject%2FdefineProperty
[5]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FGlobal_Objects%2FObject%2FdefineProperties 
[6]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FGlobal_Objects%2FObject%2FpropertyIsEnumerable
[7]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FGlobal_Objects%2FObject%2Fkeys
[8]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames
[9]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor
[10]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze
[11]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf
[12]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions