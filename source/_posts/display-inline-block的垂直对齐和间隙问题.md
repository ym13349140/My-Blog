---
title: display-inline-block的垂直对齐和间隙问题
date: 2018-05-17 17:48:02
tags:
    - CSS
categories:
    - CSS
---

<blockquote class="blockquote-center">当我们对一个元素设置display:inline-block的时候，经常会出现一些意想不到的布局上的问题，包括上下错位，左右间隙，margin:auto失效等等，本文主要是对这几个问题的一个总结。</blockquote>

<!--more-->

### 前言
在写一个简单的布局测试的时候，给多个div设置了inline-block属性，然后发现他们的排列方式并不是想象中的整齐排列的上下错开了，而且左右直接也会有间隙，内部的inline-block元素也没法水平居中了，具体html代码如下：
```html
<div class="wrapper">
    <div class="div">
        <div id="inner-div" class="inner">innerDIV</div>
    </div>
    <div class="div">
    </div>
    <div class="div">
    </div>
</div>
```
CSS样式设置如下：
```CSS
.wrapper {
    width: 306px;
    margin: 100px auto;
    box-sizing: border-box;
    padding-top: 100px;
    background: aquamarine;
    position: relative;
    overflow: hidden;
}
.div {
    height: 100px;
    width: 100px;
    display: inline-block;
    margin: 0;
    border: 1px solid red;
}
#inner-div {
    width: 50px;
    height: 50px;
    display: inline-block;
    margin: 0 auto; /*这里没有生效*/
    background-color: blueviolet;
}
```
然后显示出来的效果是这样的：
{% qnimg CSS/display-inline-block错位问题.png title:display-inline-block错位问题 alt:display-inline-block错位问题示意图 extend:?imageView2/2/w/600 %}

这里主要有三个问题，接下来我们一一来解决。

### margin:auto失效的问题
我们发现给innerDIV设置了 `margin:0 auto` ，但是紫色的innerDIV并没有如我们所愿水平居中，仍旧在父元素的最左边，这是因为，给块级元素设置 `margin:0 auto` 之后，浏览器会根据父元素的宽度的宽度去计算当前块级元素的可用宽度的值（当前元素也必须设置了宽度值），然后将当前块级元素border以外的空间都作为它的margin值，然后用margin去填满剩余的可用空间。但是当我们给当前块级元素设置了 `display: inline-block` 之后，他就变成了一个行内元素，会跟其他的inline或者inline-block元素同处于一行，虽然他还是有自己的宽度和高度，但是父元素中剩余的宽度是留给其他行内元素的，所以浏览器获取不到当前元素可用宽度，就会将设置为了auto的margin值设置为0。
那么如果此时还要让当前元素水平居中的话，可以通过在父元素设置 `text-align: center` 来实现。

### 垂直错开问题
解决了水平居中的问题，我们再来看垂直错开的问题，按照正常的思维，两个同级的 inline-block 的 div 元素，应该是并排在同一行对齐才是，但是实际的效果却是一上一下错开的，归根到底的话，这是由于 vertical-align 属性造成的，我们仔细观察会发现，上图中右边的div的下边界是与左边紫色innerDIV里面的文字的下边界对齐的，也就是我们所知的 `vertical-align: baseline` 的效果。这是因为对于所有的行内元素（包括inline和inline-block），vertical-align 属性默认的值都是baseline，即使CSS中没有设置，但是默认都是有这个属性的。
对于同一级的兄弟div元素设置了 `display: inline-block` 之后，就分为以下两种情况：
> 1. 如果这些div里面都没有其他元素或者这些div本身设置了overflow属性（不为visible），那么这些div会默认并排对齐排列，也就是预想的样子，因为在这种情况下，这些div的baseline其实就是他们的margin的底边缘；
> 2. 当其中某些div（上图中左边的div）当中包含了其他行内元素，例如文字、图像或者icon等元素时，它的baseline就会变成他的子元素中的最后一个正常文档流中的line-box的baseline来决定，在上面的例子中就是那一行文字的下边缘，这时候我们其实可以通过设置vertical-align为top/middle/bottom 改变垂直对齐的方式实现垂直对齐，或者是直接设置overflow为hidden/auto/scroll 改变baseline为margin下边界的方式来实现垂直对齐。

关于baseline更详细的解读，请参考[此篇文章](https://segmentfault.com/a/1190000002668492)

### 水平间隙问题
当多个同级的div元素都设置了 `display: inline-block` 的时候，我们会发现即使设置了margin为0，相邻的div元素之间也不是紧密排列的，而且有一个空隙，这是因为我们在HTML文档里两个div之间有换行引起的，设置了 `display: inline-block` 之后div虽然变成了行内元素，但是换行符还是被保存下来了，所以会出现空隙。解决方法主要有以下几种：
>1. 将相邻两个元素之间的换行采用 `<!--><-->` 注释掉
>2. 相邻两个元素的标签首尾相连消除换行，例如：
```html
<div>
   div-1
</div><div>
   div-2
</div>
```
> 3. 设置margin-left或者margin-right为负值，具体的数值对于不同浏览器的不同字体大小也会不一样（不实用）
> 4. 设置letter-spacing或者word-spacing为负值，具体数值同上，这种就需要在这些div元素的子元素中将letter-spacing和word-spacing重新设置回正常值，以保证内部的文字能正常显示（较麻烦）
> 5. 在父元素中设置font-size为0（较常用），这样回车符的宽度就变为了0，跟第四种方法一样，需要在内部子元素中将font-size设置为正常值以便内部的文字能正常显示。

最终，正常的样式显示如下：
{% qnimg CSS/display-inline-block正确显示.png title:display-inline-block正确显示 alt:display-inline-block正确显示示意图 extend:?imageView2/2/w/600 %}