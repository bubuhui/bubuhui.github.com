---
title: 项目maven改造问题记录
date: 2016-08-17 17:48:38
tags: [maven]
categories: [maven改造]
---

以下是记录maven改造过程中的一些问题，以及处理问题的思路。

------

```shell
Unsupported major.minor version 51.0 时抛出异常
```

#### jdk版本问题



这个提示表示当前编译jdk版本低于某些jar里面提供的jdk版本。具体定位问题，要结合上下文来看，例如下面的：

```shell
错误：服务配置文件不正确，或构造处理程序对象 javax.annotation.processing.Processor: Provider org.eclipse.sisu.space.SisuIndexAPT6 could not be instantiated: java.lang.UnsupportedClassVersionError: javax/inject/Named : Unsupported major.minor version 51.0 时抛出异常
```

这个地方问题出在 `javax.annotation.processing.Processor: Provider org.eclipse.sisu.space.SisuIndexAPT6 could not be instantiated` 这一句，`SisuIndexAPT6` 不能倍实例化，找到具体class对应jar，过滤依赖或者升级jdk来处理。

#### clipse A cycle was detected in the build path of project XXX Build Path Problem

出现这个情况，表示项目中有循环依赖，这个提示里面的xxx表示循环依赖的项目，尝试在每个包里面排除掉循环依赖关系，发现eclipse里面还是有红色的感叹号，这种情况下，只能选择手动调整编译器报错选项了，具体方法就是把循环依赖编译选项由error调整为warning，如下所示：

```
Windows -> Preferences -> Java-> Compiler -> Building -> Circular Dependencies
```

[参考文章]: http://stackoverflow.com/questions/1084866/a-cycle-was-detected-in-the-build-path-of-project-xxx-build-path-problem