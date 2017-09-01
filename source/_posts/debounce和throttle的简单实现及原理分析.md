---
title: debounce和throttle的简单实现及原理分析
id: 58
categories:
  - 前端
  - 技术
date: 2017-07-03 18:35:46
tags:
---

### `debounce(func, wait)`

这是一个防抖动函数，该函数会在`wait` 毫秒之后调用`func`方法。

简单实现：

```javascript
/**
 *
 * @param fn  需要执行的函数
 * @param wait 需要延迟执行的时间
 * @returns {Function}
 */
function debounce(fn, wait) {
    var timer;

    return function () {
        var self = this
        var args = arguments

        clearTimeout(timer)

        timer = setTimeout(function () {
            fn.apply(self, args);
        }, wait)
    }
}
```

&nbsp;

这里通过闭包函数，将`timer`暂存在 `debounce` 内部，在频繁触发的事件回调中，函数每次都会执行，但是由于 `clearTimeout(timer)` 的存在，会将在延迟时间内，将未执行的回调`clear`掉，只有在等待`wait`时间之后，没有新的事件触发，才会调用`fn`。

&nbsp;

### `throttle(func,wait)`

这是一个节流函数，在 `wait` 秒内最多执行 `func` 一次的函数。

简单实现：
```javascript
/**
 *
 * @param fn 需要执行的函数
 * @param wait 节流阀的间隔时间
 * @returns {Function}
 */
function throttle(fn, wait) {
    var last = +new Date();
    var timer
    wait || (wait = 300)
    return function () {
        var self = this;
        var args = arguments;
        var now = +new Date();
        if (now &lt; last + wait) {
            clearTimeout(timer)
            timer = setTimeout(function () {
                last = now;
                fn.apply(self, args);
            }, wait)
        } else {
            last = now;
            fn.apply(self, args);
        }
    }
}

```
原理同`debounce`，利用闭包将`timer` 保存起来，每间隔一个`wait` 的时间，执行一次`fn`，在间隔时间内的回调会`clear`上一次的保留的`timer`。

`debounce`和`throttle` 函数是闭包函数使用的经典场景，理解这两个函数对深入理解闭包有很大对帮助。～

更细节的实现方法可以参考

https://github.com/lodash/lodash/

https://github.com/lishengzxc/bblog/issues/7