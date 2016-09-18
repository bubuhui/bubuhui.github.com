---
title: maven改造
date: 2016-09-08 11:26:57
tags: [maven]
categories: [maven]
---

### Java项目maven化改造

1. ​

2. 改造中遇到的问题

   - Maven改造后，web项目启动时候如果报：invalid-byte-tag-in-constant-pool：60 这种类似的错误，可以修改项目中web.xml中的头部信息中添加  metadata-complete="true" 。 [参考链接1](http://stackoverflow.com/questions/32810492/java-7-and-tomcat-7-0-64-classformatexception-invalid-byte-tag-in-constant-po) [参考连接2](http://stackoverflow.com/questions/6751920/tomcat-7-servlet-3-0-invalid-byte-tag-in-constant-pool)

   - 如果不依赖parentpom-war，自建项目打包发现资源文件未发布到class目录，可以在pom.xml文件添加resource插件，具体可以参考parentpom里面的配置。如果不加这个插件，resource打包会出现乱码问题。

     ```xml
     <plugin>
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-resources-plugin</artifactId>
       <version>3.0.1</version>
       <configuration>
         <encoding>UTF-8</encoding>
       </configuration>
     </plugin>
     ```

   - 非maven工程改造maven工程时，需要修改工程中的.classpath和.project文件，不然会把之前的jar和一些linkresource引入进来了。开发人员遇到这种情况，应该修改好了之后提交正确的工程文件。

   - jdk版本问题，具体错误异常类似`Unsupported major.minor version 51.0 时抛出异常，这个提示表示当前编译jdk版本低于某些jar里面提供的jdk版本。具体定位问题，要结合上下文来看，例如下面的：

     ```shell
     错误：服务配置文件不正确，或构造处理程序对象 javax.annotation.processing.Processor: Provider org.eclipse.sisu.space.SisuIndexAPT6 could not be instantiated: java.lang.UnsupportedClassVersionError: javax/inject/Named : Unsupported major.minor version 51.0 时抛出异常
     ```

     这个地方问题出在 `javax.annotation.processing.Processor: Provider org.eclipse.sisu.space.SisuIndexAPT6 could not be instantiated` 这一句，`SisuIndexAPT6` 不能倍实例化，找到具体class对应jar，过滤依赖或者升级jdk来处理。**还有一些报这个错误时候，没有提示明确的类，这个就比较难排查了，需要靠猜测加运气一个个排除了。**

   - `clipse A cycle was detected in the build path of project XXX Build Path Problem`出现这个情况，表示项目中有循环依赖，这个提示里面的xxx表示循环依赖的项目，尝试在每个包里面排除掉循环依赖关系，发现eclipse里面还是有红色的感叹号，这种情况下，只能选择手动调整编译器报错选项了，具体方法就是把循环依赖编译选项由error调整为warning，如下所示：

     ```shell
     Windows -> Preferences -> Java-> Compiler -> Building -> Circular Dependencies
     ```

   - 日志jar冲突

     ```shell
     Exception in thread "main" java.lang.NoClassDefFoundError: Could not initialize class org.apache.log4j.Log4jLoggerFactory
     ```

     出现这样的错误，一般是由于log4j和slf4j冲突导致的。可以使用myeclipse自带的pom.xml编辑器查看依赖关系，或者使用命令行`mvn dependency:tree`来查看依赖。找到依赖引入的关系图谱，然后再排除slf4j相关的jar。有兴趣可以看看为什么这2个jar不能同时共存：[slf4j冲突](http://www.cnblogs.com/asfeixue/p/slf4j.html)

   - jsp或者js中变量${id}被maven替换问题。由于maven自身有一些内置的变量，例如 id、groupId等等，如果在jsp或者js中使用这些变量，在打包过程中则会被替换。目前的解决方案是在war插件中指定需要替换的目录，如下面，表示只替换xml类型文件和frame目录。

     ```xml
        <includes>
          <include>frame/*</include>
          <include>**/*.xml</include>
        </includes>
     ```

   - 二进制文件被替换后损坏问题。由于maven打包过程中会使用resource插件进行替换复制，在这个过程中会对文件进行读——替换——写三个操作，由于编码原因，会损坏二进制文件。因此我们需要但对对这类的文件进行过滤。过滤方式可以参考dotoyo_convert_service_web项目。下面这段配置表示过滤dll文件，不进行替换，xml和properties会进行替换。至于为什么要写一对相反的配置，这是由于有的文件需要复制不替换，有的文件要替换并复制。

     ```xml
     <resources>
       <resource>
         <directory>src/main/resources</directory>
         <filtering>true</filtering>
         <excludes>
           <exclude>**/*.dll</exclude>
         </excludes>
         <includes>
           <include>**/*.xml</include>
           <include>**/*.properties</include>
         </includes>
       </resource>
       <resource>
         <directory>src/main/resources</directory>
         <filtering>false</filtering>
         <excludes>
           <exclude>**/*.xml</exclude>
           <exclude>**/*.properties</exclude>
         </excludes>
         <includes>
           <include>**/*.dll</include>
         </includes>
       </resource>
     </resources>
     ```


4. 改造后遗留问题及处理

   - 目前遗留问题是同名类问题，具体表现形式是找不到某个方法，页面报500错误等等。解决方案是合并同名类。
   - Jar冲突问题，这个目前发现都有日志类，还有一些出现过的，目前都已经处理了，后期如果引入其他项目，有可能会有此类风险，需要按照实际情况排除。

5. Maven使用小技巧

   - 如果想排除某一依赖包所有的jar，可以使用通配符*来进行排除。Maven的版本需要大于3.2.1。下面的例子是排除掉test_common_core的所有jar


   ```xml
      <dependency>
        <groupId>com.test.att</groupId>
        <artifactId>test_common_core</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <exclusions>
          <exclusion>
            <groupId>*</groupId>
            <artifactId>*</artifactId>
          </exclusion>
        </exclusions>
      </dependency>
   ```

6. ​


### 架构调整相关

架构调整一定是有一个明确的目标，知道现阶段的主要问题，对调整的风险和收益情况有一个大致的判断，采用小步快跑、逐步推进的方式比较好。根据下一阶段架构调整目标，分下面3类情况进行分析。

1. 开发相关
   ​	 项目拆分&服务化，目前是直接拆分成多个项目，拆成多个项目过程中由于人力限制，导致部分项目耦合较多，同名类扩散到多个项目，引入一些新的问题。初期项目可以按照整体服务化，只把项目一分为二，拆成web和service2个项目，这样既能满足水平扩展，又可以避免前期由于业务没有梳理清楚而拆分引起的问题。
   ​	业务拆分，由于之前服务化改造并不是很彻底，下一步会按照模块进行拆分。拆分应该不是简单的迁移代码，拆分会涉及到业务架构、代码重构、新业务开发几个方面，步骤不应过大，应该选取新业务重新组织，使用频率高又有问题的老业务进行重构，如果相对比较稳定，现阶段维护较少的，可以在时间充裕后进行重构，这样风险比较容易控制。
   ​	开发规范，这里说的规范不仅仅是指编码风格，还有业务规范，在业务架构清晰之后，开发人员要能明确知道开发的功能属于哪一个模块。各个模块之间采用不同的包名来进行隔离。
2. 性能相关
   1. 系统需要有一个全面的性能测试，知道系统的容量极限，以及系统瓶颈主要在哪些地方。
   2. 整个性能优化应该是一直持续整个项目进度的，可以定期把一些执行时间比较多而且相对较慢的方法或者url拿出来进行优化。
3. 运维相关
   1. 运维工具要配套跟上，发布尽量减少手动操作，可以根据实际情况写成脚本来处理。
   2. 服务器、应用、数据库监控，设置报警机制。
   3. 考虑扩容的方案，至少在新服务器上有一套机制很便捷的扩容。
