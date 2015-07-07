---
layout: post
title: "Javascript的继承模式"
---

原文链接:[http://www.bolinfest.com/javascript/inheritance.php](http://www.bolinfest.com/javascript/inheritance.php)

*译注：写javascript也有一段时间了，对于面向对象、设计模式这类词还是有一些模糊，[Michael Bolin](https://profiles.google.com/u/0/107176920289865686893)对此做了浅显易懂的总结，遂进行了简单的翻译和整理。不过这篇文章的内容是基于Closure Library和JavaScript:The Good Parts写的，比较部分针对性较强。*

在JavaScript中主要有两种继承模式：伪类(pseudoclassical)模式和方法(functional)继承模式。由[Closure Compiler](http://code.google.com/closure/compiler/)编译的[Closure Library](http://code.google.com/closure/library/)中使用了伪类模式，Douglas Crockford编写的《JavaScript:The Good Parts》一书中使用了方法模式。

Crockford这样评价伪类模式：“伪类模式中没有私有的概念，所有的属性都是共有的。也没有方法去构造`super`方法。。。更糟糕的是，构造方法的使用有很大的风险。一旦开发人员忘记在构造函数加上`new`，那`this`就会指向为空对象。。。而且既没有编译警告，也不会有运行时的警告。”

本文探讨了伪类模式相对于方法模式的优势，本人认为Closure Library中使用的模式消除了伪类模式的风险，同样方法模式也有自身的缺陷。首先我们来看看方法模式。

###Functional pattern的示例

下面的例子来源于*JavaScript: The Good Parts*一书。下面定义了一个`phone`和一个`smartPhone`:

	var Phone = function(spec){
		var that = {};
		that.getPhoneNumber = function(){
			return spec.phoneNumber;
		}
		that.getDescription = function(){
			return "This is a phone that can make calls.";
		}
		return that;
	};

	var smartPhone = function(spec){
		var that = Phone(spec);
		spec.signature = spec.signature || 'sent from:'+that.getPhoneNumber();
		
		that.sendEmail = function(emailAddress,message){
			// Assume sendMessage() is globally available.
			sendMessage(emailAddress,message + "\n" + spec.signature);
		}
		var super_getDescription = that.superior("getDescription");
		
		that.getDescription = function(){
			return super_getDescription() + " It can also send email messages.";
		}

		return that;
	};

根据以上的类型可以这样来构造实例：

	var myPhone = phone({"phoneNumber": "8675309"});
	var mySmartPhone = smartPhone({"phoneNumber": "5555555", "signature": "Adios"});
	mySmartPhone.sendEmail("noone@example.com", "I can send email from my phone!"); 

###Pseudoclassical pattern的示例
上面的例子使用伪类模式可以这样来写（代码使用了Closure的风格）：

	goog.provide('Phone');
	goog.provide('SmartPhone');

	/**
	 * @param {string} phoneNumber
	 * @constructor
	 */
	Phone = function(phoneNumber) {

	  /**
	   * @type {string}
	   * @private
	   */
	  this.phoneNumber_ = phoneNumber;
	};

	/** @return {string} */
	Phone.prototype.getPhoneNumber = function() {
	  return this.phoneNumber_;
	};

	/** @return {string} */
	Phone.prototype.getDescription = function() {
	  return 'This is a phone that can make calls.';
	};


	/**
	 * @param {string} phoneNumber
	 * @param {string=} signature
	 * @constructor
	 * @extends {Phone}
	 */
	SmartPhone = function(phoneNumber, signature) {
	  Phone.call(this, phoneNumber);

	  /**
	   * @type {string}
	   * @private
	   */
	  this.signature_ = signature || 'sent from ' + this.getPhoneNumber();
	};
	goog.inherits(SmartPhone, Phone);//这里的逻辑？

	/**
	 * @param {string} emailAddress
	 * @param {string} message
	 */
	SmartPhone.prototype.sendEmail = function(emailAddress, message) {
	  // Assume sendMessage() is globally available.
	  sendMessage(emailAddress, message + '\n' + this.signature_);
	};

	/** @override */
	SmartPhone.prototype.getDescription = function() {
	  return SmartPhone.superClass_.getDescription.call(this) +
	      ' It can also send email messages.';
	};

构造实例如下：

	goog.require('Phone');
	goog.require('SmartPhone');

	var phone = new Phone('8675309');
	var smartPhone = new SmartPhone('5555555', 'Adios'};
	smartPhone.sendEmail('noone@example.com', 'I can send email from my phone!'); 

###Functional pattern的缺陷

####类型实例化消耗内存
每执行`phone()` 一次, `getPhoneNumber`和`getDescription`方法就会创建一次，这两个方法都形成了一个闭包，闭包会保存父函数的所有变量，两者都会始终存在于变量中，不会在调用结束后，被垃圾回收机制回收，对内存的消耗很大，可能会导致内存溢出。

对于伪类模式就不存在这个问题了，因为`Phone()`使用了基于原型的继承，原型链方法只需要定义一次就可以在实例中获取。

####方法不能内联
有些时候，编译器会使函数内联，尤其是对一些简单的`get`方法，这样可以提高运行时性能。在方法模式中，函数中的变量没有被明确的指向，所以编译器没有办法做方法的重写。不同的是，伪类模式中可以重写，例如：

	// phone1 and phone2 are of type Phone
	var caller = phone1.getPhoneNumber();
	var receiver = phone2.getPhoneNumber();
	operator.createConnection(caller, receiver);
	
上述代码编译器可以重写为：
	
	operator.createConnection(caller.phoneNumber_, receiver.phoneNumber_);

####超类方法不能被压缩
这个和代码压缩有关，在`Closure Compiler`中，没有被引号引用的属性可以通过重命名进行压缩。在方法模式中`getDescription`方法在子类中作为superior的参数被调用了，在编译的过程中就不能被压缩。(*不太明白普通的编译器的压缩机制是否也这样，关于压缩这块还没有太理解作者意思*)

####不能使用`instanceof`方法进行类型判断
因为不存在构造方法，所以无法判断一个`object`是`phone`还是`smartPhone`的实例.当然也可以通过特定的方法属性来区分两种类型。例如：

	if (arg.sendEmail) {
 	 // implies arg is a mobilePhone, do mobilePhone things
	}

这种方法的区分表意能力不强，而且也会有其他类型满足这一条件。

####鼓励为`Function.prototype`和`Object.prototype`增加`property`
在The Good Parts一书中，Crockford为`Object.prototype`增加了`superior`的方法，这样的方式对于开发者来说不太友好，例如：

	// Create a new object literal.
	var obj = { "one": 1, "two": 2 };

	// Enumerate the properties of obj.
	for (var property in obj) alert(property);

开发人员预期的结果只有两个property，实际上还会包括其他的原型方法。

####不能更新一种类型的所有实例
这是显而易见的

####命名的问题
构造方法的第一位约定俗称为大写字母，而变量一般为小写字符，在伪类模式中显得很清晰。这算是作者的个人喜好。

####书写上带来了一次不必要的缩进
作者的个人喜好。

###对伪类模式的反对之声和反驳

####容易忘记`new`操作符?
只要在书写代码的时候对构造方法加上@constructor，那`Closure Compiler`就会自动去调用该方法时是否使用了`new`，如果没有则会报错，相反也是。

####没有super方法？
`goog.inherits(SmartPhone, Phone)`会将Phone作为superClass_赋给SmartPhone,这样就可以使用父类的方法了。

####没有`public`属性？

####每个方法前面都加上SomeClass.prototype是一种浪费?
`The Compiler`会为SomeClass.prototype创建一个临时变量进行重用.而且还可以将SomeClass.prototype写成一个对象如：

	SomeClass.prototype = {
	  getFoo: function() {
	    return this.foo_;
	  },

	  setFoo: function(foo) {
	    this.foo_ = foo;
	  },

	  toString: function() {
	    return '<foo:' + this.getFoo() + '>';
	  }
	};

不过这样写增加了缩进，而且不方便调整方法之间的顺序，因为最后一个方法不用逗号结尾，所以还是建议最上面示例中的书写方法。
