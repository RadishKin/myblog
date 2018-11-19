---
title: [转载]常用数据库 JDBC URL 格式
categories:
- 笔记
tags:
- java
- jdbc
---


转载自  ：
{% blockquote 落落无伤的博客 https://www.cnblogs.com/qianqian528/p/8032098.html 常用数据库 JDBC URL 格式 %}
{% endblockquote %}

 

## 一 常用数据库 JDBC URL 格式
### 1 sqLite
 驱动程序包名：`sqlitejdbc-v056.jar`  
驱动程序类名: `org.sqlite.JDBC`  
JDBC URL: `jdbc:sqlite:c:\*.db`  
默认端口 无
 
### 2 Microsoft SQL Server
Microsoft SQL Server JDBC Driver 
 
驱动程序包名：`msbase.jar mssqlserver.jar msutil.jar`  
驱动程序类名: `com.microsoft.jdbc.sqlserver.SQLServerDriver`  
JDBC URL: `jdbc:microsoft:sqlserver://<server_name>:<port>`  
默认端口1433，如果服务器使用默认端口则port可以省略

 
### 3 Oracle
Oracle Thin JDBC Driver
 
驱动程序包名：`ojdbc14.jar`  
驱动程序类名: `oracle.jdbc.driver.OracleDriver`  
JDBC URL:  
`jdbc:oracle:thin:@//<host>:<port>/ServiceName`  或  
`jdbc:oracle:thin:@<host>:<port>:<SID>`
 
### 4 IBM DB2
IBM DB2 Universal Driver Type 4
 
驱动程序包名：`db2jcc.jar db2jcc_license_cu.jar`  
驱动程序类名: `com.ibm.db2.jcc.DB2Driver`  
JDBC URL:` jdbc:db2://<host>[:<port>]/<database_name>`  

 
### 5 MySQL
MySQL Connector/J Driver
 
驱动程序包名：`mysql-connector-java-x.x.xx-bin.jar`  
驱动程序类名: `com.mysql.jdbc.Driver`  
JDBC URL:` jdbc:mysql://<host>:<port>/<database_name>`  
默认端口3306，如果服务器使用默认端口则port可以省略  

>MySQL Connector/J Driver 允许在URL中添加额外的连接属性  
>```
>jdbc:mysql://<host>:<port>/<database_name>?property1=value1&property2=value2
>```  
>注意： 需要操作记录为了避免乱码应该加上属性 useUnicode=true&characterEncoding=utf8 ，比如  
>```
>jdbc:mysql://192.168.177.129:3306/report?useUnicode=true&characterEncoding=utf8
>``` 

## 二 Hibernate 中数据库的方言  
### 1 Oracle 方言
```
org.hibernate.dialect.OracleDialect
```
### 2 MySQL方言
```
org.hibernate.dialect.MySQLDialect
```
3 SQLServer方言
```
org.hibernate.dialect.SQLServerDialect
```