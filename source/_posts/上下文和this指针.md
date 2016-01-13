title: 上下文和this指针
date: 2016-01-13 14:22:48
tags: JavaScript
---
# 上下文和this指针

> A this value is a special object which is related with the execution context. Therefore, it may be named as a context object (i.e. an object in which context the execution context is activated).

> this适合执行的上下文环境息息相关的一个特殊对象。因此，它也可以称为上下文对象\[context object\](激活执行上下文的上下文)。

this与变量不同，其实是对一个区域上下文环境的记录，他与变量不同的是

* 他不会有一个向上搜寻的过程
* 不能给this赋值

然后变量和this在某个点还是有一处交集的，就是全局变量

```
var a=0;
console.log(this.a==a);
```
global context 是个很神奇的作用域，同时适用于this的属性和全局变量

修改函数this指向的方法有没有，当然有
* call/apply
```
var a = {
	"nihao":"1",
	"say":function () {
		console.log(this.nihao);
	}
}
this.nihao="2"
a.say.call(this)
```
此处本来a.say的this本来是指向a的，不过由于a.say.call()函数的关系，导致a.say这个函数this指向了全局变量的this
apply也同理，只不过在于传参的位置，apply的第二个参数是一个数组作为a.say的传参，call的后续参数会做为a.say的传参

还有 functionName.caller 和 arguments.callee 的关系
functionName.caller 是该函数的调用者
arguments.callee 是该函数自身

```
function callerDemo() {
    if (callerDemo.caller) {
        var a = callerDemo.caller.toString();
        alert(a);
    } else {
        alert("this is a top function");
    }
}
function handleCaller() {
    callerDemo();
}
handleCaller();
```
functionName.caller 常用于判断有没有父函数
而 arguments.callee 则可用于自身函数的再次执行
```
function alertAgain(){
	alert(0);

	if(alertAgain.caller)
		return;
	arguments.callee();
}
alertAgain();
```