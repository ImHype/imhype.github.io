title: inline-block与float
date: 2016-01-13 14:15:56
tags:
---
# inline-block与float

一直在想inline-block与float有什么关系呢，因为觉得inline-block与float的的效果是类似的
比如，我要做一个nav，我有两种方式
```
	<style type="text/css">
		.float li{
		float: left;
		width: 200px;
		line-height: 30px;
		background: yellow;
		border: 1px solid #eee;
		list-style: none;
	}
	.float{
		overflow: hidden;
	}
	</style>
	<!-- banner1 -->
	<ul class="float">
		<li>1</li>
		<li>2</li>
	</ul>
```

```
	<style type="text/css">
	.ib li{
		display: inline-block;
		width: 200px;
		line-height: 30px;
		background: green;
		border: 1px solid #eee
	}
	<!-- banner2 -->
	<ul class="ib">
		<li>1</li><li>2</li>
	</ul>
	</style>
```

{% asset_img float&inline-block.png %}

由上面的示例表明，某些情况上float与inline-block之间可以表现出相同的外观
而这两种布局方式是怎么一个产生效果的过程了

1. float + "display:inline"或者"display:block"
float的元素在内部会创建一个独立的BFC空间，使该容器完全独立于外部，并拥有块级元素的属性
设置float不为none的元素会向他的一个浮动方向移动，造成了水平排练的现象 ---- 一种inline-block化

2. "display:inline-block"
设置"display"为"inline-block"的元素，拥有block元素的盒模型，同时又能向inline元素一样水平排列

那对于这两种方式，各有什么优劣呢？

* "float"的元素由于是通过浮动实现，故会产生脱离常规流这一现象。后续的不需要用到水平排列的元素需要清除浮动；而"inline-block"的元素不需要清除浮动，也不能实现环绕布局

* 水平居中，inline-block的元素可以轻易的"text-align:center"进行水平居中，显然浮动的元素是无法使用这一点的

* 垂直方向 inline-block元素具有vertical-align属性，浮动也没有

* 空白部分 ：由于inline-block 在外部展现出来inline的布局方式，所以如果在两个Inline-block之间存在空白的话，在网页中显示时，也会存在空白
	* 删除html中的空白
	* 给父元素设置font-size:0，子元素重新设置font-size属性