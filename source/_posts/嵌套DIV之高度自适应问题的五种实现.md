---
title: 嵌套DIV之高度自适应问题的五种实现
date: 2018-05-06 16:56:16
tags:
    - CSS
    - 网页布局
categories:
    - CSS
---

<blockquote class="blockquote-center">嵌套DIV之高度自适应问题----CSS样式</blockquote>

<!--more-->

在自己实习生面试的时候遇到的一个问题，当时只答出来了两种实现，事后自己也去总结了一下，发现以下五种方法都可以实现。

## 问题描述
一个外层div里面嵌套两个内部div，外层div高度固定（假设未知），内层上面的div高度固定，如何让下面的div实现撑满外层的div高度？

> 看到过网上有类似的问题，但是大部分都是假设外层高度为100%或者是已知的，而我遇到的是外层高度虽然固定，但是我们并不知道是多少（这里需要说明一下，代码中外层的高度我虽然自己给了固定值，但是只是为了效果，后面其实都没有用到这个值，基本就满足我们不知道外层的具体高度是多少这个条件了），所以也参看了网上的一些零散的解法，自己也都去实践了一下，最后总结出五种可以实现的方法。

## HTML结构
这里外层div高度未知，内层两个div上下排列。

```HTML
<div class="wrapper">
    <div class="div1">这里是div-1</div>
    <div class="div2">这里是div-2</div>
</div>
```

## 五种实现方法

### 1、使用flex布局实现
  ```CSS
  .wrapper {
      width: 200px;
      height: 400px;
      margin: 100px auto; /*这里的margin纯属为了测试时候的显示效果居中，无实际用途*/
      background: aquamarine;
      display: flex;
      flex-direction: column;
  }

  .div1 {
      height: 100px;
      width: 100%;
      background-color: violet;
      order: 0;
  }

  .div2 {
      background-color: teal;
      width: 100%;
      order: 1;
      flex-grow: 1;
  }
  ```

### 2、下面的div使用position:absolute + top/bottom实现
  ```CSS
  .wrapper {
      width: 200px;
      height: 400px;
      margin: 100px auto;
      background: aquamarine;
      position: relative;
  }
    
  .div1 {
      height: 100px;
      background-color: violet;
  }

  .div2 {
      background-color: teal;
      width: 100%;
      position: absolute;
      top: 100px;
      bottom: 0;
  }
  ```

### 3、下面的div设置margin-top为负值实现
  ```CSS
  .wrapper {
      width: 200px;
      height: 400px;
      margin: 100px auto;
      background: aquamarine;
      position: relative;
  }
    
  .div1 {
      height: 100px;
      background-color: violet;
      position: relative;
  }

  .div2 {
      background-color: teal;
      width: 100%;
      height: 100%;
      margin-top: -100px;
      box-sizing: border-box;
      padding-top: 100px;
  }
  ```
### 4、外层div设置padding-top，内层上面的div绝对定位实现

  ```CSS
  .wrapper {
      width: 200px;
      height: 400px;
      margin: 100px auto;
      box-sizing: border-box;
      padding-top: 100px;
      background: aquamarine;
      position: relative;
  }

  .div1 {
      height: 100px;
      width: 100%;
      background-color: violet;
      position: absolute;
      top: 0;
  }

  .div2 {
      background-color: teal;
      width: 100%;
      height: 100%;
  }
  ```
### 5、上面的div绝对定位，top:0；下面的div设置box-sizing + padding-top

  ```CSS
  .wrapper {
      width: 200px;
      height: 400px;
      margin: 100px auto;
      background: aquamarine;
      position: relative;
  }

  .div1 {
      height: 100px;
      width: 100%;
      background-color: violet;
      position: absolute;
      top: 0;
  }

  .div2 {
      background-color: teal;
      width: 100%;
      height: 100%;
      box-sizing: border-box;
      padding-top: 100px;
  }
  ```
