# 自定义负载均衡策略 · dynamic-datasource · 看云

## 自定义负载均衡策略
## 基础介绍
如下图slave组下有三个数据源，当用户使用slave切换数据源时会使用负载均衡算法。

系统自带了两个负载均衡算法

+ LoadBalanceDynamicDataSourceStrategy 轮询,是默认的。
+ RandomDynamicDataSourceStrategy 随机的。

```plain
spring:
  datasource:
    dynamic:
      datasource:
        master:
          username: sa
          password: ""
          url: jdbc:h2:mem:test
          driver-class-name: org.h2.Driver
          schema: db/schema.sql
        slave_1:
          username: sa
          password: ""
          url: jdbc:h2:mem:test
          driver-class-name: org.h2.Driver
        slave_2:
          username: sa
          password: ""
          url: jdbc:h2:mem:test
          driver-class-name: org.h2.Driver
        slave_3:
          username: sa
          password: ""
          url: jdbc:h2:mem:test
          driver-class-name: org.h2.Driver
      strategy: com.baomidou.dynamic.datasource.strategy.LoadBalanceDynamicDataSourceStrategy
```

复制

## 如何自定义
如果默认的两个都不能满足要求，可以参考源码自定义。 暂时只能全局更改。

```plain
import java.util.List;
import java.util.concurrent.ThreadLocalRandom;
import javax.sql.DataSource;

public class RandomDynamicDataSourceStrategy implements DynamicDataSourceStrategy {
    public RandomDynamicDataSourceStrategy() {
    }

    public DataSource determineDataSource(List<DataSource> dataSources) {
        return (DataSource)dataSources.get(ThreadLocalRandom.current().nextInt(dataSources.size()));
    }
}
```

复制  


> 来自: [自定义负载均衡策略 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268604)
>



> 更新: 2024-02-27 11:49:37  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/prqxlti5yif92zhq>