# 集成DBCP2 · dynamic-datasource · 看云

+ [基础介绍](https://www.kancloud.cn/tracy5546/dynamic-datasource/2273418#_2)
+ [集成步骤](https://www.kancloud.cn/tracy5546/dynamic-datasource/2273418#_7)
    - [1. 项目引入 Dbcp2 依赖。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2273418#1__Dbcp2___9)
    - [2.参数配置。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2273418#2_21)
+ [核心源码](https://www.kancloud.cn/tracy5546/dynamic-datasource/2273418#_44)

## 基础介绍
+ Dbcp2 Github [https://github.com/apache/commons-dbcp](https://github.com/apache/commons-dbcp)
+ Dbcp2 文档 [https://commons.apache.org/proper/commons-dbcp/](https://commons.apache.org/proper/commons-dbcp/)

## 集成步骤
## 1. 项目引入 Dbcp2 依赖。
![1709005572262-e7556545-4a54-47e5-bd1e-a40faebe8da9.svg](./img/1MouJ-qik3tDxbYO/1709005572262-e7556545-4a54-47e5-bd1e-a40faebe8da9-595757.svg)

```plain
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-dbcp2</artifactId>
    <version>${version}</version>
</dependency>
```

复制

## 2.参数配置。
1. 如果参数都未配置，则保持原组件默认值。
2. 如果配置了全局参数，则每一个数据源都会继承对应参数。
3. 每一个数据源可以单独设置参数覆盖全局参数。

```plain
spring:
  datasource:
    dynamic:
      dbcp2:  # 全局dbcp2参数，所有值和默认保持一致。(现已支持的参数如下,不清楚含义不要乱设置)
        initial-size:
      datasource:
        master:
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver
          url: jdbc:mysql://xx.xx.xx.xx:3306/dynamic?characterEncoding=utf8&useSSL=false
          dbcp2: # 以下参数针对每个库可以重新设置dbcp2参数
            initial-size:
#           ......
```

复制

## 核心源码
dbcp2数据源创建器[https://github.com/baomidou/dynamic-datasource-spring-boot-starter/blob/master/dynamic-datasource-creator/src/main/java/com/baomidou/dynamic/datasource/creator/dbcp/Dbcp2DataSourceCreator.java](https://github.com/baomidou/dynamic-datasource-spring-boot-starter/blob/master/dynamic-datasource-creator/src/main/java/com/baomidou/dynamic/datasource/creator/dbcp/Dbcp2DataSourceCreator.java)

dbcp2参数配置类[https://github.com/baomidou/dynamic-datasource-spring-boot-starter/blob/master/dynamic-datasource-creator/src/main/java/com/baomidou/dynamic/datasource/creator/dbcp/Dbcp2Config.java](https://github.com/baomidou/dynamic-datasource-spring-boot-starter/blob/master/dynamic-datasource-creator/src/main/java/com/baomidou/dynamic/datasource/creator/dbcp/Dbcp2Config.java)  


> 来自: [集成DBCP2 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2273418)
>



> 更新: 2024-02-27 11:46:14  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/mdzqfzbr71vpwft9>