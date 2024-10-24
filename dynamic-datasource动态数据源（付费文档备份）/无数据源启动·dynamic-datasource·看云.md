# 无数据源启动 · dynamic-datasource · 看云

+ [基础介绍](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268601#_2)
+ [配置方式](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268601#_6)

## 基础介绍
场景：部分系统可能启动的时候完全不需要数据源，全靠启动后动态添加。

## 配置方式
即基本不需要配置，可能根据需要配置一些类似Druid和HikariCp全局参数。

如需配置Druid和HikariCp全局参数可参考对应章节文档。

```plain
spring:
  datasource:
    dynamic:
      primary: master #设置默认的数据源或者数据源组,默认值即为master
      strict: false #设置严格模式,默认false不启动. 启动后在未匹配到指定数据源时候会抛出异常,不启动则使用默认数据源.
```

复制

因为没有匹配的主数据源，启动的时候你会见到类似如下日志。

```plain
dynamic-datasource initial loaded 0 datasource,Please add your primary datasource or check your configuration
```

复制

一般无数据源启动需要结合 进阶使用里的动态添加移除在启动后添加  


> 来自: [无数据源启动 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268601)
>



> 更新: 2024-02-27 11:48:46  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/xikoerpyvf1goouk>