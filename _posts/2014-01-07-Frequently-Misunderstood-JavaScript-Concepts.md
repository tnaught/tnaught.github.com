---
layout: post
title: "那些容易被误解的js概念"
description: "javascript basic concept"
category: "translate"
tags: [javascript,concept]
group: post
---
<!-- {% include JB/setup %} -->

原文链接:[http://bolinfest.com/javascript/misunderstood.html?utm_campaign=Manong_Weekly_Issue_9&utm_medium=EDM&utm_source=Manong_Weekly](http://bolinfest.com/javascript/misunderstood.html?utm_campaign=Manong_Weekly_Issue_9amp;utm_medium=EDMamp;utm_source=Manong_Weekly)

###JavaScript对象
JavaScript对象都可以看做是关联数组，类似Java中的Map，不同的是后者的键值可以为任何类型，而JavaScript的键值只能是String，如果不是String类型也不会报错，JavaScript会自动转换为String类型。例如：

	var foo = new Object();
	var bar = new Object();
	var map = new Object();
	map[foo] = 'foo';
	map[bar] = 'bar';
	alert(map[foo]);

结果为`bar`，而不是`foo`。`foo`和`bar`的类型是`Object`，不能作为key值，实际做为key的是它们对应的toString()值，即`[object Object]`,也就是说

	map['[object Object']] = 'foo';
	map['[object Object']] = 'bar';
	alert(map['[object Object]']);

这样重写后结果显而易见了。

###多种Object取值的方法
有三种不同的方法在Object中取值：

1.	直接通过名字查找：

	>map.meaning_of_life;
2.  传关键字的方法：

	>map\["meaning_of_life"\];
3.  使用对象传值(*这种方法有存在的价值？*)
	
		var machine = new Object();
		machine.toString = function(){
		    return 'meaning_of_name'
		};
		map[machine]

第一种方法中对象的名称必须是一个有效的标示符，否则就会报错。所以说使用第二种方法会安全一些，不过在`Closure`中使用第二种方法会影响编译器对代码压缩。

###单引号和双引号是一样的
值得注意的是传入JSON.parse的参数必须是双引号的字符串,key值也要带双引号

	JSON.parse('{"a":1}')//right
	JSON.parse("{\"a\":1}")//right
	JSON.parse('{a:1}')//wrong
	JSON.parse("{'a':1}")//wrong

###Object的声明

1.    var obj = new Object();
2.    var obj = new Object;
3.    var obj = {};

需要提一下的是第三种形式，这种形式可以直接为对象增加属性

	var obj4 = {
	  one: 'uno',//#可以不用加引号#
	  'in':'in',//#必须加引号,因为in是关键字#
	  'two': 'dos',//#这里不要忘记','，否则会报错#
	  'three': 'tres',//#在IE中会报错，ff、chrome、safari中不会#
	};

###原型链

在javascript中所有的方法都有一个prototype属性，其值为一个对象，该属性对象的constructor为方法本身。

	var a = {b:1};
	console.log(a.constructor);//function Object() { [native code] } 
	console.log(a.prototype);//undefined
	var b = function(){
		var cc = 1;
	};
	console.log(b.constructor);//function Function() { [native code] } 
	console.log(b.prototype);//Object {} 
	console.log(b.prototype.constructor);//function (){var cc = 1} 

下面是一个原型链的示例图：
![例子](/assets/images/appendix_b_box_and_arrow.png)

上图很形象的说明了原型链中的几个容易混淆的概念:`prototype`、`_proto_`、`constructor`。图中Rectangle是构造函数，rect是Rectangle的实例，width和height是Rectangle对象(*function也是object*)的prototype属性object上的两个键值。从rect节点开始沿着红色箭头直到根节点就是一条原型链。rect上关联的属性对象就是原型链经过的节点所具有的属性对象的集合。例如rect.width和rect.height和rect.hasOwnProperty都可以直接取到相应的值和方法。不过值得注意的是，delete方法只能删除自有属性对象中的值，对原型链上的属性对象不起作用。

Object对象的prototype属性对象上有一个原生方法hasOwnProperty，在javascript中每一个object都有这个方法，它可用来判断object是否具有某个property，原型对象上的property不包含在内。

最后需要补充的是，_propo_属性不能被显性的赋值，它是在javascript运行时生成的关系。

###函数的定义方法

>1.    函数声明:function f(){}
>2.    函数表达式:var f = function(){}

从代码的书写上看起来声明的方式更加简洁，不过在javascript编译后会对变量声明进行提升，称为#Hoisting#，例如:

	function hereOrThere() {
		alert(a);
	  	return 'here';
	}
	var a;
	alert(hereOrThere()); 
	a = 2;
	function hereOrThere() {
	  alert(a);
	  return 'there';
	}

上述代码在编译的时候会被转化为：

	var a;//值为undefined;
	function hereOrThere() {
	  return 'here';
	}

	function hereOrThere() {//声明被提升
	  return 'there';
	}
	alert(hereOrThere()); 
	a = 2;
	
结果依次弹出undefined和2,代码本身的预期应该是返回`here`，使用方法表达式就不存在这样的问题，所以在Google Style Guide中推荐使用第二种方法来定义方法。

###函数中的this是指什么?
函数中的this是指函数的执行对象，这个执行对象可以由函数执行的上下文环境决定，也可以使用`call`方法和`apply`进行对象的关联。

	var value = 10;
	
	var f = function(){
		console.log(this.value)
	};

	f();//这里this是指window，返回值为10;
	
	var obj = {
		value:20
	};
	
	f.apply(obj,[arguments]);//this指对象obj,返回值为20
	
	f.call(obj,argument1,argument2,..)//this指对象obj,返回值为20
	
	obj.f = f;
	obj.f();//this指对象obj,返回值为20

`call`和`apply`都可以重新改变函数的this对象，不同在于`call`需要用单独的参数为函数参数赋值，`apply`是用一个参数数组为函数参数赋值。

###你可能不知道的var关键字
不要小瞧var这个关键字哦，不小心忽略它的话会带来一些意外的bug。不带var的变量申明会被当做是全局变量。例如：

	var f = function() {
	  for (i = 0; i < 3; i++) {
	    g(i);
	  } 
	};

	var g = function() {
	  for (i = 0; i < 3; i++) {
	    alert(i);
	  }
	}

	f();

上述执行结果依次弹出1,2,3，而不是执行3次g方法。

###JavaScript中不存在块状作用域
`if`、`while`、`for`语句中的变量在整个函数区域都可以获取到，只要是在方法中申明的变量都会被提升到方法最前面.还是用例子说话:

	var i = 10;
	var strangeLoop = function(someArray) {
		alert(i);//尽管有全局变量i，不过因为`hoisting`，局部变量i会在方法的最前面声明，结果为undefined

		for (i = 0; i < someArray.length; i++) {
			alert('Element ' + i + ' is: ' + someArray[i]);//i依次取值为0,1
		}

		var i = 42;
	};
	strangeLoop([10,20]);


