---
title: 64位 windows下注册nginx、tomcat、mysql为服务
date: 2016-08-24 21:18:08
tags: [nginx,tomcat,windows]
categories: [windows软件使用]
---

### 注册解压版tomcat为服务，下面是摘录网上的2个例子。

1. [windows下将解压缩版的tomcat设置为自动运行的系统服务](http://www.cnblogs.com/samcn/archive/2011/02/18/1957599.html)
2. [在windows下如何将Tomcat设置为自动启动的服务](http://jingyan.baidu.com/article/b2c186c89f5127c46ef6ff08.html)

附注第一个例子使用方法：

1. 在DOS窗口中进入到tomcat的解压目录下，在进入到bin目录；
2. 运行 service install tomcat就安装了tomcat服务，该命令就是运行了bin目录下的service.bat脚本，tomcat是服务的名称；
3. 第二步只是安装了服务，并没有设置服务为开机启动，这里要设置一下，在命令行中输入：sc config tomcat start= auto，start= 和auto之间要有空格。
4. 为tomcat 添加依赖服务：sc config "tomcat" depend= "MySql"

### 注册mysql为service服务

使用管理员权限安装

```shell
mysqld install MySQL --defaults-file="D:\program files\mysql\bin\my.ini"
```

### 注册nginx为service服务

下面介绍几种方法

1. 使用winsw，网上也有很多例子，例如 [nginx创建windows服务自启动](http://www.cnblogs.com/JayK/p/3429795.html)，我的电脑是64位，win7，按照这种方法启动始终报1067这个错误。

2. 使用srvany.exe，这几个未测试过，具体例子如下：

   [Windows下Nginx以服务的方式运行](http://www.xiaojian.org/article/432.html)

   [在64位windows下使用instsrv.exe和srvany.exe创建windows服务](http://www.iflym.com/index.php/computer-use/201205020001.html)

   [使用srvany.exe把程序安装成windows服务](http://www.cnblogs.com/codealone/p/3156943.html)

3. 由于上面的方法，在64位电脑上面没成功，

   于是找到[winsw](https://github.com/kohsuke/winsw)项目首页，下载最新的winsw，按照官网配置，只保留了启动配置，结果可以正常启动。附配置

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <service>
     <id>nginx</id>
     <name>nginx</name>
     <description>This service runs nginx continuous integration system.</description>
     <executable>d:/nginx/nginx.exe</executable>
   </service>
   ```

   ​