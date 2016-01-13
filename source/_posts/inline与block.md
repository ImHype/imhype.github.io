title: inline与block
date: 2016-01-13 14:17:27
tags: css
---
# 页面之本：inline 与 block

讲完页面的盒子模型，可以理解到网页上丰富的页面效果是由一个一个盒子构成的，而盒子种类，其实有很多种，用的最多的就是inline、block与inline-block，这节我们重点讲下inline和block属性

## display:inline

行内元素的默认值即为 display:inline


常见的元素有 a,span,b,em,font,i,small,strong,u

想要搞懂 display:inline 的一些特性还是要理解 ifc 一些特性

对于行内元素的特性，做简单介绍

* 一个行内元素且内部不包含display:block属性的元素，具备一个行内格式化上下文，生成一个独立的行内区域，并包裹了一个个的行框

* 行内元素不允许你设置高度和宽度，元素的宽高是内容的高宽

* 行内元素内部地水平排布，起点是包含块的顶部。水平方向上的 margin，border 和 padding 在框之间得到保留，垂直方向的margin不起作用，padding和border有效

* 行内元素内部可以被折断

当行内元素内容的宽度大于行框可以接受的宽度的时候，行框就会从中间折断，并且自动转到下一行

* vertical-align

这是一个规定垂直方向对齐方式的属性，具有

	* baseline -- 文字的基线与行框的基线对其（默认）
	* sub -- 基线与行框下标对其
	* super -- 基线与行框上标线对齐
	* top -- 顶端与顶端对齐
	* text-top -- 元素顶端和父元素内容区对齐
	* middle -- 元素的中垂点与 父元素的基线加1/2父元素中小写字母的高度 对齐？？？。
	* bottom -- 元素底部与底部对齐
	* text-bottom -- 文字底部和父元素内容区底部对齐
	* inherit -- 继承样式

九大属性。

而top、text-top与bottom、text-bottom之间有时可能会重合
出现这种情况，是因为

* top与bottom 是跟着line-height变化的且位于行框的底部，而text-top与text-bottom则是用于位于文字内容的底部

* 当元素的line-height小于等于元素的font-size的时候，两者重合


## display:block

块级元素默认的display值为block

常见的块级元素有 div,p,h1~h6,pre,form,ul,li,hr等等等
块级元素有哪些特征呢？

* 正常流下，块级元素从上到下排布，且可以依次堆叠

* 默认宽度是包含块的100%
```
<div style="width:500px;">
	<div style="position:fixed;width:100%;background:red;">nihao</div>
</div>
```

{% asset_img block_width.png%}

* 高度，行高以及顶和底边距都可控制

<small>注：在学习block的时候不能忽略margin collapse以及ie6下的float+block的双倍margin（具体看我position一讲）</small>