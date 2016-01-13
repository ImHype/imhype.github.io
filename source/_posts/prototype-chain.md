title: prototype chain
date: 2016-01-13 14:21:17
tags: JavaScript
---
# prototype chain

prototype 是 constructor 的其中一个属性
拥有在创建实例的过程中引用给实例的功能

首先，有个问题，在new 一个构造函数的时候，js为我们做了什么？

1 把函数内部的this指针指向新创建的对象
2 把新对象的__proto__属性指引到构造函数的prototype
3 完成对象的初始化
4 把这个对象隐式地return 出来

```
var a = {
  x: 10,
  calculate: function (z) {
    return this.x + this.y + z
  }
};
 
var b = {
  y: 20,
  __proto__: a
};
 
var c = {
  y: 30,
  __proto__: a
};
 
// 调用继承过来的方法
b.calculate(30); // 60
c.calculate(40); // 80
```
既然已经知道了prototype的规则，
也可以按照如上的方式，直接修改新对象的__proto__，此方式与
```
var a = function(){
	return this;
}
a.prototype.x = 10;
a.prototype.calculate = function(z){
	return this.x+this.y+z
}
var b = new a();
b.y = 20;
console.log(b)
console.log(b.calculate(30));
```
构造出来的这个对象是一致的

而经过不断的创建实例，其实__proto__属性是存在嵌套的，
如一个对象会展现如下姿态
```
a={
	x:1,
	__proto__:{
		y:2,
		__proto__:{
			z:3,
			__proto__:{
				n:0
			}
		}
	}
}
```
此时a可以获取到来自祖先prototype的n属性的
不过通过 console.log(a.hasOwnProperty("n"));
可以确定n不是来自a本身，而是来自他的prototype层的

看了原型链，接下来我们来看看对象的继承，一般有两种常见的方式
1 空对象实例化后赋给 prototype
```
function a(){
	this.x=1;
}
a.prototype.m = function(){
	alert(1);
}
function Empty(){	
}
Empty.prototype = a.prototype;
Empty.constructor = Empty;
function b(){
	a.apply(this,arguments);
}
b.prototype=new Empty();
b.constructor = b;
```
2 对象的枚举
```
function a(){
	this.x=1;
}
a.prototype.m = function(){
	alert(1);
}

function b(){
	a.apply(this,arguments);
}
for(var i in a.prototype){
	b.prototype[i] = a.prototype[i];
}
b.constructor = b;
```