# 集成BeeCP · dynamic-datasource · 看云

+ [基础介绍](https://www.kancloud.cn/tracy5546/dynamic-datasource/2270658#_2)
+ [集成步骤](https://www.kancloud.cn/tracy5546/dynamic-datasource/2270658#_7)
    - [1. 项目引入 BeeCp 依赖。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2270658#1__BeeCp__9)
    - [2.参数配置。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2270658#2_21)
+ [核心源码](https://www.kancloud.cn/tracy5546/dynamic-datasource/2270658#_68)

## 基础介绍
+ BeeCp Github [https://github.com/Chris2018998/BeeCP](https://github.com/Chris2018998/BeeCP)
+ BeeCp 文档 [https://github.com/Chris2018998/BeeCP/blob/master/README_ZH.md](https://github.com/Chris2018998/BeeCP/blob/master/README_ZH.md)

## 集成步骤
## 1. 项目引入 BeeCp 依赖。
![1709005558978-ae1d670d-b623-4d4e-9bd9-b1fe2db18f34.svg](./img/bQsEfTwD7J78fjSI/1709005558978-ae1d670d-b623-4d4e-9bd9-b1fe2db18f34-688483.svg)

```plain
<dependency>
    <groupId>com.github.chris2018998</groupId>
    <artifactId>beecp</artifactId>
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
      beecp:  # 全局beeCp参数，所有值和默认保持一致。(现已支持的参数如下,不清楚含义不要乱设置)
        default-catalog:
        default-schema:
        default-readOnly:
        default-auto-commit:
        default-transaction-isolation-code:
        default-transaction-isolation-name:
        fair-mode:
        initial-size:
        max-active:
        borrow-semaphore-size:
        max-wait:
        idle-timeout:
        hold-timeout:
        connection-test-sql:
        connection-test-timeout:
        connection-test-integererval:
        idle-check-time-integererval:
        force-close-using-on-clear:
        delay-time-for-next-clear:
        connection-factory-class-name:
        xa-connection-factory-class-name:
        pool-implement-class-name:
        enable-jmx:
        connect-properties:
      datasource:
        master:
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver
          url: jdbc:mysql://xx.xx.xx.xx:3306/dynamic?characterEncoding=utf8&useSSL=false
          beecp: # 以下参数针对每个库可以重新设置beeCp参数
            initial-size:
            max-active:
#           ......
```

复制

## 核心源码
BeeCp数据源创建器[https://github.com/baomidou/dynamic-datasource-spring-boot-starter/blob/master/dynamic-datasource-creator/src/main/java/com/baomidou/dynamic/datasource/creator/beecp/BeeCpDataSourceCreator.java](https://github.com/baomidou/dynamic-datasource-spring-boot-starter/blob/master/dynamic-datasource-creator/src/main/java/com/baomidou/dynamic/datasource/creator/beecp/BeeCpDataSourceCreator.java)

BeeCp参数配置类[https://github.com/baomidou/dynamic-datasource-spring-boot-starter/blob/master/dynamic-datasource-creator/src/main/java/com/baomidou/dynamic/datasource/creator/beecp/BeeCpConfig.java](https://github.com/baomidou/dynamic-datasource-spring-boot-starter/blob/master/dynamic-datasource-creator/src/main/java/com/baomidou/dynamic/datasource/creator/beecp/BeeCpConfig.java)  


> 来自: [集成BeeCP · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2270658)
>



> 更新: 2024-02-27 11:46:01  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/ag9bkl13kwlq9fo5>