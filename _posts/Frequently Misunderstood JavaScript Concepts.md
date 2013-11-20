原文链接：http://bolinfest.com/javascript/misunderstood.html?utm_campaign=Manong_Weekly_Issue_9&utm_medium=EDM&utm_source=Manong_Weekly

JavaScript对象是键值始终为String的关联数组，类似Java中的Map，不同的是后者的键值可以为任何类型，而JavaScript的键值只能是String，如果不是String类型也不会报错，JavaScript会自动转换为String类型。
Example：
var foo = new Object();
var bar = new Object();
var map = new Object();
map[foo] = 'foo';
map[bar] = 'bar';
alert(map[foo]);
结果为'bar'，而不是'foo';
foo和bar的类型是Object，不能作为map的key值，实际做为key的是两者的toString()值，即'[object Object]'。也就是说
map['[object Object'] = 'foo';
map['[object Object'] = 'bar';
alert(map['[object Object]']);
所以结果是'bar';

有三种不同的方法在Object中取值
1.map.meaning_of_name
2.map['meaning_of_name']
3.
var machine = new Object();
machine.toString = function(){return 'meaning_of_name'};
map[machine]

单引号和双引号的字符串是一样的
值得注意的是传入JSON.parse的参数必须是双引号的字符串,key值也要带双引号
JSON.parse('{"a":1}')//right
JSON.parse("{\"a\":1}")//right
JSON.parse('{a:1}')//wrong
JSON.parse("{'a':1}")//wrong

JavaScript中有多种定义Object的方法
1.var obj = new Object();
2.var obj = new Object;
3.var obj = {};

"prototype"属性不是Prototype

函数的两种定义方法
1.function f(){}
2.var f = function(){}

this的指向
var f = function(){console.log(this.value)};
f();//this referto window
var o = {};
f.apply(o,[arguments]);
f.call(o,argument1,argument2,..)//this referto o;
o.f = f;

JavaScript中不存在块状作用域

