---
title: Promise深究
id: 125
categories:
  - else
date: 2017-08-28 17:23:46
tags:
---

## Promise是什么？

一直以来都把`promise`当成es6中新增的内置对象。最近看`promise`的实现原理，发现自己对`Promise`的认知一直是错误的。

近几年随着`JavaScript`开发模式的逐渐成熟，CommonJS规范顺势而生，其中就包括提出了`Promise`规范，`Promise`完全改变了`js`异步编程的写法，让`js`异步代码更加易于理解。可以把`Promise`认为是一种设计模式，一种表现形式，不要把`Promise`看成是某些具体的方法，函数或者对象。

我们常常提到的es6中的`Promise`，就是Ecma官方提供的一种遵循`Promise`规范的实现，其他比较经典的实现还有[q](https://github.com/kriskowal/q)，[Bluebird](https://github.com/petkaantonov/bluebird)，都是`Promise A+` 标准的基础上提供了一些封装和帮助方法。

简单来说，`Promise`代表一个异步计算的最终结果。使用`Promise`最基础的方式是使用它的then方法，该方法会注册两个回调函数，一个接收`Promise`完成的最终值，一个接收`Promise`被拒绝的原因。

## Promise规范

[Promises/A](http://wiki.commonjs.org/wiki/Promises/A)

[Promises/A+](https://promisesaplus.com/)

`Promises/A+`在`Promises/A`议案的基础上，更清晰阐述了一些准则，拓展覆盖了一些事实上的行为规范，同时删除了一些不足或者有问题的部分。

`Promises/A+`规范目前只关注如何提供一个可操作的`then`方法，而关于如何创建，决议`Promise`是日后的工作。

## Promise和jQuery.defer

个人理解：`jQuery`中的`deferred`对象是`jquery`实现异步编程的一种方式，而`Promise`是另外一种异步编程实现方式，两者间并没有必然的关联，只是在实现上有些地方比较接近。