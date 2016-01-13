title: box-model
date: 2016-01-13 13:22:28
tags: css
---
## 盒模型
div+css布局中，每个页面是由一个又一个小框构成的，一个个独立的盒子的横向竖向排布，组成了丰富的页面结构。
!["盒模型"](http://www.w3help.org/zh-cn/kb/006/006/boxdim.png)
<!-- more -->
而每个盒子内部是怎么样的，每个盒子都有复杂的结构。
* margin
margin 是指盒子的外边距，盒子border外面的一圈。
margin 非 table 类型的元素，以及 table 类型中 table-caption, table 和 inline-table 这3类。例如 TD TR TH 等，margin 是不适用的。 并且，对于行内非替换元素（例如 SPAN），垂直方向的 margin 不起作用。
* border
盒子的一个边框
* padding
盒子内部的一个内边距
* content


## margin的外边距折叠
> 需要注意--两个或多个毗邻的普通流中的块元素在垂直方向上的margin会折叠

归结起来有两点
* 这两个或多个外边距没有被非空内容、padding、border 或 clear 分隔开。
* 这些盒子都处于普通流中
```
<div>
  <div style="margin:50px;background:red">999</div>
  <div>nihao</div>
  <div style="margin:50px;background:red">999</div>
</div>
```
{% asset_img test03.png%}

其中参与margin折叠的两个box的margin正负会生成下面几种情况
* 参与折叠margin的正值
  以两个值的较大值为最终两者外边距值
* 参与折叠margin的负值
  以两个值的绝对值较大值为最终两者负外边距值
* 参与折叠margin的有正有负
```
<div style="margin:50px 0; background-color:green; width:50px;">
    <div style="margin:-60px 0;">
           <div style="margin:150px 0;">A</div>
    </div>
</div>
<div style="margin:-100px 0; background-color:green; width:50px;">
    <div style="margin:-120px 0;">
           <div style="margin:200px 0;">B</div>
    </div>
</div>
```
此处要计算B的margin重叠，则应取出正最大减去负最大，才是最终的A和B的margin值


## 盒模型计算的差异性
1. W3C标准盒模型
w3c对于元素高度、宽度的计算只是 content区域
2. IE怪异模式下的盒模型
IE怪异模式下对于高度和宽度的计算是content + padding + border

一个例子讲述两者区别
```
<style type="text/css">
#test{
  width:100px;
  height:100px;
  background:green;
  padding:10px;
  margin:10px;
  border:1px solid #eee;
}
</style>
<div id="test"></div>
```
1. w3c标准

{% asset_img w3cbox.png%}


2. ie6怪异模式

{% asset_img ie.png%}


而w3c为了调和现代web前端编程，增加了一个css的规则 box-sizing
有两个属性
* content-box
  规定盒模型以w3c标准进行渲染
* border-box
  规定盒模型以ie怪异模式进行渲染