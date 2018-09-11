---
title: '[译]2分钟介绍Rx'
date: 2018-09-07 09:41:47
categories: 
- Frontend
- Rx
tags:
- ReactiveX
- RxJS
- Rx
---

[原文传送](https://medium.com/@andrestaltz/2-minute-introduction-to-rx-24c8ca793877)

可能你已经看过我前一段时间写过的[这篇教程](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)了。觉得太长难懂？好吧，其实 Rx不是那么难，甚至你自己也能创造它。接着看下去吧~

<!--more-->

你知道阵列 (Array) 吗？显然，答案是肯定，这就是个阵列：

>### 	*[ 14, 9, 5, 2, 10, 13, 4 ]*

如果我告诉这是一个 **不可变(immutable)** 数组，而你需要取走所有奇数，你会怎么做？
多数人会这样：

>### 	*[ 14, 9, 5, 2, 10, 13, 4 ]*
>### 	*filter( (x) -> x % 2 == 0 )*
>### 	*[ 14, 2, 10, 4 ]*

没什么新奇的，对吧？这在 `underscore.js` , `ECMAScript 5.1` , `LINQ` , `Guava` 等中很常见。它来自功能范例。


现在让我们来思考下鼠标点击事件。如果你在时间轴上绘制它们，它们看起来就像这样：
![](https://cdn-images-1.medium.com/max/1600/1*FjTqms95LbK_ztsZXiNpoQ.png)

朋友，这就是 `event steam` 中文： `事件流` ，也叫作 `Observable` 。

点击事件来自鼠标，因此整个事件流也是**不可变**的，因为在它被定义之后我们无法添加或删除。

但是如果我只对 `x<250` 的点击事件感兴趣呢？那么我们不能像通过操作数组一样来过滤并创建新的事件流吗？答案是肯定的：

>![](https://cdn-images-1.medium.com/max/1600/1*FjTqms95LbK_ztsZXiNpoQ.png)
>### *filter（（event） - > event.x <250）*
>![](https://cdn-images-1.medium.com/max/1600/1*DvH5Iqul7Nxor7r7AencgA.png)

那么 **不可变数组** 和 **Observable **之间的区别是什么？其实并没有差太多。我们可以将 `map` , `filter` , `reduce` 应用于两者。在 `Observables` 上，还可以应用 `merge` , `delay` , `concat` , `buffer`, `distinct` , `first` , `last` , `zip` , `startWith` , `window` , `takeUntil` , `skip` , `scan` , `sample` ,  `amb` , `join` , `flatMap` 。

我们只需要将其视为**异步不可变数组**。

Rx是事件的underscore.js。也许会有人问，什么是事件？你的应用程序中的大部分内容不都能成为事件吗？“应用启动”,“API响应”,“键盘按键”,“invalidateLayout()”,“设备睡眠”等等。

事实上，任何东西都可以是一系列事件。这只是一个组合和正确组合的问题。

以上2分钟的Rx介绍。

