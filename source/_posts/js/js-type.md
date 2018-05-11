---
title: js判断数据类型的方法
author: fish
comments: true
tags: 
- javascript
- 前端
categories: 
- 前端
---

在项目中，我们经常需要判断数据的类型，根据数据类型对数据做不同操作，这里总结一下js中判断数据类型的方法。

# 常用的检测对象类型的方法

## typeof

typeof 是一个操作符，其右侧跟一个一元表达式，并返回这个表达式的数据类型。返回的结果用该类型的字符串(全小写字母)形式表示，包括以下 7 种：number、boolean、symbol、string、object、undefined、function 等。

```js
typeof ''  // 'string' 字符串类型  有效
typeof 1;  // 'number' 数字类型  有效
typeof Symbol(); // 'symbol' symbol类型 有效
typeof true; // 'boolean' 布尔类型 有效
typeof undefined; //'undefined' 未定义 有效
typeof new Function(); // 'function' 方法 有效

typeof null; //object 无效
typeof [] ; //object 无效
typeof new Date(); //object 无效
typeof new RegExp(); //object 无效
```

typeof 可以正确判断string,number,symbol,boolean,undefind,function类型，但对于null,array,Date,RegExp均只能返回object字符串，无法对其类型做出正确判断。

## instanceof

instanceof 是用来判断A是否为B的实例，如 A instanceof B，如果A是B的实例，则返回true，否则返回false。instanceof检测的是原型。

```js
[] instanceof Array // true
{} instanceof Object // true

[] instanceof Object; // true
new Date() instanceof Object;// true
new Function() instanceof Object // true

```
可以发现，上面代码返回都是true，*instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。*

## Array.isArray()

Array.isArray()是ES5提供的确认某个对象本身是否为Array类型

```js
Array.isArray([]) //true
```

## constructor

可以通过检测原型对象的constructor属性判断数据类型

```js
''.constructor == String  // true 
true.constructor == Boolean // true
[].constructor == Array // true
document.constructor == HTMLDocument // true
window.constructor == Window // true

new Number(1).constructor == Number // true
new Function().constructor == Function // true
new Date().constructor == Date // true
new Error().constructor == Error // true
```

部分类型数据本身带有constructor属性，而部分类型需要实例化后才能进行比较。

```
1. null 和 undefined 是无效的对象，因此是不会有 constructor 存在的，这两种类型的数据需要通过其他方式来判断。

2. 函数的 constructor 是不稳定的，这个主要体现在自定义对象上，当开发者重写 prototype 后，原有的 constructor 引用会丢失，constructor 会默认为 Object

```

## toString

toString() 是 Object 的原型方法，调用该方法，默认返回当前对象的 [[Class]] 。这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。

对于 Object 对象，直接调用 toString()  就能返回 [object Object] 。而对于其他对象，则需要通过 call / apply 来调用才能返回正确的类型信息。

```js
Object.prototype.toString.call('') ;   // [object String]
Object.prototype.toString.call(1) ;    // [object Number]
Object.prototype.toString.call(true) ; // [object Boolean]
Object.prototype.toString.call(Symbol()); //[object Symbol]
Object.prototype.toString.call(undefined) ; // [object Undefined]
Object.prototype.toString.call(null) ; // [object Null]
Object.prototype.toString.call(new Function()) ; // [object Function]
Object.prototype.toString.call(new Date()) ; // [object Date]
Object.prototype.toString.call([]) ; // [object Array]
Object.prototype.toString.call(new RegExp()) ; // [object RegExp]
Object.prototype.toString.call(new Error()) ; // [object Error]
Object.prototype.toString.call(document) ; // [object HTMLDocument]
Object.prototype.toString.call(window) ; //[object global] window 是全局对象 global 的引用
```

# 如何判断

可以根据项目需求使用不同的方式对数据类型进行判断:

对于一般数据类型，如string,number,boolean,symbol,undefined我们可以使用*typeof*判断。

对于数组的判断一般直接使用ES5自带的Array.isArray(value)方法进行判断。

如果需要判断足够多的数据类型，使用Object.prototype.toString.call(value)是比较方便的。