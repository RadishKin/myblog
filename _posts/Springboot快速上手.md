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

### 一个简单的Springboot入门实例

>本实例参照库米云微王华杰老师课程代码

本实例实现了一套登陆与注册接口，并使用slflog收集日志，并用swagger2提供文档和测试服务


编辑`resources`目录下的`application.properties`文件
```yaml
#tomcat config
server.port = 8080
server.tomcat.uri-encoding = utf-8

#Mysql
spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
#配置mysql驱动
spring.datasource.url = jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2B8
#serverTimezone=GMT%2B8是解决运行中出现f服务器时区无法识别的异常的
spring.datasource.username = root
spring.datasource.password = ***********

#jpa
spring.jpa.database = mysql
spring.jpa.show-sql = true
spring.jpa.hibernate.ddl-auto = update
spring.jpa.hibernate.naming.physical-strategy = org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy
#数据库方言mysql
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect

```
然后在MySQL中新建一个名叫test的数据库

然后先新建一个用户实体类，在model包下新建一个User.java,写入以下内容
```java
package top.ljmx.demo.model;

import lombok.Data;
import javax.persistence.Entity;
import javax.persistence.*;

@Entity   //声明数据实体类
@Data   //产生setter，getter方法
public class User {
    @Id
    //标识主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer keyid;

    private String userName;
    private String phoneNumber;
    private String password;

}
```
可以在该类添加@table注解可以指定自动生成的表的类名

然后基于JPA实现DAO层（数据操作层，就是repository包），新建一个UserRepositoty的类，代码如下
```java
package top.ljmx.demo.repositoty;

import org.springframework.data.jpa.repository.Query;
import top.ljmx.demo.model.User;
import org.springframework.stereotype.Repository;
import org.springframework.data.jpa.repository.JpaRepository;

@Repository
public interface UserRepositoty extends JpaRepository<User,Integer>{
    @Query("select a from User a where a.phoneNumber=?1")
    User getByPhoneNumber(String phoneNumber);

}

```

需要解释的是，Spring Data JPA提供了很多持久层接口，例如`Repository`,`CrudRepositoty`,`PagingAndSortingRepository` 以及`JpaRepository` 接口。其中`Repository`为基类，`JpaRepository`继承自`PagingAndSortingRepository`接口，两个泛型参数分别代表Java POJO类以及主键数据类型。我们创建自己的数据库操作接口时，只需继承上述JPA提供的某个接口，即可自动继承相关数据操作方法，而不需要再次实现。例如`CrudRepositoty`提供了对增删改查操作的实现，`PagingAndSortingRepository`提供了分页查询方法的实现。另外JPA提供了一套命名规则例如`readBy()`等，这些方法也只需要用户申明而由JPA自动实现了。如果这仍不能满足业务需求，也可以自定义SQL查询语句，例如上述代码所示，采用@Query标签,我们之后将开设单独的一章讲解JPA

最后实现控制器类，在包 `controller`中新建UserController类
```java

package top.ljmx.demo.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import top.ljmx.demo.model.User;
import top.ljmx.demo.repositoty.UserRepositoty;

//Spring组合注解,声明Rest接口
@RestController
public class UserController {
    @Autowired
    //自动装配成员变量
    private UserRepositoty userRepositoty;

    @ApiOperation(value = "注册")
    @RequestMapping(value = "/user/register",method = RequestMethod.GET)
    //地址映射，并指定请求方法
    public Object register(
            @RequestParam("username") String userName,
            @RequestParam("phonenumber") String phoneNumber,
            @RequestParam("password") String password){
            //参数列表
        User user = new User();
        user.setUserName(userName);
        user.setPhoneNumber(phoneNumber);
        user.setPassword(password);

        try {
            userRepositoty.save(user);
            //实例化对象
        }catch (Exception e){
            return "注册失败";
        }
        return "注册成功";

    }

    @RequestMapping(value = "/user/login",method = RequestMethod.GET)
    public Object login(@RequestParam("phonenumber") String phoneNumber,
                        @RequestParam("password") String password){
        User user = userRepositoty.getByPhoneNumber(phoneNumber);
        if (user.getPassword().equals(password)){
            return "登录成功";
        }else {
            return "登录失败";
        }
    }
}

```
这个控制器实现了`login`和`register`两个接口,可以模拟简单的登录和注册功能

当我们配置好数据库信息时，
运行main函数，访问`http://localhost:8080/user/register?username=mx&phonenumber=1234546&password=123`会在数据库中新建一条数据,访问`http://localhost:8080/user/login?phonenumber=123454&password=123`(数据表里存在的数据)会输出登录正常。

之后的文章会对这个简单的注册登录做更多改进