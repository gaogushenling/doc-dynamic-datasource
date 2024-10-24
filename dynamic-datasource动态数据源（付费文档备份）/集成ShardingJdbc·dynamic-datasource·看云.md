# 集成ShardingJdbc · dynamic-datasource · 看云

+ [基础介绍](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268593#_2)
+ [使用方法](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268593#_16)
    - [1. 项目引入 shardingsphere 依赖。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268593#1__shardingsphere__18)
    - [2. 分别配置shardingjdbc和多数据源。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268593#2_shardingjdbc_35)
    - [3. 通过自定义DynamicDataSourceProvider完成与shardingsphere的集成。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268593#3_DynamicDataSourceProvidershardingsphere_95)
+ [示例项目](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268593#_153)

## 基础介绍
+ shardingsphere Github [https://github.com/apache/shardingsphere](https://github.com/apache/shardingsphere)
+ shardingsphere 文档 [https://shardingsphere.apache.org/](https://shardingsphere.apache.org/)
+ shardingsphere 示例 [https://github.com/apache/shardingsphere/tree/master/examples](https://github.com/apache/shardingsphere/tree/master/examples)

---

多数据源与shardingsphere集成的场景：部分表比较大需要分表由shardingsphere完成，同时又有多库的需求。

可参考文章 [https://zhuanlan.zhihu.com/p/166105923](https://zhuanlan.zhihu.com/p/166105923)

shardingsphere有多个不同版本，**不同版本的集成方法具体查看最底下的示例项目**。

## 使用方法
## 1. 项目引入 shardingsphere 依赖。
![1709005638893-f2baf3a2-f2cc-46bd-8767-71eedb3a0617.svg](./img/Ea9vFN6po8kgwLV8/1709005638893-f2baf3a2-f2cc-46bd-8767-71eedb3a0617-940350.svg)

```plain
<dependency>
    <groupId>org.apache.shardingsphere</groupId>
    <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
    <version>${version}</version>
</dependency>
<dependency>
    <groupId>org.apache.shardingsphere</groupId>
    <artifactId>sharding-jdbc-spring-namespace</artifactId>
    <version>${version}</version>
</dependency>
```

复制

## 2. 分别配置shardingjdbc和多数据源。
```plain
spring:
  # shardingjdbc 配置
  shardingsphere:
    datasource:
      names: shardingmaster,shardingslave0,shardingslave1
      shardingmaster:
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: org.h2.Driver
        jdbc-url: jdbc:h2:mem:test
        username: sa
        password: ""
      shardingslave0:
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: org.h2.Driver
        jdbc-url: jdbc:h2:mem:test
        username: sa
        password: ""
      shardingslave1:
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: org.h2.Driver
        jdbc-url: jdbc:h2:mem:test
        username: sa
        password: ""
    masterslave:
      name: ms
      load-balance-algorithm-type: round_robin
      master-data-source-name: shardingmaster
      slave-data-source-names: shardingslave0,shardingslave1
    sharding:
      tables:
        t_order:
          actualDataNodes: shardingmaster.t_order${0..1}
          tableStrategy:
            inline:
              shardingColumn: order_id
              algorithmExpression: t_order${order_id % 2}
          keyGenerator:
            type: SNOWFLAKE
            column: order_id
  # 动态数据源配置
  datasource:
    dynamic:
      datasource:
        master:
          username: sa
          password: ""
          url: jdbc:h2:mem:master
          driver-class-name: org.h2.Driver
          schema: db/schema.sql
        test:
          username: sa
          password: ""
          url: jdbc:h2:mem:test
          driver-class-name: org.h2.Driver
          schema: db/schema.sql
```

复制

## 3. 通过自定义DynamicDataSourceProvider完成与shardingsphere的集成。
```plain
@Configuration
public class MyDataSourceConfiguration {

    @Resource
    private DynamicDataSourceProperties properties;

    /**
     * 分片: shardingDataSource
     * 主从: masterSlaveDataSource
     * 根据自己场景修改注入
     */
//    @Lazy 某些springBoot版本不加会报错（暂不清楚原理0 0）
    @Resource
    private MasterSlaveDataSource masterSlaveDataSource;

 //    @Lazy
//    @Resource
//    private ShardingDataSource shardingDataSource;

    @Bean
    public DynamicDataSourceProvider dynamicDataSourceProvider() {
        return new AbstractDataSourceProvider() {

            @Override
            public Map<String, DataSource> loadDataSources() {
                Map<String, DataSource> dataSourceMap = new HashMap<>();
                dataSourceMap.put("sharding", masterSlaveDataSource);
//下面的代码可以把 shardingJdbc 内部管理的子数据源也同时添加到动态数据源里 (根据自己需要选择开启+注解了@Lazy被代理了不可以)
                Map<String, DataSource> shardingInnerDataSources = masterSlaveDataSource.getDataSourceMap();
                dataSourceMap.putAll(shardingInnerDataSources);
                return dataSourceMap;
            }
        };
    }

    /**
     * 将动态数据源设置为首选的
     * 当spring存在多个数据源时, 自动注入的是首选的对象
     * 设置为主要的数据源之后，就可以支持shardingjdbc原生的配置方式了
     * 3.4.0版本及以上使用以下方式注入,老版本请阅读文档  进阶-手动注入多数据源
     */
    @Primary
    @Bean
    public DataSource dataSource() { 
        DynamicRoutingDataSource dataSource = new DynamicRoutingDataSource();
        dataSource.setPrimary(properties.getPrimary());
        dataSource.setStrict(properties.getStrict());
        dataSource.setStrategy(properties.getStrategy());
        dataSource.setP6spy(properties.getP6spy());
        dataSource.setSeata(properties.getSeata());
        return dataSource;
    }
}
```

复制

## 示例项目
[https://github.com/dynamic-datasource/dynamic-datasource-samples/tree/master/third-part-samples/shardingsphere-sample](https://github.com/dynamic-datasource/dynamic-datasource-samples/tree/master/third-part-samples/shardingsphere-sample)  


> 来自: [集成ShardingJdbc · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268593)
>



> 更新: 2024-02-27 11:47:22  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/cpzowh1s8y2ygakz>