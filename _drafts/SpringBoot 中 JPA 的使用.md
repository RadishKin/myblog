---
title: Springboot中JPA的使用
categories:
- 笔记
tags:
- java
- Springboot
---

本文是我的Springboot系列的第二篇


## JPA 概述
1. Java Persistence API（Java 持久层 API）：用于对象持久化的 API，JPA通过JDK 5.0注解或XML描述对象－关系表的映射关系，并将运行期的实体对象持久化到数据库中
2. 作用：使得应用程序以统一的方式访问持久层
3. 前言中提到了 Hibernate，那么JPA 与 Hibernate究竟是什么关系呢：

    1. JPA 是 Hibernate 的一个抽象

    2. JPA 是一种 ORM 规范，是 Hibernate 功能的一个子集 

    3. Hibernate 是 JPA 的一个实现

## spring-data-jpa的简单介绍
SpringData : Spring 的一个子项目。用于简化数据库访问，支持NoSQL 和 关系数据存储。其主要目标是使数据库的访问变得方便快捷。

SpringData 项目所支持 NoSQL 存储：
```
 MongoDB （文档数据库）
 Neo4j（图形数据库）
 Redis（键/值存储）
 Hbase（列族数据库）
```
SpringData 项目所支持的关系数据存储技术：
```
JDBC
JPA
```

