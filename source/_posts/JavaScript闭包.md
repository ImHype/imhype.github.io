title: JavaScript闭包
date: 2016-01-13 14:22:07
tags: JavaScript
---
# 作用域和闭包
每个函数都有自己的作用域，而每个变量其实是有作用域链
```
var a = 1;
(function(){
	a = 0;
}());
console.log(a);
```
此处由于a是全局的变量，故在自执行函数里面改变a的值，函数外部也会随之改变

如果换成a作为function内的局部变量呢,如何实现外部进行内部函数的删改呢
```
function a(){
	var a = 1;
}
console.log(a);
```
可以这么玩
```
function a(){
	var a = 1;
	return {
		getA:function(){
			return a;
		},
		setA:function(b){
			a = b;
		}
	}
}
a().getA();
a().setA(5);
```
此处实现了外部对内部变量的读取，return 出来的getA和setA有从外部作用域获取 function a 内部变量的权力，形成了闭包
而由于getA 和 setA 的关系，a变量会保存在内存中

另外还有
```
for(var i=0;i<5;i++){
	(function(index){
		buttons[index].onclick=function(){
			console.log(index);
		}
	}(i))
}
```
此处由于自执行函数内部的onclick的相应函数读取了onclick函数外面的index属性，故该函数被以闭包的形式保存了下来，导致引用了的index也保存了下来，其实与下面的方式是一样的
```
for(var i=0;i<5;i++){
	(function(){
		var index = i;
		/**
		* 不同的index
		*/
		buttons[index].onclick=function(){
			console.log(index);
		}
	}())
}
```
为什么
```
for(var i=0;i<5;i++){
	var index = i;
	buttons[index].onclick=function(){
		console.log(index);
	}
}
```
不行呢？
因为:
1. for循环是没有作用域的
2. 5个index处于同一级别的作用域内，被修改了

```
var x = 10;
 
function foo() {
  console.log(x);
}
 
(function () {
 
  var x = 20;
 
  foo();
 
})();
```
由这个例子可以得出，一个函数在被创建（是创建，不是执行）的时候，会保存外部的作用域，以用于未来函数调用时的变量查找