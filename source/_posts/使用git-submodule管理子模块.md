---
title: 使用git submodule管理子模块
date: 2018-01-11 14:36:42
tags: git submodule tags 
---
### 前言
最近在为团队开发一套具有一致接口、模块化、高性能的 `JavaScript` 工具库，可以在前端开发，node开发中通用。主要包含常用的工具函数，比如`url parse,number pad,cookie get/set，localStorage get/set`等等。

我们希望这套工具库作为第三方的模块，独立于业务代码，避免在进行业务开发时，直接在目录下进行修改，导致在不同项目中统一方法有不同的表现形式。
最常见的实现方式就是将这套代码，发布为一个`npm`包，通过`npm install` 的方式引入到各项目中，再通过`npm update`的方式进行版本升级。
但是！！问题来了————
由于部门维护的项目中，有些年代久远，并不支持`npm module`开发，因此我们需要别的方式来完成这件事
经高人指点，  `git submodule`进入了我们的视线

### git submodule 是什么
{% quote %}
`git Submodule` 是一个很好的多项目使用共同类库的工具，他允许类库项目做为`repository`,子项目做为一个单独的`git`项目存在父项目中，子项目可以有自己的独立的`commit`，`push`，`pull`。而父项目以`Submodule`的形式包含子项目，父项目可以指定子项目`header`，父项目中会的提交信息包含`Submodule`的信息，再`clone`父项目的时候可以把`Submodule`初始化。
{% endquote %}

### 在项目中使用Submodule

使用`git`命令可以直接添加`Submodule`：

{% quote %}
git submodule add git@gitlab.corp.qunar.com:yuancheng.yuan/aura.git aura
{% endquote %}

使用`git status`命令可以看到
```
        On branch master
        Changes to be committed:
            **new file:   .gitmodules**
            **new file:   aura**
```

可以看到多了两个待提交的文件,一个是我们子模块的文件夹`aura`
`.gitmodules`中包含了`submodule`的主要信息，指定`reposirory`,指定路径:

```
    [submodule "aura"]
    	path = aura
    	url = git@gitlab.corp.qunar.com:yuancheng.yuan/aura.git
```

我们对以上两个项目进行保存提交

```
git add .
git commit -m "add submodule"
git push
```

接下来我们去git页面上去查看发现，我们的子模块目录后面跟了一传字符，这个是每次git commit之后保存的一个sha-1值，也就是子模块的HEAD指针指向的提交历史
{% asset_img 2.jpeg  %}

### 如何做版本控制
既然用了`git submodule`来管理子模块，那么自然而然的会想到用`git tag`来管理子模块的版本

首先在业务代码中引入的子模块，是不允许进行修改的，在子模块每次升级之后，都会打一个`tag`，每一个`tag`对应一次`commit`的提交记录。
父项目的git并不会记录`Submodule`的文件变动，它是按照`commit`指定`Submodule`的`git header`。

当我们需要更新项目中的子模块版本时，只需要进入到子模块的目录下执行
{% quote %}
git checkout version-tag
{% endquote %}
再回到 项目目录下提交刚刚的修改，这时候父项目记录的子模块的`sha-1`值就会变成 `version-tag`对应的`sha-1`值

#### 举个🌰

首先我们有个项目 `myproject`,包含了一个子模块 `aura`，在`gitlab`中可以看到 `myproject` 引用的子模块的`sha-1` 值为 `f62bae98d06`
{% asset_img 1.jpeg  %}

在终端中，我们切换了子模块的`tag(版本)`
{% asset_img 3.jpeg  %}

返回到父目录中，因为子模块对`header`变了， 因此需要重新提交
{% asset_img 4.jpeg  %}

最后我们再回`gitlab`，发现 `myproject` 中保存的子模块的`header`值也发生了变化 `3f341f62773`
{% asset_img 5.jpeg  %}


### clone 一个带有子模块的项目
clone Submodule有两种方式 一种是采用递归的方式clone整个项目，一种是clone父项目，再更新子项目。

* 采用递归参数 --recursive

{% quote %}
git clone git@gitlab.corp.qunar.com:yuancheng.yuan/myproject.git --recursive
{% endquote %}

* 第二种方法先clone父项目，再初始化Submodule
```
git clone git@gitlab.corp.qunar.com:yuancheng.yuan/myproject.git
cd pod-myproject
git submodule init
git submodule update
```

### 删除项目中的子模块
```
git rm --cached aura
rm -rf aura
rm .gitmodules
```

更改git的配置文件config:
````
vim .git/config
````
### 参考：
· {% link  Git 工具 - 子模块 https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97 %}
· {% link  Git 基础 - 打标签 https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE %}
· {% link  git命令之git tag 给当前分支打标签 http://blog.csdn.net/wangjia55/article/details/8793577 %}
· {% link  git checkout之一 HEAD基本和detached 状态 http://blog.csdn.net/csfreebird/article/details/7583363 %}
· {% link  Git权威指南 http://www.worldhello.net/gotgit/ %}
