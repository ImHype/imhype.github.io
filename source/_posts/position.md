title: position个人理解
date: 2016-01-13 14:18:41
tags: css
---
# position的个人理解

position 是指元素在网页中所存在的方式，给元素设置不同的position会改变元素的包含块。

学习position的时候，要搞懂另外几个上下文的概念 normal flow(常规流)、containning block、bfc、base line等等等。
<!--more-->

一一展开介绍

## 常规流（normal flow）
* 什么是常规流？

常规流就是元素该有的排布方式，具体即FC的展现形式，块级元素有块级元素的展现，行内有行内的展现，按照块级上下文和行内上下文排布的方式即位常规流

* 常规流是怎样产生的？

所有FC容器内部都会形成一个常规流，具体参见BFC与IFC的规则与展现形式

* 什么会脱离常规流？

position为非static和float为非none的时候

## BFC（块级元素格式化上下文）

* 什么是BFC

他是一个格式化区域，有他自己的规则，满足规则之后的一定区域内就是一个块级格式化上下文的区域

* 展现形式
	* 内部的Box会在垂直方向，一个接一个地放置。
	* Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
	* 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
	* BFC的区域不会与float box重叠。
	* BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
	* 计算BFC的高度时，浮动元素也参与计算

每条指示都很好理解，概括来说就是 垂直排布，margin重叠，与包含块贴合，杜绝浮动，独立空间，高度由所有内部元素决定

* 而哪些情况会生成这块级格式化上下文呢？
	* 根元素
	* 有浮动的元素内部
	* position 为absolute和fixed
	* display inline-block, table-cell, table-caption, flex, inline-flex
	* overflow 不为visible

怎么理解呢？

首先，根元素，浮动元素，position absolute 和fixed，和inline-block等等内部形成了一个独立空间，与外界不相干
而overflow:hidden;则是强制生成一个独立的空间，所有margin和padding的排布都按照box的规则进行排布
所以我认为BFC以及所有FC最重要的一个概念就是格式化上下文，里面的子元素不会影响到外面的元素

## IFC与base line

要理解IFC，必须要搞清楚的几个概念： line boxes 、base line 

* 什么是IFC（inline formatting context）。

一个独立的行内格式化上下文区域

* line boxes

位于行内格式化上下文中，每个行框是独立的框子，行框一个接一个地水平排列，起点是包含块的顶部。 水平方向上的 margin，border 和 padding 在框之间得到保留，垂直方向的margin不起作用，padding和border有效

* 行框的范围

第一个行框一边贴合包含块的一边如果内部存在float的元素会缩短行框的宽度，

* 行框的对齐

vertical-align属性，包含九个不同的值，就像英语本子上四条线一样，规定了行框垂直方向上的对其方式。

	* baseline -- 文字的基线与行框的基线对其
	* sub -- 基线与行框下标对其
	* super -- 基线与行框上标线对齐
	* top -- 顶端与顶端对齐
	* text-top -- 元素顶端和父元素内容区对齐
	* middle -- 元素的中垂点与 父元素的基线加1/2父元素中小写字母的高度 对齐？？？。
	* bottom -- 元素底部与底部对齐
	* text-bottom -- 文字底部和父元素内容区底部对齐
	* inherit -- 继承样式

text-top与 top可能会表现的一样，但是这只是巧合，top是跟着line-height走的，取决于行框的line-height,而text-top只会沿着文字的顶端排布

text-align："center","left","right"表示行框的水平对齐
有必要画一下那八条线的理清楚一下具体的位置

* 行框可以被折断
当行框内容的宽度大于行框可以接受的宽度的时候，行框就会从中间折断，并且自动转到下一行

## 包含块（containing-blog）

* 什么是包含块？
包含块，是指在排版上包裹一个元素的元素，不一定是那个元素的父层元素，该的所有布局都是围绕他展开的

* 包含块的判定规则，伪代码展现
``` js
if(元素是根元素){
	他的包含块就是初始包含块
}else{
	switch(position){
		case "static或者relative":
			他的包含块是最近的块级或单元格或行内块祖先元素的内容区域
			break;
		case "absolute"
			if(存在position为非"static"的祖先元素){
				if(该祖先元素为行内){
					他的包含块是该祖先元素的内边距边界
				}else{
					switch(direction){
						case "ltr":
							包含块的左上边界是该行内祖先元素文本的左上边界，右下边界是该行内级祖先元素文本的右下边界
							break;					
						case "rtl":
							包含块的右上边界是该祖先元素文本的右上边界，做下边界是该祖先元素文本的左下边界
					} // 不同浏览器展示的方式不同，谷歌浏览器经测试，包含块宽度不会为负，最多为0；
				}
			}else{
				他的包含块是初始包含块
			}
	}
}
```
如上便是包含块概念的判定标准，要按照流程一步步判断，包含块概念确定很重要，他将决定你的一些position布局是如何展开的

好了，理清楚上述一些概念之后，可以更好的理解position的一些形态上的问题。

position包含四个值："static","relative","absolute","fixed"

首先根据包含块，判断他的包含块是哪一个，他的"left","top","right","bottom"的属性都是根据包含块来确定的

此处，同一方向的两个属性的取值有一定的规则

* "left" 和 "right" 都会auto ，则left=0;
* "left" 和 "right" 其中一个是auto，另外一个有值，则取有值的那一个
* "left" 和 "right" 都有值，此时包含块就起作用了，if(包含块的direction为ltr)则根据left来确定 else 根据right来确定
* "bottom" 和 "top" 如果都有值，则按照top的值来计算，因为元素垂直方向的排布都是从上到下排布

## z-index

position为非static的时候，元素会有一个z-index的属性，决定重叠区域的元素排布。

他遵从几条比较准则：
* 在同一包含块下，都没有z-index属性的元素，各元素之间按照元素的先后性进行排布，先出现的在下面，后出现的在上面
* 在同一包含块下，规定了z-index，则z-index大的比z-index小的更上层，不赋值则等同于0 
* 不同包含块下的元素无法直观比较z-index，应该先比较同一届比包含块的z-index

**至于vertical-align:middle 未找到正确的介绍，暂时留空，以后查阅资料之后补充**
