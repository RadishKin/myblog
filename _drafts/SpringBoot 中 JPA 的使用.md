---
title: Springboot中JPA的使用
categories:
- 笔记
tags:
- java
- Springboot
---

本文是我的Springboot系列的第二篇

谈起操作数据库，大致可以分为几个阶段：首先是 JDBC 阶段，初学 JDBC 可能会使用原生的 JDBC 的 API，再然后可能会使用数据库连接池，比如：c3p0、dbcp，还有一些第三方工具，比如 dbutils 等；下一个阶段就是 Hibernate，大家体会到了操作数据库可以不用自己手动编写 SQL，调用 Hibernate 提供的 API 即可。今天给大家讲述的是操作数据库的另一个模块 JPA，即 Java 持久层的 API。

## 一、JPA 概述
1. Java Persistence API（Java 持久层 API）：用于对象持久化的 API，JPA通过JDK 5.0注解或XML描述对象－关系表的映射关系，并将运行期的实体对象持久化到数据库中
2. 作用：使得应用程序以统一的方式访问持久层
3. 前言中提到了 Hibernate，那么JPA 与 Hibernate究竟是什么关系呢：

    1. JPA 是 Hibernate 的一个抽象

    2. JPA 是一种 ORM 规范，是 Hibernate 功能的一个子集 

    3. Hibernate 是 JPA 的一个实现

4. JPA 包括三个方面的技术：

    1. ORM 映射元数据，支持 XML 和 JDK 注解两种元数据的形式

    2. JPA 的 API

    3. 查询语言：JPQL

本文也将详细介绍JPA ORM 映射元数据的注解方式和 JPA 的 API 以及 JPQL 三个方面