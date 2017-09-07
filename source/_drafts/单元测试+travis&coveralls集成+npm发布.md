---
title: 单元测试+travis&coveralls集成+npm发布
categories:
  - JavaScript
date: 2017-09-07 10:51:59
tags:
  - mocha
  - istanbul
  - travis
  - coveralls
  - npm
---

在[`codewars`](https://www.codewars.com) 刷到了很有意思的一道题，题目给出了篱笆密码法的介绍，让你根据介绍写出对应的`encode`和`decode`方法。
刷过之后感觉非常满足，为了充实下自己的`github`，顺便尝试写写单元测试，我决定整理下将code发布到`npm`。

 晚上九点发布之后，第二天早上来看，发现被下载了151次！！有点小惊喜，完全不知道发生了什么，不过挺开心的，哈哈哈哈哈

[项目地址  欢迎fork，star](https://github.com/rjdangcc/railFenceCipher)

大致步骤如下

· code
· 单元测试
· travis
· coveralls
· npm publish

### Code
题目链接：
[Rail Fence Cipher: Encoding and Decoding](https://www.codewars.com/kata/58c5577d61aefcf3ff000081)
有兴趣的同学可以自己做做看，这里对解题思路不做分析

code准备好之后，就可以开始准备构建自己的`npm`包了

新建目录之后，
{% codeblock %}
 $ npm init
 {% endcodeblock %}
 填写对应的信息，生成`package.json`，这里可以随便填一点，毕竟后面肯定会改的。。

 之后就是构建项目目录，项目中使用`gulp`对文件已经了转译压缩等工作，最终的项目目录如下

 src 源文件
 dist `gulp`的输出文件
 test 对应的单元测试文件

{% codeblock %}
 .
 ├── gulpfile.js
 ├── index.js
 ├── node_modules
 ├── package-lock.json
 ├── package.json
 ├── dist
 │   ├── index.js
 │   └── railFenceCipher.min.js
 ├── src
 │   └── index.js
 └── test
     └── index.test.js

{% endcodeblock %}

### 单元测试

这里使用`mocha`做单元测试，`Istanbul` 做覆盖率测试

#### mocha

##### 安装
简单介绍下`mocha`的使用，首先就是安装`mocha` ，就是`npm`那一套，要注意的是为了操作的方便，请在全面环境也安装一下`mocha`。

{% codeblock %}
 npm install --global mocha
{% endcodeblock %}

 通常，测试脚本与所要测试的源码脚本同名，但是后缀名为`.test.js`（表示测试）或者`.spec.js`（表示规格），统一放在`test`文件夹下，每个`src`的文件，都有一个`**.test.js`与之对应

##### 写法
 每个`**.test.js`文件中都应该包括一个或多个`describe`块，每个`describe`中应该有一个或者多个`it`块

 `describe`块称为"测试套件"（test suite），表示一组相关的测试。它是一个函数，第一个参数是测试套件的名称，第二个参数是一个实际执行的函数。
 `it`块称为"测试用例"（test case），表示一个单独的测试，是测试的最小单位。它也是一个函数，第一个参数是测试用例的名称，第二个参数是一个实际执行的函数。

 如何分组是个人习惯问题，也可以参考大型的开源项目中单元测试的写法，更有指导意义

{% codeblock lang:javascript %}
var chai = require('chai')
var railfencecipher = require('../src/index.js')
var assert = chai.assert;

describe('rail fence cipher', function () {
    it('encode', function () {
        var ans = railfencecipher.encodeRailFenceCipher('WEAREDISCOVEREDFLEEATONCE', 3);
        assert.equal(ans, 'WECRLTEERDSOEEFEAOCAIVDEN');
    });
    it('decode', function () {
        var ans = railfencecipher.decodeRailFenceCipher('WECRLTEERDSOEEFEAOCAIVDEN', 3);
        assert.equal(ans, 'WEAREDISCOVEREDFLEEATONCE');
    });
});

{% endcodeblock %}

##### 断言库

所谓"断言"，就是判断源码的实际执行结果与预期结果是否一致，上面代码中的 `assert.equal` 就是用来判断函数输出的结果是否正确。
`nodejs`本身有自带的断言库`assert`，这里我们使用了`chai`的断言库，所以在使用前要
{% codeblock %}
npm install chai --save-dev
{% endcodeblock %}
##### 运行

写好单元测试脚本之后，我们就可以来运行它了
{% codeblock %}
$ mocha test/index.test.js
{% endcodeblock %}
`mocha`默认运行test文件夹下的文件，因此当你执行

{% codeblock %}
$ mocha
{% endcodeblock %}
命令时，会默认将`test`目录下所有的测试脚本运行一遍

当`test`文件夹下有目录嵌套时，需要在命令后加上 --recursive 参数，这样`test`目录下所有子文件夹中的测试用例都会被执行

{% codeblock %}
$ mocha --recursive
{% endcodeblock %}

我们项目中只有一个测试脚本，因此运行之后可以看到所有的测试用例都通过了
{% codeblock %}
$ mocha
{% endcodeblock %}


{% asset_img mochatest.jpg mochatest %}

[查看更多 mocha 的用法](https://mochajs.org/)

#### Istanbul

通过mocha进行单元测试的时候，我们常常关心我们写的测试用例是否覆盖到了代码的边边角角各种情况，这时候我们可以用Istanbul来对测试覆盖率进行检测

· 行覆盖率（line coverage）：是否每一行都执行了？
· 函数覆盖率（function coverage）：是否每个函数都调用了？
· 分支覆盖率（branch coverage）：是否每个if代码块都执行了？
· 语句覆盖率（statement coverage）：是否每个语句都执行了？

##### 安装
{% codeblock %}
$ npm install istanbul --save-dev
{% endcodeblock %}

##### 使用
我们在使用`Istanbul`的时候，不需要写额外的代码。由于我们的测试框架使用了`mocha`，因此我们只需要执行

{% codeblock %}
$ istanbul cover _mocha
{% endcodeblock %}

就可以拿到我们测试覆盖率的结果，命令会将所有的测试用例运行一遍我们可以看到terminal中的返回结果

{% asset_img istanbultest.jpg istanbultest %}

运行之后会生成一个`coverage`文件夹，`lcov-report` 目录下有一个`index.html`，在浏览器中打开后会告诉你测试的结果，如果测试覆盖率没有打到100%，会将没有测试到的代码标出

{% asset_img coveragehtml.jpg coveragehtml %}

[查看更多 istanbul 的用法](https://github.com/gotwarlost/istanbul)

### travis-ci

我们经常在开源项目中看到这个badge，感觉非常高大上，为了让自己的`npm`包看起来更高端，更可靠，我们也来配置一下

 [![build](https://travis-ci.org/rjdangcc/railFenceCipher.svg?branch=1.1.5)]()

 通常我们都会使用`Travis-CI`
 `Travis-CI`是在线的，分布式的持续集成服务，它会同步你在`GitHub`上托管的项目，每当你Commit Push之后，就会在几分钟内开始按照你的要求测试部署你的项目。

 #### 在项目中配置

 首先要在项目的根目录下新建一个`.travis.yml`文件，加入项目的基本信息，具体写法可以[看这里](http://docs.travis-ci.com/user/getting-started/)

 `nodejs`的项目配置项很简单，如下图

{% codeblock %}
 language: node_js
 node_js:
   - "v6.9.1"
 npm:
   - "5.3.0"
{% endcodeblock %}

 #### 登陆`travis`

 使用`github`账号登陆`travis`，将需要持续集成的项目(repo)勾选进去

 这样再次`push`的时候，`travis`就会自动运行测试脚本，测试通过之后，我们就可以看到上面提到的`badge`了

{% asset_img travispage.jpg travispage %}

 #### 配置badge到项目的`README.md`

 点击这个badge，复制弹窗里的链接，在`README.md`中使用对应的markdown语句标记后，就可以看到了

 我的项目中，对应的markdown长这样

 {% codeblock %}
 [![build](https://travis-ci.org/rjdangcc/railFenceCipher.svg?branch=1.1.5)]()
 {% endcodeblock %}

 ### coveralls

 除了[![build](https://travis-ci.org/rjdangcc/railFenceCipher.svg?branch=1.1.5)]()外，我们还可以添加测试覆盖率的badge，给人的感觉就是靠谱

 我们可以使用`coveralls`的服务,打开 [`coveralls`](https://coveralls.io/)官网并登陆，将项目勾选上

 #### 安装

 之前我们已经安装了`Istanbul`，这里只要安装`coveralls`，以及配合`mocha`使用的插件

 {% codeblock %}
 $ npm install --save-dev coveralls mocha-lcov-reporter
 {% endcodeblock %}

 #### 配置

 此外，我们需要在`package.json`中，将 `npm test` 的内容修改一下

  {% codeblock %}
  istanbul cover ./node_modules/mocha/bin/_mocha --report lcovonly -- -R spec && cat ./coverage/lcov.info | ./node_modules/coveralls/bin/coveralls.js && rm -rf ./coverage
  {% endcodeblock %}

  配置好之后，在本地运行`npm test`会报错，建议在本地执行

  {% codeblock %}
  $ istanbul cover _mocha
  {% endcodeblock %}

  之所以要配置`npm test`，是因为`git push`之后 `travis` 会执行 `npm test` 命令，会在测试结束之后将对应的覆盖率信息通知到`coveralls`，`coveralls`来更新的我们项目的 coverage badge

 {% asset_img travistest.jpg travistest %}

 #### 复制badge链接

 在`push`之后，登陆[`coveralls`](https://coveralls.io/)，进入对应的项目，拉到最下面 点击`EMBED`，复制markdown里的代码，直接粘贴到`README.md`中，就可以展示你最新代码的测试覆盖率badge了

 {% asset_img covbadge.jpeg covbadge %}

 这样我们基本的准备工作就完成了，接下来就要准备`npm publish`了

 ### npm publish

 在本地测试无误之后，修改`package.json`中的相应信息，尽量把各类信息补充完整
 在每次 `publish` 前都要记得修改 `version` , 假设我们这次更新后的版本号为 `2.0.0`

 依次执行

  {% codeblock %}
  $ git tag 2.0.0
  $ git push --tag
  {% endcodeblock %}

 #### 注册 npm

 登陆npm官网进行注册，注册之后要验证邮箱，否则无法成功 `publish`

 注册成功后在本地登陆`npm`，这里要注意如果你之前修改的`npm`源的地址，一定要切换回`npm`官方的源地址再进行操作

 #### 登陆&发布

 {% codeblock %}
 $ npm login
 {% endcodeblock %}

 输入你的`Username`以及`password`，邮箱
 {% codeblock %}
 $ npm publish
 {% endcodeblock %}

 发布成功后，在`npm`官网上就可以搜到你刚刚发布的`npm`包了, 项目的简介就是`README.md`中的内容

 ### 参考

 [mochajs](https://mochajs.org/)
 [istanbul](https://github.com/gotwarlost/istanbul)
 [travis-ci](https://travis-ci.org/)
 [coveralls](https://coveralls.io/)
 [测试框架 Mocha 实例教程](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html)
 [代码覆盖率工具 Istanbul 入门教程](http://www.ruanyifeng.com/blog/2015/06/istanbul.html)
 [JS 单元测试代码覆盖率和持续集成](http://csbun.github.io/blog/2015/07/js-unit-test-coverage-ci/)

