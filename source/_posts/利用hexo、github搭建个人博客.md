---
title: 利用hexo、github搭建个人博客
date: 2016-08-17 23:18:09
tags: [hexo,github]
categories: [hexo使用]
---

### 一些必要软件安装

------

利用hexo、github搭建博客，需要使用到node.js、hexo、git这几个软件。本文所使用的软件版本信息如下：

```
node: 4.4.7
hexo: 3.2.2
git:  1.9.4
```

#### node安装

选择合适的nodejs版本进行安装，附下载地址及安装方法链接：

[nodejs下载](https://nodejs.org/en/download/)

[安装方法](http://www.runoob.com/nodejs/nodejs-install-setup.html)

#### git安装

git简单入门地址：[廖雪峰git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)，[runoob教程](http://www.runoob.com/git/git-install-setup.html)

git下载地址：[win下载](https://git-scm.com/download/win)

#### [安装hexo](https://hexo.io/zh-cn/docs/)

```shell
### 安装hexo
npm install -g hexo-cli
### 初始化blog
hexo init <folder>
cd <folder>
npm install
### 生成静态文件
hexo g
### 启动服务
hexo s
```

安装hexo插件，或者hexo 2.x 升级 3.x可以参考 [Migrating from 2.x to 3.0](https://github.com/hexojs/hexo/wiki/Migrating-from-2.x-to-3.0)

#### Github Pages设置

参考这篇博文就可以了，写得太详细了。[如何利用GitHub Pages和Hexo快速搭建个人博客](http://sunwhut.com/2015/10/30/buildBlog/)

**重要提示：**我用的hexo3，这篇文章还是hexo2，_config.yml 需要修改为下面的:

```properties
deploy:
  type: git
  repo: 你的github地址，如果你设置了ssh，可以使用ssh地址
  branch: master
  message: hexo deploy
```



#### 结束语

自己写果然还是很费劲，这里基本上都在偷懒，从网上copy来的教程。