title: for循环性能优化
date: 2016-01-13 14:20:05
tags: JavaScript
---
# for循环性能优化

在js中你经常需要用选择器来获取一系列的元素，并且对元素进行批量的操作，如
```
var btns = document.getElementsByTagName("button");
for(var i =0;i<btns.length;i++){
	btns.className="nihao";
}
```
然而这种方式是最低效的，首先每次循环需要不断地换区btns.length，故对此可做如下修改
```
var btns = document.getElementsByTagName("button");
for(var i = 0,len = btns.length;i<len;i++){
	btns.className="nihao";
}
```
如上的方式好了好多，但是"i<len"处还可以做做文章，没有上面能和0比对更快的事情了，故做倒序递减
```
var btns = document.getElementsByTagName("button");
for(var i = btns.length;i--;){
	btns.className="nihao";
}
```
"i--"处做了两步，
首先 i = i-1;
然后判断i是否为零，如果不为零继续执行，如果为零则停止循环

还有就是在遇到大范围的for循环修改dom的话，要巧用文档碎片，来优化代码性能

# for-in
for in 用于循环一个没有序号的对象，可以用来枚举一个对象的可枚举性的属性，如一个{}的枚举，Object.prototype的枚举
一般数据不适合用for-in来遍历因为for-in是不能保证循环顺序的
在遍历prototype的时候，大部分时候需要加一把锁 Object.prototype.hasOwnProperty 把不是自己的属性给过滤掉