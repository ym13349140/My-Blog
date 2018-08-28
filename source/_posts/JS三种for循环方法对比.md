---
title: JS循环遍历方法对比
date: 2018-05-06 13:15:11
tags:
    - JS
categories:
    - JS
---

<blockquote class="blockquote-center">JS 几种循环遍历的方法对比---for...in/for...of/forEach/Object.keys()</blockquote>

<!--more-->

## for...in
> * 作用：用于遍历对象可枚举属性的属性名（包括继承来的属性和用户添加的额外属性，但不包括数组的length属性）
> * 对于数组对象，`for...in` 实际遍历的是数组的index，而并非数组的value
> * `for...in` 不适用与遍历 `Map` 和 `Set` 对象

## for...of(ES6新增方法)
> * 作用：用于遍历可迭代对象（`Array, Map, Set, String, TypedArray, arguments, NodeList`）的属性名称，只包含对象本身定义时的属性（不包括继承来的属性和用户后期添加的额外属性）
> * 对于 `Array` 对象和 `Set` 对象，`for...of` 遍历的是他们实际的value值
> * 对于 `Map` 对象，`for...of` 遍历的是对象中的每一个键值对
> * 对于 `Object` 对象，`for...of` 不能直接遍历 `Object` 对象，可结合 `Object.keys()` 来遍历

## forEach
> * `forEach()` 是数组的一个方法，用于遍历数组的每一项，并对每一项执行一个 `callback` 函数；`forEach()` 没有返回值，返回值总是 `undefined`。
> * 对于 `Array` 对象，`callback` 函数为：`function(element, index,arr){}`；
> * 对于 `Set` 对象，`callback` 函数为：`function(element, sameElement, set){}`；因为它没有 `index` 值，所以回调函数的前两个参数都是元素本身。
> * 对于 `Map` 对象，`callback` 函数为：`function(key, value, map){}`
> * `forEach` 中不能中断循环(使用break语句或使用return语句)

## Object.keys()
> * 作用：返回对象自身可枚举的属性组成的数组，不包括原型链上的属性及Symbol属性
> * 对于数组的遍历顺序与for in 一致，但不包括原型链上的部分