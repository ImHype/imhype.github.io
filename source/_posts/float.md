title: float
date: 2016-01-13 14:05:37
tags: css
---

# 浮动与文字环绕

理解浮动，现需要清楚position那一讲讲到的包含块、常规流和BFC的概念

讲下浮动排布的原理：

设置float不为none以后，该浮动框会向左或向右偏移，直到接触到他的包含块边界或者是另外一个浮动元素的边界
<!--more-->

# 缩短了行框宽度
前面那一节讲行框的时候讲到，行框的宽度受浮动元素影响，行框的左上边界是贴合包含块的左上内容边界的，若存在浮动框，则会导致该行框的左上边界不予包含块贴合，从而缩短了行框宽度

# 文字环绕
文字环绕其实是行框的一个非常重要的特性，首先行框从浮动框右边开始排列，并不断的累积，遇到容器宽度大于自己内容时，出现换行，而当文字内容的高度大于浮动框的高度时，行框会出现占满整个父容器宽度的情况。此现象即为文字环绕。

# 浮动框定位规则

* 浮动框不能在其浮动方向上溢出包含块
假如说float:left;则该元素不会向左超出包含块
* 当父容器不足以放置下一个浮动元素的时候，下一个浮动元素会另起一行放置
```html
	<div style="width:200px;">
		<div style="width:50px;background:yellow;float:left;">a</div>
		<div style="width:151px;background:red">b</div>
	</div>
```
* 浮动框与不同向浮动框不可重叠，会掉下来
```html
<div style="width:200px">
	<div style="width:110px;background:yellow;float:left">a</div>
	<div style="width:110px;background:yellow;float:right">b</div>
</div>
```
* 浮动框的顶外边不能高于它包含块的顶部。
另外，当一个浮动框发生在两个外边距折叠的中间时，浮动元素的定位好像它有另一个空的块级父框位于常规流中。

也就是说，当浮动框处于两个发生外边距折叠的地方时，会被当作包含在一个空的块框中，它上面和下面的外边距会穿过它发生外边距折叠，当它不存在。

```html
<style type="text/css">
	.test01 div{
		width: 100px;
		height: 100px;
		background: red;
		margin: 10px;
	}
</style>
<div class="test01">
	<div>A</div>
	<div>B</div>
	<div>C</div>
</div>
```
{% asset_img test01.png%}


如上所示，B处在A和C的中间，且不影响A和C的margin重叠，且B不与A进行margin重叠，说明B有个一个隐形的包含框，而包含框的顶部不高于A的外边框底部


* 浮动框的顶边不可以高于源文档中先前元素产生的块框或浮动框的顶
```
<style type="text/css">
	.test02{
		width: 190px;
	}
	.test02 div{
		width: 100px;
		height: 100px;
		background: red;
		float: left;
	}
</style>
<div class="test02">
	<div>A</div>
	<div>B</div>
	<div style="float:right;width:20px;background:yellow">C</div>
</div>
```
<style type="text/css">
	.test02{
		width: 190px;
	}
	.test02 div{
		width: 100px;
		height: 100px;
		background: red;
		float: left;
	}
</style>
<div class="test02">
	<div>A</div>
	<div>B</div>
	<div style="float:right;width:20px;background:yellow">C</div>
</div>

{% asset_img test2.png%}

如上，C 不会高于A而存在（不高于先前元素产生的框块或浮动框的顶）