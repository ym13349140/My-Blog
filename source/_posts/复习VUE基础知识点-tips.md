---
title: 复习VUE基础知识点-tips
date: 2018-07-03 20:13:18
tags:
    - JS
    - vue.js
categories:
    - JS
---

<blockquote class="blockquote-center">记录vue基础复习过程中的一些琐碎知识点</blockquote>

<!--more-->
> 1. v-if 和 v-show 对比：
> * `v-if`: 惰性渲染，只有在满足 if 条件为真时才会真正渲染对应的DOM元素内容，否则不渲染此DOM元素。它可以确保条件块内的事件监听器和子组件都会随着条件的切换适当地被销毁和重建。可以结合 `v-else-if` 和 `v-else` 指令一起使用。
> * `v-show`: “假”的条件渲染，无论条件是否为真，条件块都会被渲染并保留在DOM中，只不过是通过切换CSS的 display 属性来实现显示和隐藏的效果。

> 总的来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。