---
title: JS异步方法执行顺序对比
date: 2018-05-06 14:45:34
tags:
    - JS
    - 异步执行
    - Node.js
categories:
    - JS
---


<blockquote class="blockquote-center">JS 异步操作的执行顺序问题(不同队列)
    `setTimeout/setInterval/setImmediate/Promise/process.nextTick`
</blockquote>

<!--more-->

## JS任务介绍
> JS任务分为同步任务和异步任务。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

> 浏览器中, js引擎线程会循环从任务队列中读取事件并且执行, 这种运行机制称作 Event Loop (事件循环).

## 任务队列介绍(TaskQueue)
> JS任务队列可以主要有`Macro-TaskQueue/Micro-TaskQueue/Tick-TaskQueue`三种。
> * `Macro-taskQueue`：相当于JS中的主线程，处理正常顺序的主线程任务，包括`rendering`, `script`(页面脚本), 鼠标, 键盘, 网络请求等事件触发，以及通过 `setTimeout`、 `setInterval` 和 `setImmediate` 添加的任务；
> * `Micro-TaskQueue`： 相当于JS中的异步任务线程，主要用于保 存`Promise.then` 创建的异步任务;
> * `Tick-TaskQueue`： 是介于`Macro-TaskQueue` 和 `Micro-TaskQueue` 之间的一个任务队列，主要用于保存`node.js` 中的 `process.nextTick` 异步事件。`node.js` 中有一个_tickCallback函数，在每一次执行完Macro-TaskQueue的一个任务之后被调用，_tickCallback函数主要做了以下两件事：
>   1. 第一步，将当前Tick-TaskQueue当中的所有任务执行完；
>   2. 第二步，执行_runMicroTask函数，执行Micro-TaskQueue当中的任务。

> * 三者的结构图如下：

{% qnimg JS/queue.png title:queue alt:queue示意图 extend:?imageView2/2/w/600 %}

## 主线程（Macro-queue）执行流程
> 主线程（Macro-queue）内部又遵循着一定的执行顺序，主要分为6个阶段，具体流程图如下：<br>
> {% qnimg JS/macro-queue.png title:queue alt:macro-queue示意图 extend:?imageView2/2/w/600 %}
> 1. *Timer*: 事件循环的起始阶段，处理定时器到期之后的 setTimeout 和 setInterval 的回调函数；
> 2. *I/O callbacks*: 处理除了 setTimeout, setInterval 和 setImmediate 之外的所有回调函数，它没有close callbacks； 
> 3. *Idle, prepare*: Node 内部使用. 
> 4. *Poll*: 整个事件循环中最重要的阶段，此阶段接受新的传入连接（新的套接字建立等）和数据（文件读取等） 
>       * 如果watch_queue（连接到轮询阶段的队列）中存在某些内容，它们将一个接一个地同步执行，直到队列为空或达到系统特定的最大限制为止。
>       * 一旦队列为空，节点将尝试等待新连接等。等待或睡眠的时间取决于各种因素;
> 5. *Check*: 一个专门用于处理setImmediate()回调的阶段；
> 6. *Close callbacks*: 这里处理关闭类型的回调（例如：socket.on（'close'，（）=> {}））。更像是一个清理阶段； 

> * *nextTickQueue & microTaskQueue*: nextTickQueue 用于保存 process.nextTick()的回调函数; microTaskQueue 用于保存Promise.then()的回调函数，这两个都不属于主线程事件循环的一部分，在以上六个阶段的任意时刻发现有这两个队列不为空，都会在当前同步程序执行完成之后尽快调用它们。


## 回调优先级对比
> **`process.nextTick > promise.then > setTimeout/setInterval/setImmediate`**

*注意：`setImmediate` 每次都添加在`Macro-TaskQueue`的`Check`观察者部分，而`setTimeout`是添加在`Macro-TaskQueue`的`Timer`观察者部分，当二者同时在主线程中出现时，执行顺序不确定（因为`setTimeout`延迟不确定），但是当二者是同时出现在别的异步回调函数当中时，`setImmediate` 会先于 `setTimout` 执行。例如：*

```javascript
// 例一：
setImmediate(function () {
    setImmediate(function A() {
        console.log(1);
        setImmediate(function B() {
            console.log(2);
        });
    });

    setTimeout(function timeout() {
        console.log('TIMEOUT FIRED');
    }, 0);
});
// 输出顺序始终如下：
// 1
// TIMEOUT FIRED
// 2
```
```javascript
// 例二：
setImmediate(function A() {
    console.log(1);
    setImmediate(function B() {
        console.log(2);
    });
});

setTimeout(function timeout() {
    console.log('TIMEOUT FIRED');
}, 0);
// 1和TIMEOUT FIRED输出顺序不确定，2始终是最后输出的。
```