---
title: JS字符串子串截取方法对比
date: 2018-05-06 16:25:06
tags:
    - JS
    - 字符串操作
categories:
    - JS
---


<blockquote class="blockquote-center">JS String对象的三种截取子串操作---substr/substring/slice对比</blockquote>

<!--more-->

## substr
> * 用法：`string.substr(start,length)`
> * start：必需的，表示子串的起始下标，可以为负数，负数表示从尾部算起（例如：-2表示倒数第二个）
> * length：可选参数，必需是正整数。省略改参数表示一直截取到字符串尾部

## substring
> * 用法：`stringObject.substring(start,end)`，截取到的字符串为`[start,end)`，左闭右开
> * start：必需的，表示子串的起始下标，必需是**非负整数**
> * end：可选参数，必需是**非负整数**，表示要提取的子串的最后一个字符在原字符串中的位置再往后一位。省略该参数将截取到原字符串尾部。  

*【注意】* 
1. `substring` 理论上不接受负整数的参数，但实际使用了负数时不会报错，会直接忽略
2. 如果参数 `start` 与 `end` 相等，那么该方法会返回一个空串（即长度为 0 的字符串）;若 `start` 比 `end` 大，那么在提取子串之前会先交换这两个参数，然后再截取子串。

## slice
> * 用法：`stringObject.slice(start,end)`，截取到的字符串为`[start,end)`，左闭右开（与substring用法相同）
> * start： 必需的，表示子串的起始下标，可以为负数，负数表示从尾部算起（例如：-2表示倒数第二个）
> * end：可选参数，表示要提取的子串的最后一个字符在原字符串中的位置再往后一位。省略则表示截取到原字符串结尾。可为负数，负数表示从尾部算起  
*例如： `var str="abcdefg"; str.slice(-5,-1)` 表示从倒数第五个截取到倒数第一个（不包括倒数第一个），即为 `cdef`。*

*【注意】* 
1. `slice()` 不支持 `start `比 `end` 大，会返回空字符，例如`str.slice(4,1)`、`str.slice(-2,-4)` 都会返回空字符串；
2. 当 `start` 为负数， `end` 为正数时， 必须 `start` 所表示的位置在 `end` 所表示的位置的前面才能正常截取，否则会返回空字符串；例如：
```Javascript
  var str="abcdefg"; 
  console.log(str.slice(-5,1)); //空串
  console.log(str.slice(-5,6)); //cdef
```