---
title: Springboot快速上手
categories:
- 笔记
tags:
- java
- Springboot
---

Springboot是由spring开发团队Pivotal推出的全新框架，他设计目的简化繁琐的Spring配置,专注于应用的开发。SpringBoot采用了"约定优先于配置（COC，Convnetion Over Configuration）"的设计思想，通过为企业开发应用环境设计出了很多自动配置的依赖模块,这些模块几乎不需要配置，开箱即用，极大的简化了开发周期和代码量。

<!--toc-->

### 环境

本文开发环境以写作时的最新版本`2.1.0.RELEASE`为基准
- JDK1.8及以上
- Maven 3.2
- Tomcat 8及以上
- hibernate 5.2以上

使用最新版idea可以快速创建一个springboot项目并初始化一些模块，如果不使用idea，可以新建Maven项目并从官网下载并定制所需组件


### 项目Maven依赖
我们使用Maven作为项目的的依赖管理，下面列出了项目所有使用的模块

继承Springboot的核心依赖
```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.1.0.RELEASE</version>
	<relativePath/> <!-- lookup parent from repository -->
</parent>
```

jpa
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

web核心组件
```xml   
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
mysql ,如果mysql-server版本是8.0需要指明connector版本8.0+
```xml
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<scope>runtime</scope>
</dependency>
```
lmbok，减少代码量
```xml
<dependency>
	<groupId>org.projectlombok</groupId>
	<artifactId>lombok</artifactId>
	<optional>true</optional>
</dependency>
```
Springboot 测试模块，包括junit
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<scope>test</scope>
</dependency>
```

swagger
```xml
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.2.2</version>
</dependency>

<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.2.2</version>
</dependency>
```

Spring Restful
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-rest</artifactId>
</dependency>
```

### 工程目录结构

然后我们查看下项目生成的默认文件结构
```bash
$ tree                
.
├── mvnw
├── mvnw.cmd
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── top/ljmx/springboot/demo/demo
│   │   │       └── DemoApplication.java
│   │   └── resources
│   │       ├── application.properties
│   │       ├── static
│   │       └── templates
│   └── test
│       └── java
│           └── top/ljmx/springboot/demo/demo
│               └── DemoApplicationTests.java
└── srpingboot-sdemo.iml

18 directories, 7 files
```

#

`src`是项目的主文件夹，其中`main`是主要代码，`test`是测试代码，这是一个典型的Maven工程的文件结构，`pom.xml`是Maven的依赖文件。`main`先分为`java`和`resources`，`java`存放代码，`resources`存放静态资源，目录中的`application.properties`是工程的配置文件

Springboot遵循springMVC的分层结构结构，所以我们先在包`top.ljmx.springboot.demo.demo`下新建以下几个子包
- controller 存放所有的控制器代码
- model 存放所有的数据模型
- repository 存放所有的数据操作
- service 业务代码

其他可选包
- config 存放配置代码，如swagger
- util 存放工具类
- expection 统一异常处理
