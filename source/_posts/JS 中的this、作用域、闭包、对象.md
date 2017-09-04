---
title: JS 中的this、作用域、闭包、对象
categories:
  - JavaScript
date: 2017-09-04 10:25:46
tags:
  - js
  - closure
  - this
  - object
---

### 让人头疼的`this`

我们在日常开发中经常会用到`this`，在不同的情况下`this`的指向也有所不同，我们常会用一种意会的感觉去判断`this`的指向。
以至于当遇到复杂的函数调用时，就分不清`this`的真正指向。

常见的`this`场景

· 常规函数
· 箭头函数
· 闭包函数
· 闭包箭头函数
· 构造函数

下面我们将通过一段代码来搞清楚，`this`到底指向哪里 ( 浏览器环境下 )

##### 代码一
```javascript

    /**
     * Question 1
     */

    var name = 'window'

    var person1 = {
      name: 'person1',
      show1: function () {
        console.log(this.name)
      },
      show2: () => console.log(this.name),
      show3: function () {
        return function () {
          console.log(this.name)
        }
      },
      show4: function () {
        return () => console.log(this.name)
      }
    }

    var person2 = { name: 'person2' }

```
```javascript
    person1.show1()
    person1.show1.call(person2)
```
```javascript
    person1.show2()
    person1.show2.call(person2)
```
```javascript
    person1.show3()()
    person1.show3().call(person2)
    person1.show3.call(person2)()
```
```javascript
    person1.show4()()
    person1.show4().call(person2)
    person1.show4.call(person2)()
```

在上述代码中我们构造了两个对象，去分别调用四个show方法，分别对应了

· 常规函数
· 箭头函数
· 闭包函数
· 闭包箭头函数


#### 代码运行的结果是

```javascript
person1.show1() // person1
person1.show1.call(person2) // person2
```
```javascript
person1.show2() // window
person1.show2.call(person2) // window
```
```javascript
person1.show3()() // window
person1.show3().call(person2) // person2
person1.show3.call(person2)() // window
```
```javascript
person1.show4()() // person1
person1.show4().call(person2) // person1
person1.show4.call(person2)() // person2
```

 对输出对结果进行分析可以得出

 __在常规函数中__
  `person1.show1()`和 `person1.show1.call(person2)` 中的`this`，都指向了调用此方法的对象，分别为`person1`,`person2`。

 __在箭头函数中__
 `person1.show2()`和 `person1.show2.call(person2)` 中的`this` 都指向了全局，可以理解为箭头函数中的`this`，指向了外层函数的`this`。

 __在闭包函数中__
 `person1.show3()` 是一个闭包函数，分步走的话，可以写成：

 ```javascript
 var func = person3.show()

 func()
 ```

 这样的话，函数的执行环境是 `window`，但并不是`window`对象调用了它。所以说，`this`总是指向调用该函数的对象，这句话还得补充一句：在全局函数中，`this`等于`window`。

 `person1.show3().call(person2)`，分段分析一下，`person1.show3()` 返回了一个闭包函数，再通过`person2` 调用了闭包函数，执行了打印方法，因此返回了`person2`。

 `person1.show3.call(person2)()`，是先通过`person2`调用了`person1`的`show3`方法，又在全局环境中执行了`show3`方法的闭包函数，因此最后的`console`语句输出是`window`。

 __在箭头闭包函数中__
`person1.show4()()`，`person1.show4().call(person2)`都是打印`person1`。这好像又印证了那句：箭头函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象。
箭头函数中的`this`指向是无法通过改变函数本身的执行环境来改变，`call`,`apply`以及`bind`方法都不生效，`person1.show4().call(person2)` 等同于 `person1.show4()()`。

在`person1.show4.call(person2)()`中，`show4`方法本身是一个常规函数，在调用这个常规函数的时候，绑定了`person2`，因此`show4`的`this`对象指向了`person2`，因此`show4`函数中箭头的`this`也就指向了 `person2`。

##### 代码二


```javascript
/**
 * Question 2
 */
var name = 'window'

function Person (name) {
  this.name = name;
  this.show1 = function () {
    console.log(this.name)
  }
  this.show2 = () => console.log(this.name)
  this.show3 = function () {
    return function () {
      console.log(this.name)
    }
  }
  this.show4 = function () {
    return () => console.log(this.name)
  }
}

var personA = new Person('personA')
var personB = new Person('personB')
```
```javascript
personA.show1()
personA.show1.call(personB)
```
```javascript
personA.show2()
personA.show2.call(personB)
```
```javascript
personA.show3()()
personA.show3().call(personB)
personA.show3.call(personB)()
```
```javascript
personA.show4()()
personA.show4().call(personB)
personA.show4.call(personB)()
```


#### 代码运行的结果是

```javascript
personA.show1() // personA
personA.show1.call(personB) // personB
```
```javascript
personA.show2() // personA
personA.show2.call(personB) // personA
```
```javascript
personA.show3()() // window
personA.show3().call(personB) // personB
personA.show3.call(personB)() // window
```
```javascript
personA.show4()() // personA
personA.show4().call(personB) // personA
personA.show4.call(personB)() // personB
```

跟上一段代码相比，
