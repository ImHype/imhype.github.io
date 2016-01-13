title: Object.create
date: 2016-01-13 14:20:54
tags: JavaScript
---
# Object.create 与 Object.defineProperty
Object.create() 是什么？
Object.create(proto [, propertiesObject ]) 是ES5中新引入的对象快捷创建方式

第一个参数是一个{}，用于指向新创建的函数的prototype，如果为NULL，则创建一个__proto__指向Object的对象

第二个参数也是一个{}，有属性名
* writable:是否可任意写
* configurable：是否能够删除，是否能够被修改
* enumerable：是否能用 for in 枚举
* value：值
* get: 当给该函数取值时采取的方法
* set: 当给该函数赋值时采取的方法

当然Object.create虽然好用，但是由于是es5 故有兼容问题，不过搞清楚他的原理还作用之后，还是可以做兼容的
```javascript
inherit = function(p,descriptors){
	if(Object.create){
		return Object.create(p)
	}
	function f(){}
	if(p==null){
		p=Object.prototype;
	}
	f.prototype=p;
	return new f();
}
```
而由于es3没有提供对象的property description 故无法做第二个传参的兼容

Object.defineProperty 其实是一样的
对一个已经创建的对象，修改他的属性
```javascript
var a = {x:1};
Object.defineProperty(a,"x",{
	/**
	* 此处为option
	*/
});
```