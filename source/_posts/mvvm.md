---
title: MVVM
tags:
  - mvvm
id: 17
categories:
  - 前端
  - 技术
date: 2017-04-14 16:49:33
---

### 由头

`MVVM`火起来有一段时间了，但是一直对 `MVVM`一知半解，趁着最近工作不忙，研究了一下`MVVM`的实现方式，在此对知识点做一些梳理，加深理解。

注：本文不适合作为入门教程

### 什么是`MVVM`

![](http://www.carlz.top/wp-content/uploads/2017/04/Mvvm3.png)

随着业务发展，交互引起的页面 化越来越难以手动管理。如果我们把所有的状态都集中 一个对象 `Model` 中，用户的每次操作`(ViewModel)`，都会直接修改 `Model`对象，并自动的更新到 `View`，这种框架就是 `MVVM`。

`Model` 层和View层是完全独立的，`Model`的改变通过`ViewModel`渲染到`View`层，`View`层的交互操作，通过`ViewModel`来改变`Model。`

### Mvvm 核心思想

`MVVM`的核心是当`Model`发生改变时，修改`View`，也就是监听`Model`的变化。这里介绍一下Vue.js中的做法：数据劫持。

##### 数据劫持

```javascript
let bind = {
    title: 'data bind',
    content: 'qaq',
    class: 'vue'
}
bind.title = 'Madrid'

```
在上述代码中 `bind.title = 'Madrid'` 修改了 `title` 的值`bind.title = 'Madrid'`在语言规范中实际上是实现了`[[Set]]`操作，如果我们使用 `Object.defineProperty` 方法对`title` 中的 `[[Set]]` 方法进行重写，就可以达到监听数据变动的目的。
```javascript
Object.defineProperty(
    bind,
    'title',
    {
        get: () => old,
        set: (now)  => {
            console.log('title属性被修改')
            old = now;
        }
    }
)
```
个人认为，这里是Vue最精髓的地方

除了数据劫持之外，常见的方法还有脏值检查，发布-订阅者模式等等

### MVVM框架整体流程

*   解析模板
*   解析数据
*   绑定模板与数据

#### 解析模板

将html模板中的变量替换成数据，然后渲染页面视图，并将每个指令对应的节点绑定到监听器上，一旦数据被监听到被修改，立即更新视图。

#### 解析数据

即对数据进行数据劫持，重写`[[set]]`方法

#### 绑定模板与数据

在解析模板的过程中，遍历节点树，对不同的节点绑定不同的回调函数。

**_参考：_**

##### _[250行实现一个简单的MVVM](https://lovin0730.github.io/2016/12/19/simple-mvvm/) _

##### _[剖析Vue原理&amp;实现双向绑定MVVM](https://segmentfault.com/a/1190000006599500)_