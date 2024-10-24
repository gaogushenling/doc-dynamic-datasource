# 集成Jndi · dynamic-datasource · 看云

JNDI是什么？ [https://blog.csdn.net/zhaosg198312/article/details/3979435](https://blog.csdn.net/zhaosg198312/article/details/3979435)

用到JNDI大部分应该是老项目了。 如无需求不建议继续使用JNDI数据源。

```plain
spring:
  datasource:
    dynamic:
      datasource:
          db1:
            jndi_name: xxx
          db2:
            jndi_name: xxx
```

复制  


> 来自: [集成Jndi · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2292020)
>



> 更新: 2024-02-27 11:46:31  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/qgmd52a7q01ll74c>