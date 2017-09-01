---
title: Promise入门
id: 23
categories:
  - 前端
  - 技术
date: 2017-04-19 10:43:08
tags:
---

# `Promise`的含义

`Promise`是异步编程的一种解决方案
一个 `Promise`有以下几种状态:

*   `pending`: 初始状态，未履行或拒绝。
*   `fulfilled`: 意味着操作成功完成。
*   `rejected`: 意味着操作失败。
`pending` 状态的`Promise` 对象可能以`fulfilled`状态返回了一个值，也可能被某种理由（异常信息）拒绝（`reject`）了。当其中任一种情况出现时，`Promise` 对象的 `then` 方法绑定的处理方法（`handlers` ）就会被调用（`then`方法包含两个参数：`onfulfilled` 和 `onrejected`，它们都是 `Function` 类型。当值被填充时，调用 `then` 的 `onfulfilled` 方法，当`Promise`被拒绝时，调用 `then` 的 `onrejected` 方法， 所以在异步操作的完成和绑定处理方法之间不存在竞争）。

### 优点

`Promise`比起传统的异步实现方式——回调和事件，更加合理和强大。它由社区最早提出和实现，`ES6`将其写进了语言标准，统一了用法。

### 特点

*   对象的状态不受外界影响，`Promise`对象代表一个异步操作，包含三种存在状态，只有异步操作的结果可以决定当前是哪一种状态。这也是`Promise`这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。
*   一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：从`Pending`变为`fulfilled`和从`Pending`变为`Rejected`。再次给promise添加回调函数也会立即得到这个结果。

# 方法

## `1.Promise.resolve()`

<span style="font-family: Monaco, Consolas, 'Andale Mono', 'DejaVu Sans Mono', monospace;"><span style="font-size: 13px;">resolve</span></span> 函数将`promise` 对象的状态从 未完成 变为‘成功’，（`Pending`——`fulfilled`）,在异步操作成功的同时，将异步操作的结果作为参数传递出去；

## `2.Promise.reject()`

`reject`函数将`promise` 对象的状态从 未完成 变为‘失败’，（`Pending`——`reject`）,在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

## `3.Promise.all(iterable)`

该对象返回一个新的`promise`对象，只有该对象在`iterable`里的所有`promise`对象都成功时，才会返回成功，任何一个`iterable`里面的`promise`对象失败，则立即出发`promise`对象的失败。这个新的`promise`对象在触发成功状态以后，会把一个包含`iterable`里，所有`promise`返回值的数组作为成功回调的返回值，顺序跟`iterable`中的顺序一致，如果新的`promise`对象触发了失败状态，它会把第一个触发失败对象的错误信息作为它的失败错误信息。

## `4.Promise.race(iterable)`

当`iterable`参数里的任意一个子`promise`被成功或失败后，父`promise`马上也会用子`promise`的成功返回值或失败详情作为参数调用父`promise`绑定的相应句柄，并返回该`promise`对象。可以形容为竞速，更容易理解。

## `5.Promise.prototype.then()`

`Promise`实例具有`then`方法，它的作用是为`Promise`实例添加状态改变时的回调函数，`then`方法的第一个参数是`Resolved`状态的回调函数，第二个参数（可选）是`Rejected`状态的回调函数。

`then`方法返回的是一个新的`Promise`实例，不是原来的，因此可以采用链式写法

    <span class="token function">getJSON<span class="token punctuation">(</span></span><span class="token string">"/posts.json"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>json<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> json<span class="token punctuation">.</span>post<span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>post<span class="token punctuation">)</span> <span class="token punctuation">{</span>
     <span class="token comment" spellcheck="true"> // ...
    </span><span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>`</pre>
    上面的代码使用`then`方法，依次指定了两个回调函数。第一个回调函数完成以后，会将返回结果作为参数，传入第二个回调函数。

    采用链式的`then`，可以指定一组按照次序调用的回调函数。这时，前一个回调函数，有可能返回的还是一个`Promise`对象（即有异步操作），这时后一个回调函数，就会等待该`Promise`对象的状态发生变化，才会被调用。
    <pre class=" language-javascript">`<span class="token function">getJSON<span class="token punctuation">(</span></span><span class="token string">"/post/1.json"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>post<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token function">getJSON<span class="token punctuation">(</span></span>post<span class="token punctuation">.</span>commentURL<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span> <span class="token function">funcA<span class="token punctuation">(</span></span>comments<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">"Resolved: "</span><span class="token punctuation">,</span> comments<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token function">funcB<span class="token punctuation">(</span></span>err<span class="token punctuation">)</span><span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">"Rejected: "</span><span class="token punctuation">,</span> err<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;

    </span>`</pre>
    上面代码中，第一个`then`方法指定的回调函数，返回的是另一个`Promise`对象。这时，第二个`then`方法指定的回调函数，就会等待这个新的`Promise`对象状态发生变化。如果变为`Resolved`，就调用`funcA`，如果状态变为Rejected，就调用`funcB`。

    ## `6.Promise.prototype.catch()`

    `Promise.prototype.catch`方法是`.then(null, rejection)`的别名，用于指定发生错误时的回调函数。
    <pre class=" language-javascript">`<span class="token function">getJSON<span class="token punctuation">(</span></span><span class="token string">'/posts.json'</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>posts<span class="token punctuation">)</span> <span class="token punctuation">{</span>
     <span class="token comment" spellcheck="true"> // ...
    </span><span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
     <span class="token comment" spellcheck="true"> // 处理 getJSON 和 前一个回调函数运行时发生的错误
    </span>  console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'发生错误！'</span><span class="token punctuation">,</span> error<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    `</pre>
    上面代码中，`getJSON`方法返回一个 Promise 对象，如果该对象状态变为`Resolved`，则会调用`then`方法指定的回调函数；如果异步操作抛出错误，状态就会变为`Rejected`，就会调用`catch`方法指定的回调函数，处理这个错误。另外，`then`方法指定的回调函数，如果运行中抛出错误，也会被`catch`方法捕获。
    <pre class=" language-javascript">`p<span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token punctuation">(</span>val<span class="token punctuation">)</span> <span class="token operator">=</span><span class="token operator">&gt;</span> console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'fulfilled:'</span><span class="token punctuation">,</span> val<span class="token punctuation">)</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span> <span class="token operator">=</span><span class="token operator">&gt;</span> console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'rejected'</span><span class="token punctuation">,</span> err<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment" spellcheck="true">
    // 等同于
    </span>p<span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token punctuation">(</span>val<span class="token punctuation">)</span> <span class="token operator">=</span><span class="token operator">&gt;</span> console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'fulfilled:'</span><span class="token punctuation">,</span> val<span class="token punctuation">)</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token punctuation">(</span>err<span class="token punctuation">)</span> <span class="token operator">=</span><span class="token operator">&gt;</span> console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">"rejected:"</span><span class="token punctuation">,</span> err<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    `</pre>
    下面是一个例子。
    <pre class=" language-javascript">`<span class="token keyword">var</span> promise <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">Error</span><span class="token punctuation">(</span><span class="token string">'test'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    promise<span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span>error<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">
    // Error: test
    </span>`</pre>
    上面代码中，`promise`抛出一个错误，就被`catch`方法指定的回调函数捕获。注意，上面的写法与下面两种写法是等价的。
    <pre class=" language-javascript">`<span class="token comment" spellcheck="true">// 写法一
    </span><span class="token keyword">var</span> promise <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">try</span> <span class="token punctuation">{</span>
        <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">Error</span><span class="token punctuation">(</span><span class="token string">'test'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token punctuation">}</span> <span class="token keyword">catch</span><span class="token punctuation">(</span>e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">reject<span class="token punctuation">(</span></span>e<span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token punctuation">}</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    promise<span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span>error<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment" spellcheck="true">
    // 写法二
    </span><span class="token keyword">var</span> promise <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token function">reject<span class="token punctuation">(</span></span><span class="token keyword">new</span> <span class="token class-name">Error</span><span class="token punctuation">(</span><span class="token string">'test'</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    promise<span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span>error<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    `</pre>
    比较上面两种写法，可以发现`reject`方法的作用，等同于抛出错误。

    如果Promise状态已经变成`Resolved`，再抛出错误是无效的。
    <pre class=" language-javascript">`<span class="token keyword">var</span> promise <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token function">resolve<span class="token punctuation">(</span></span><span class="token string">'ok'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">Error</span><span class="token punctuation">(</span><span class="token string">'test'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    promise
      <span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>value<span class="token punctuation">)</span> <span class="token punctuation">{</span> console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span>value<span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span> console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span>error<span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">
    // ok
    </span>`</pre>
    上面代码中，Promise 在`resolve`语句后面，再抛出错误，不会被捕获，等于没有抛出。因为 Promise 的状态一旦改变，就永久保持该状态，不会再变了。

    Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个`catch`语句捕获。
    <pre class=" language-javascript">`<span class="token function">getJSON<span class="token punctuation">(</span></span><span class="token string">'/post/1.json'</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>post<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token function">getJSON<span class="token punctuation">(</span></span>post<span class="token punctuation">.</span>commentURL<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>comments<span class="token punctuation">)</span> <span class="token punctuation">{</span>
     <span class="token comment" spellcheck="true"> // some code
    </span><span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
     <span class="token comment" spellcheck="true"> // 处理前面三个Promise产生的错误
    </span><span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    `</pre>
    上面代码中，一共有三个Promise对象：一个由`getJSON`产生，两个由`then`产生。它们之中任何一个抛出的错误，都会被最后一个`catch`捕获。

    一般来说，不要在`then`方法里面定义Reject状态的回调函数（即`then`的第二个参数），总是使用`catch`方法。
    <pre class=" language-javascript">`<span class="token comment" spellcheck="true">// bad
    </span>promise
      <span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>data<span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token comment" spellcheck="true"> // success
    </span>  <span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token keyword">function</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token comment" spellcheck="true"> // error
    </span>  <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment" spellcheck="true">
    // good
    </span>promise
      <span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>data<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token comment" spellcheck="true"> //cb
    </span>   <span class="token comment" spellcheck="true"> // success
    </span>  <span class="token punctuation">}</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token comment" spellcheck="true"> // error
    </span>  <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    `</pre>
    上面代码中，第二种写法要好于第一种写法，理由是第二种写法可以捕获前面`then`方法执行中的错误，也更接近同步的写法（`try/catch`）。因此，建议总是使用`catch`方法，而不使用`then`方法的第二个参数。

    跟传统的`try/catch`代码块不同的是，如果没有使用`catch`方法指定错误处理的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应。
    <pre class=" language-javascript">`<span class="token keyword">var</span> someAsyncThing <span class="token operator">=</span> <span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token comment" spellcheck="true"> // 下面一行会报错，因为x没有声明
    </span>    <span class="token function">resolve<span class="token punctuation">(</span></span>x <span class="token operator">+</span> <span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>

    <span class="token function">someAsyncThing<span class="token punctuation">(</span></span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'everything is great'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    `</pre>
    上面代码中，`someAsyncThing`函数产生的Promise对象会报错，但是由于没有指定`catch`方法，这个错误不会被捕获，也不会传递到外层代码，导致运行后没有任何输出。注意，Chrome浏览器不遵守这条规定，它会抛出错误“ReferenceError: x is not defined”。
    <pre class=" language-javascript">`<span class="token keyword">var</span> promise <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token function">resolve<span class="token punctuation">(</span></span><span class="token string">'ok'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token function">setTimeout<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">Error</span><span class="token punctuation">(</span><span class="token string">'test'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token number">0</span><span class="token punctuation">)</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    promise<span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span>value<span class="token punctuation">)</span> <span class="token punctuation">{</span> console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span>value<span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">
    // ok
    </span><span class="token comment" spellcheck="true">// Uncaught Error: test
    </span>`</pre>
    上面代码中，Promise 指定在下一轮“事件循环”再抛出错误，结果由于没有指定使用`try...catch`语句，就冒泡到最外层，成了未捕获的错误。因为此时，Promise的函数体已经运行结束了，所以这个错误是在Promise函数体外抛出的。

    Node 有一个`unhandledRejection`事件，专门监听未捕获的`reject`错误。
    <pre class=" language-javascript">`process<span class="token punctuation">.</span><span class="token function">on<span class="token punctuation">(</span></span><span class="token string">'unhandledRejection'</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span>err<span class="token punctuation">,</span> p<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">error<span class="token punctuation">(</span></span>err<span class="token punctuation">.</span>stack<span class="token punctuation">)</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    `</pre>
    上面代码中，`unhandledRejection`事件的监听函数有两个参数，第一个是错误对象，第二个是报错的Promise实例，它可以用来了解发生错误的环境信息。。

    需要注意的是，`catch`方法返回的还是一个 Promise 对象，因此后面还可以接着调用`then`方法。
    <pre class=" language-javascript">`<span class="token keyword">var</span> someAsyncThing <span class="token operator">=</span> <span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token comment" spellcheck="true"> // 下面一行会报错，因为x没有声明
    </span>    <span class="token function">resolve<span class="token punctuation">(</span></span>x <span class="token operator">+</span> <span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>

    <span class="token function">someAsyncThing<span class="token punctuation">(</span></span><span class="token punctuation">)</span>
    <span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'oh no'</span><span class="token punctuation">,</span> error<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span>
    <span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'carry on'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">
    // oh no [ReferenceError: x is not defined]
    </span><span class="token comment" spellcheck="true">// carry on
    </span>`</pre>
    上面代码运行完`catch`方法指定的回调函数，会接着运行后面那个`then`方法指定的回调函数。如果没有报错，则会跳过`catch`方法。
    <pre class=" language-javascript">`Promise<span class="token punctuation">.</span><span class="token function">resolve<span class="token punctuation">(</span></span><span class="token punctuation">)</span>
    <span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'oh no'</span><span class="token punctuation">,</span> error<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span>
    <span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'carry on'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">
    // carry on
    </span>`</pre>
    上面的代码因为没有报错，跳过了`catch`方法，直接执行后面的`then`方法。此时，要是`then`方法里面报错，就与前面的`catch`无关了。

    `catch`方法之中，还能再抛出错误。
    <pre class=" language-javascript">`<span class="token keyword">var</span> someAsyncThing <span class="token operator">=</span> <span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token comment" spellcheck="true"> // 下面一行会报错，因为x没有声明
    </span>    <span class="token function">resolve<span class="token punctuation">(</span></span>x <span class="token operator">+</span> <span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>

    <span class="token function">someAsyncThing<span class="token punctuation">(</span></span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token function">someOtherAsyncThing<span class="token punctuation">(</span></span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'oh no'</span><span class="token punctuation">,</span> error<span class="token punctuation">)</span><span class="token punctuation">;</span>
     <span class="token comment" spellcheck="true"> // 下面一行会报错，因为y没有声明
    </span>  y <span class="token operator">+</span> <span class="token number">2</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'carry on'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">
    // oh no [ReferenceError: x is not defined]
    </span>`</pre>
    上面代码中，`catch`方法抛出一个错误，因为后面没有别的`catch`方法了，导致这个错误不会被捕获，也不会传递到外层。如果改写一下，结果就不一样了。
    <pre class=" language-javascript">`<span class="token function">someAsyncThing<span class="token punctuation">(</span></span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then<span class="token punctuation">(</span></span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token function">someOtherAsyncThing<span class="token punctuation">(</span></span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'oh no'</span><span class="token punctuation">,</span> error<span class="token punctuation">)</span><span class="token punctuation">;</span>
     <span class="token comment" spellcheck="true"> // 下面一行会报错，因为y没有声明
    </span>  y <span class="token operator">+</span> <span class="token number">2</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>error<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      console<span class="token punctuation">.</span><span class="token function">log<span class="token punctuation">(</span></span><span class="token string">'carry on'</span><span class="token punctuation">,</span> error<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment" spellcheck="true">
    // oh no [ReferenceError: x is not defined]
    </span><span class="token comment" spellcheck="true">// carry on [ReferenceError: y is not defined]
    </span>`</pre>
    上面代码中，第二个`catch`方法用来捕获，前一个`catch`方法抛出的错误。

    ## 示例

    <pre class="brush: js line-numbers language-js">`<span class="token keyword">var</span> myFirstPromise <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Promise</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>resolve<span class="token punctuation">,</span> reject<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token comment" spellcheck="true">//当异步代码执行成功时，我们才会调用resolve(...), 当异步代码失败时就会调用reject(...)</span>
        <span class="token comment" spellcheck="true">//在本例中，我们使用setTimeout(...)来模拟异步代码，实际编码时可能是XHR请求或是HTML5的一些API方法.</span>
        <span class="token function">setTimeout</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
            <span class="token function">resolve</span><span class="token punctuation">(</span><span class="token string">"成功!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment" spellcheck="true">//代码正常执行！</span>
        <span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token number">250</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    myFirstPromise<span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>successMessage<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token comment" spellcheck="true">//successMessage的值是上面调用resolve(...)方法传入的值.</span>
        <span class="token comment" spellcheck="true">//successMessage参数不一定非要是字符串类型，这里只是举个例子</span>
        console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"Yay! "</span> <span class="token operator">+</span> successMessage<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

**_参考：_**
_[ECMAScript 6 入门 阮一峰](http://es6.ruanyifeng.com/#docs/promise)_
_ [developer.mozilla.org](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)_
_ [Developer Network](https://msdn.microsoft.com/zh-cn/library/dn802833(v=vs.94).aspx)_