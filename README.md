## Java SE

### 基础

 1. [类和接口](java/基础/类和接口.md)
 2. [面向对象](java/基础/面向对象.md)
 3. [数据类型](java/基础/数据类型.md)
 4. [数值比较](java/基础/数值比较.md)
 5. [异常处理](java/基础/异常处理.md)

### 容器

 1. [基础容器](java/容器/基础容器.md)
 2. [并发容器](java/容器/并发容器.md)

### 并发

 1. [线程](java/并发/线程.md)
 2. [线程安全I](java/并发/线程安全入门.md)
 3. [线程安全II](java/并发/线程安全进阶.md)
 4. [线程池](java/并发/线程池.md)

### JVM

 1. [JVM](java/JVM/JVM介绍.md)
 2. [内存空间](java/JVM/JVM内存空间.md)
 3. [垃圾回收](java/JVM/JVM垃圾回收.md)
 4. [指令重排序](java/JVM/JVM指令重排序.md)


### I/O

 1. [I/O](java/IO/IO.md)
 2. [NIO](java/IO/NIO.md)
 3. [Net](java/IO/net.md)


## Java EE

### Servlet

 [Servlet](javaee/servlet.md) 是 Java Web 程序接收远程请求的交互接口。

### JDBC

 [JDBC](javaee/JDBC.md) 是 Java 程序连接数据库的基础。在实际项目中，开发者往往会使用封装 JDBC 的 ORM 框架来管理数据库连接。

 目前常用的 ORM 框架和技术有 [JPA](javaee/JPA.md) 和 [MyBatis](javaee/MyBatis.md)。

### Spring
 
 Spring 是目前通用的 Java Web 的项目开发框架，帮助开发者快捷进行 Java Web 开发。

 主要思想：[Spring IOC（控制反转）](javaee/Spring/SpringIOC.md)和 [Spring AOP（面向切面）](javaee/Spring/SpringAOP.md)

## 其它

### 服务器

 [服务器](服务器/服务器.md)用于响应外部请求。

### 数据库

 - **传统数据库**

 在互联网应用中我们通常使用关系型数据库来存储和管理数据，即以二维表的形式存储和管理数据。

 目前最常用的数据库软件是开源免费的关系型数据库 [MySQL](数据库/SQL/MySQL.md)，开发者可以通过 [SQL 指令](数据库/SQL/SQL.md)对其进行操作。

 - **NoSQL 技术**

 传统数据库技术在应对海量用户和数据往往会面临性能瓶颈，于是我们又引入了 NoSQL 技术，作为传统数据库技术的补充。
 
 目前最常用的 NoSQL 技术包括：
 
   1. 基于内存的数据库 [Redis](数据库/NoSQL/Redis.md) ：存储高频信息
   2. 分布式文件存储的非关系型数据库 MongoDB ：存储附加文本信息


### 前端

前端技术用于绘制页面样式，交给浏览器渲染。

 1. [HTML：页面元素摆放](前端/html.md)
 2. [CSS：页面样式设计](前端/css.md)
 3. [JavaScript：前端脚本语言](前端/javascript.md)
 4. [Node.js：基于 JS 的后端运行环境](前端/nodejs.md)
 5. [Vue：前端开发框架](前端/vue.md)



### 管理工具

 1. [Git & GitHub：代码版本管理工具](工具/git.md)
 2. [Maven：第三方包管理工具](工具/maven.md)
 3. [JUnit：软件测试工具](工具/junit.md)

### 设计模式

 1. [创建型模式](设计模式/创建型模式.md)

### 分布式

 1. [Dubbo：Java RPC 框架](分布式/Dubbo.md)

## 计算机基础

### 操作系统

 1. [Linux：基于文件的操作系统](工具/linux.md)

### 计算机网络

 1. [应用层](计算机网络/应用层.md)
 2. [传输层](计算机网络/传输层.md)
 3. [网络层](计算机网络/网络层.md)
 4. [网络接口层](计算机网络/网络接口层.md)
