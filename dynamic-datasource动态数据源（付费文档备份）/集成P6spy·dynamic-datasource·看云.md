# 集成P6spy · dynamic-datasource · 看云

+ [基础介绍](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268591#_2)
+ [集成步骤](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268591#_14)
    - [1. 项目引入p6spy依赖。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268591#1_p6spy_16)
    - [2. 启用p6spy相关配置。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268591#2_p6spy_28)
    - [3. 引入相关配置文件。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268591#3__49)

## 基础介绍
+ P6spy Github [https://github.com/p6spy/p6spy](https://github.com/p6spy/p6spy)
+ P6spy 文档 [https://p6spy.readthedocs.io/en/latest/](https://p6spy.readthedocs.io/en/latest/)

```plain
# before
select * from user where age>?
# after enabled p6spy
select * from user where age>6
```

复制

## 集成步骤
## 1. 项目引入p6spy依赖。
![1709005621731-f3abae85-9caa-438a-9012-ce6a17e709db.svg](./img/A9eWtuMIpWylEf4u/1709005621731-f3abae85-9caa-438a-9012-ce6a17e709db-076040.svg)

```plain
<dependency>
    <groupId>p6spy</groupId>
    <artifactId>p6spy</artifactId>
    <version>${version}</version>
</dependency>
```

复制

## 2. 启用p6spy相关配置。
```plain
spring:
  datasource:
    dynamic:
      p6spy: true # 默认false,建议线上关闭。
      datasource:
        product:
          username: sa
          password: ""
          url: jdbc:h2:mem:test
          driver-class-name: org.h2.Driver
          p6spy: false # 如果这个库不需要可单独关闭。
        order:
          username: sa
          password: ""
          url: jdbc:h2:mem:test
          driver-class-name: org.h2.Driver
```

复制

## 3. 引入相关配置文件。
在classPath下创建spy.properties

```plain
# 一个最简单配置,定义slf4j日志输出。 更多参数请自行了解。
appender=com.p6spy.engine.spy.appender.Slf4JLogger
```

复制

简单示例 [https://github.com/dynamic-datasource/dynamic-datasource-samples/tree/master/datasource-samples/druid-sample](https://github.com/dynamic-datasource/dynamic-datasource-samples/tree/master/datasource-samples/druid-sample)  


> 来自: [集成P6spy · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268591)
>



> 更新: 2024-02-27 11:47:03  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/iad96zarmk9rooru>