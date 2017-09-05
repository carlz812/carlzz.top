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

我们在日常开发中经常会用到`this`，在不同的情况下`this`的指向也有所不同，我们常会用一种意会的感觉去判断`this`的指向。以至于当遇到复杂的函数调用时，就分不清`this`的真正指向。

常见的`this`场景

· 常规函数
· 箭头函数
· 闭包函数
· 闭包箭头函数
· 构造函数

下面我们将通过两段代码来搞清楚，`this`到底指向哪里 ( 浏览器环境下 )

#### 代码一
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


##### 代码运行的结果是

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

#### 代码二


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


##### 代码运行的结果是

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

跟上一段代码相比，只有`show2`方法的输出不同，在 question1 中，`show2`打印出的结果是`window`，在question2中却是`personA`。

对比`personA`和`person1`，两者都是一个对象，唯一不同的是对`personA`来说，它是通过构造函数构造出的对象，但是直观感受上它和`person1`是一样的。

`JSON.stringify(new Person('person1')) === JSON.stringify(person1)`

之所以产生了不同的结果，说明构造函数创建的对象与直接通过字面量创建的对象是不同的，

> 使用 `new` 操作符调用构造函数，实际上会经历一下4个步骤：
> · 创建一个新对象；
> · 将构造函数的作用域赋给新对象（因此`this`就指向了这个新对象）；
> · 执行构造函数中的代码（为这个新对象添加属性）；
> · 返回新对象。

所以与字面量创建对象相比，很大一个区别是它多了构造函数的作用域。

我们在`console`中对比两者的作用域就可以看出
{% img layoutWidth /images/persons.jpg  %}

`personA`的作用域链从构造函数产生的闭包开始，而`person1`的函数作用域仅仅是`global`，于是导致`this`指向不同。
我们发现，想要真正的理解`this`，就要先知道什么是作用域，什么是闭包。

我们常常说闭包就是能够访问其他函数内部变量的函数，然而这是一种对闭包现象的描述，并不是它的本质与形成的原因。


引用红宝书的文字（便于理解，文字顺序稍微调整），来描述这几个点：

> ...每个函数都有自己的执行环境（execution context，也叫执行上下文），每个执行环境都有一个与之关联的变量对象，环境中定义的所有变量和函数都保存在这个对象中。
> ...当执行流进入一个函数时，函数的环境就会被推入一个环境栈中。当代码在环境中执行时，会创建一个作用域链，来保证对执行环境中的所有变量和函数的有序访问。函数执行之后，栈将环境弹出。
> ...函数内部定义的函数会将包含函数的活动对象添加到它的作用域链中。

具体来说，当我们 `var func = personA.show3()`时，`personA`的`show3`函数的活动对象会一致保存在`func`的作用域链中，只要不销毁`func`，那么`show3`函数的活动就会一直保存在内存中，

而构造函数同样也是闭包的机制，`personA`的`show1`方法，是构造函数的内部函数，因此执行`this.show3 = function(){ console.log(this.name)}`时，已经把构造函数的活动对象推到了`show3`函数的作用域链中。

我们再回到`this`的指向问题。我们发现，已经不能用一句话来概括它到底指向谁了，我们需要追根溯源。

红宝书中说道：

> ...`this`引用的是函数执行的环境对象（便于理解，贴上英文原版：It is a reference to the context object that the function is operating on）。
> ...每个函数被调用时都会自动获取两个特殊变量：`this`和`arguments`。内部在搜索这个两个变量时，只会搜索到其活动对象为止，永远不可能直接访问外部函数中的这两个变量。

我们看下MDN中箭头函数的概念：

> 一个箭头函数表达式的语法比一个函数表达式更短，并且不绑定自己的 `this`，`arguments`，`super`或 `new.target`。...箭头函数会捕获其所在上下文的`this`值，作为自己的`this`值。

也就是说在普通情况下，`this`指向调用函数时的对象，在全局执行的时候，就是全局对象。

箭头函数的`this`，只能通过作用域链往上层找，直到找到一个绑定了`this`的函数作用域，并指向调用该普通函数的对象。

或者从现象来描述的话，__箭头函数的this指向声明函数时，最靠近箭头函数的普通函数(假设为 f() )的`this`。但是这个`this`也会因为f调用的环境的不同而发生改变，导致这个现象的原因是这个普通函数会产生一个闭包，将它的变量对象保存在箭头函数的作用域中。__

因此`personA`的`show2`方法因为构造函数闭包的关系，指向了构造函数作用域内的`this`，而

```javascript
var func = personA.show4.call(personB)

func() // print personB
```

因为在`personA`的`show4`方法，先在`personB`环境下执行，因此`show4`方法中的箭头函数的`this`就指向了`personB`。


