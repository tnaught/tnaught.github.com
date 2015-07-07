---
layout: post
title: "Javascript如何实现传统的继承？"
---

原文链接:[http://www.crockford.com/javascript/inheritance.html](http://www.crockford.com/javascript/inheritance.html)

    Function.prototype.method = function (name, func) {
        this.prototype[name] = func;
        return this;
    };

---------------------------------------------------
    Function.method('inherits', function (parent) {
        this.prototype = new parent();
        var d = {}, //不太清楚d的存在意义
        p = this.prototype;
        this.prototype.constructor = parent;//为什么要重新关联构造方法呢? 
        this.method('uber', function uber(name) {
            if (!(name in d)) {
                d[name] = 0;
            }        
            var f, r, t = d[name], v = parent.prototype;
            if (t) {
                while (t) {
                    v = v.constructor.prototype;
                    t -= 1;
                }
                f = v[name];
            } else {
                f = p[name];
                if (f == this[name]) {
                    f = v[name];
                }
            }
            d[name] += 1;
            r = f.apply(this, Array.prototype.slice.apply(arguments, [1]));//执行uber方法
            d[name] -= 1;
            return r;//获取uber方法的返回值
        });//在子类方法与父类方法同名时，uber可以优先获取父类方法
        return this;
    });//@param parent是要继承的父类构造方法

调用示例:

    var Parent = function(value){
        this.value_ = value;
        this.getValue = function(){
            return this.value_;
        };
        this.setValue = function(v){
            this.value_ = v;
            return this;//方法没有返回值的时候默认返回this，方便级联操作
        };
    };
    Parent.method('getDescription',function(){
        return 'this is description from:'+this._value;
    });
    var Child = function(v){
        this.value_ = v;//参数需要重新赋值
    };
    Child.inherits(Parent);//在constructor的声明后，Child的method方法之前
    Child.method('elseOperate',function(){
        console.log('this is elseOperate');
        return this;
    });
    Child.method('getDescription',function(){
        var s = this.uber('getDescription');
        return s+'!!!!!!';
    });
    var parent = new Parent('p');
    var child = new Child('c');
    console.log(parent.getDescription());//result:'this is description from:p';
    console.log(child.getDescription());//result:'this is description from:c!!!!!!';

---------------------------------------------------
    Function.method('swiss', function (parent) {
        for (var i = 1; i < arguments.length; i += 1) {
            var name = arguments[i];
            this.prototype[name] = parent.prototype[name];//只是继承父类的特定属性
        }
        return this;
    });