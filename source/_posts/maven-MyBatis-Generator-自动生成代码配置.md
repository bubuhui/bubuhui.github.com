---
title: maven + MyBatis Generator 自动生成代码配置
date: 2016-08-24 22:10:52
tags: [mybatis,maven]
categories: [mybatis]
---



最近在搭建新项目，用到mybatis，记录一下maven+mybatis generator生成代码配置的过程，作备忘用。参考网站:[mybatis generator](http://mybatis.github.io/generator/index.html)

pom文件配置，主要是引入mybatis-generator-maven-plugin插件。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.test.maven</groupId>
	<artifactId>aliexpress-server</artifactId>
	<packaging>war</packaging>
	<version>1.0-SNAPSHOT</version>
	<name>test-server Maven Webapp</name>
	<url>http://maven.apache.org</url>
	<dependencies>

		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.2.6</version>
		</dependency>

		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.2.2</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>${junit.version}</version>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<build>
		<finalName>test-maven</finalName>
		<resources>
			<resource>
				<directory>src/main/resources</directory>
				<filtering>true</filtering>
			</resource>
		</resources>
		<plugins>
			<!-- mybatis 代码生成插件 -->
			<plugin>
				<groupId>org.mybatis.generator</groupId>
				<artifactId>mybatis-generator-maven-plugin</artifactId>
				<version>1.3.2</version>
				<configuration>
					<overwrite>true</overwrite>
					<configurationFile>${project.basedir}/src/test/resources/MyBatis_Generator_Config.xml</configurationFile>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-war-plugin</artifactId>
				<version>2.2</version>
				<configuration>
					<classifier>${project.classifier}</classifier>
					<webResources>
						<resource>
							<filtering>true</filtering>
							<directory>src/main/webapp</directory>
						</resource>
					</webResources>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```

配置MyBatis_Generator_Config.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
	  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
	  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
	<!-- 使用方法:
       1.配置 properties.
       2.配置数据库驱动位置.
       3.配置需要反向生成代码的表.
       4.配置完成后,在项目目录test-maven下运行：mvn mybatis-generator:generate
       5.生成的代码在：target/generated-sources/mybatis-generator目录下.
     -->
    <!-- 1.指定配置文件路径，在这里我们使用绝对路径，如果使用properties的resource属性，需要设置classpath -->
    <properties  url="file:///G:/eclipse/workspace/test-maven/src/test/resources/init.properties"/>
    <!-- 2.指定数据库驱动jar的位置，同上，使用绝对路径  -->
	<classPathEntry location="D:/maven_repository/.m2/repository/mysql/mysql-connector-java/5.1.15/mysql-connector-java-5.1.15.jar" />

	<context id="mybatis" targetRuntime="MyBatis3">
		<jdbcConnection driverClass="${jdbc_driver}"
			connectionURL="${jdbc_url}"
			userId="${jdbc_user}"
			password="${jdbc_password}">
		</jdbcConnection>

		<javaTypeResolver >
			<property name="forceBigDecimals" value="false" />
		</javaTypeResolver>

		<javaModelGenerator targetPackage="test.model" targetProject="MAVEN">
			<property name="enableSubPackages" value="true" />
			<property name="trimStrings" value="true" />
		</javaModelGenerator>

		<sqlMapGenerator targetPackage="mapper"  targetProject="MAVEN">
			<property name="enableSubPackages" value="true" />
		</sqlMapGenerator>

		<javaClientGenerator type="XMLMAPPER" targetPackage="test.dao"  targetProject="MAVEN">
			<property name="enableSubPackages" value="true" />
		</javaClientGenerator>
		
		<table tableName="test_maven" domainObjectName="TestMaven" enableSelectByExample="false" enableDeleteByExample="false"
            enableCountByExample="false" enableUpdateByExample="false" />

	</context>
</generatorConfiguration>
```

配置init.properties，这个步骤其实可以省略，在MyBatis_Generator_Config.xml里面直接配置即可，注意参数的转义符。

```properties
#Mybatis Generator configuration
jdbc_driver = com.mysql.jdbc.Driver
jdbc_url=jdbc\:mysql\://127.0.0.1:3306/test_maven?useUnicode\=true&characterEncoding\=utf8&autoReconnect\=true&zeroDateTimeBehavior\=convertToNull&transformedBitIsBoolean\=true
jdbc_user=root
jdbc_password=root
```

最后在命令行进入项目目录，执行`mvn mybatis-generator:generate`，查看输出结果即可。执行mvn命令如果有报错，先加上-X参数看看报错异常情况，然后根据问题检查配置，如果配置无问题，还报错，有可能是注释问题，去掉注释试试，之前碰到过一次，没记录下来报错详细情况，去掉注释后正常使用。