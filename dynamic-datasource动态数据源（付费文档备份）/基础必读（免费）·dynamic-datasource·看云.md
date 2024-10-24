# 基础必读（免费） · dynamic-datasource · 看云

**一个基于springboot的快速集成多数据源的启动器**

![1709005325181-01addfae-43cd-4617-ba76-d2a68a05ad9d.svg](./img/cvd4kmvse0ynfuoc/1709005325181-01addfae-43cd-4617-ba76-d2a68a05ad9d-038078.svg)![1709005323369-8e3ea6f0-d164-4269-82a2-37148812ecf9.svg](./img/cvd4kmvse0ynfuoc/1709005323369-8e3ea6f0-d164-4269-82a2-37148812ecf9-970713.svg)![1709005323754-21a04408-e1e8-403f-993d-1d5d672f13cf.svg](./img/cvd4kmvse0ynfuoc/1709005323754-21a04408-e1e8-403f-993d-1d5d672f13cf-414431.svg)![1709005323361-61270a43-f2e9-4998-b9c4-b7c9748d6da4.svg](./img/cvd4kmvse0ynfuoc/1709005323361-61270a43-f2e9-4998-b9c4-b7c9748d6da4-892427.svg)![1709005323371-0481ecae-a77a-4657-adef-5f5acc6a1ca6.svg](./img/cvd4kmvse0ynfuoc/1709005323371-0481ecae-a77a-4657-adef-5f5acc6a1ca6-142977.svg)![1709005323751-6accfc73-1f83-4f24-8ca0-a7c2e440c086.svg](./img/cvd4kmvse0ynfuoc/1709005323751-6accfc73-1f83-4f24-8ca0-a7c2e440c086-864986.svg)![1709005323792-d812aa7e-3d4b-4c2c-897c-cf736274ced7.svg](./img/cvd4kmvse0ynfuoc/1709005323792-d812aa7e-3d4b-4c2c-897c-cf736274ced7-841717.svg)![1709005323791-3d804aa7-4a97-4907-b2f7-ca0f8704ab89.svg](./img/cvd4kmvse0ynfuoc/1709005323791-3d804aa7-4a97-4907-b2f7-ca0f8704ab89-355896.svg)![1709005324367-72c7accf-99cc-45ba-bb0c-0f98e839fc57.png](./img/cvd4kmvse0ynfuoc/1709005324367-72c7accf-99cc-45ba-bb0c-0f98e839fc57-902265.png)

## 简介
dynamic-datasource-spring-boot-starter 是一个基于springboot的快速集成多数据源的启动器。

其支持 **Jdk 1.7+, SpringBoot 1.5.x 2.x.x 3.x.x**。

## 特性
+ 支持 **数据源分组** ，适用于多种场景 纯粹多库 读写分离 一主多从 混合模式。
+ 支持数据库敏感配置信息 **加密(可自定义)** ENC()。
+ 支持每个数据库独立初始化表结构schema和数据库database。
+ 支持无数据源启动，支持懒加载数据源（需要的时候再创建连接）。
+ 支持 **自定义注解** ，需继承DS(3.2.0+)。
+ 提供并简化对Druid，HikariCp，BeeCp,Dbcp2的快速集成。
+ 提供对Mybatis-Plus，Quartz，ShardingJdbc，P6sy，Jndi等组件的集成方案。
+ 提供 **自定义数据源来源** 方案（如全从数据库加载）。
+ 提供项目启动后 **动态增加移除数据源** 方案。
+ 提供Mybatis环境下的 **纯读写分离** 方案。
+ 提供使用 **spel动态参数** 解析数据源方案。内置spel，session，header，支持自定义。
+ 支持 **多层数据源嵌套切换** 。（ServiceA >>> ServiceB >>> ServiceC）。
+ 提供 **基于seata的分布式事务方案** 。
+ 提供 **本地多数据源事务方案。**

## 约定
1. 本框架只做 **切换数据源** 这件核心的事情，并**不限制你的具体操作**，切换了数据源可以做任何CRUD。
2. 配置文件所有以下划线 _ 分割的数据源 **首部** 即为组的名称，相同组名称的数据源会放在一个组下。
3. 切换数据源可以是组名，也可以是具体数据源名称。组名则切换时采用负载均衡算法切换。
4. 默认的数据源名称为 **master** ，你可以通过 spring.datasource.dynamic.primary 修改。
5. 方法上的注解优先于类上注解。
6. DS支持继承抽象类上的DS，暂不支持继承接口上的DS。

## 使用方法
1. 引入dynamic-datasource-spring-boot-starter。

**spring-boot 1.5.x 2.x.x**

```plain
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>dynamic-datasource-spring-boot-starter</artifactId>
  <version>${version}</version>
</dependency>
```

复制

**spring-boot3**

```plain
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>dynamic-datasource-spring-boot3-starter</artifactId>
  <version>${version}</version>
</dependency>
```

复制

1. 配置数据源。

```plain
spring:
  datasource:
    dynamic:
      primary: master #设置默认的数据源或者数据源组,默认值即为master
      strict: false #严格匹配数据源,默认false. true未匹配到指定数据源时抛异常,false使用默认数据源
      datasource:
        master:
          url: jdbc:mysql://xx.xx.xx.xx:3306/dynamic
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver # 3.2.0开始支持SPI可省略此配置
        slave_1:
          url: jdbc:mysql://xx.xx.xx.xx:3307/dynamic
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver
        slave_2:
          url: ENC(xxxxx) # 内置加密,使用请查看详细文档
          username: ENC(xxxxx)
          password: ENC(xxxxx)
          driver-class-name: com.mysql.jdbc.Driver
       #......省略
       #以上会配置一个默认库master，一个组slave下有两个子库slave_1,slave_2
```

复制

```plain
# 多主多从                      纯粹多库（记得设置primary）                   混合配置
spring:                               spring:                               spring:
  datasource:                           datasource:                           datasource:
    dynamic:                              dynamic:                              dynamic:
      datasource:                           datasource:                           datasource:
        master_1:                             mysql:                                master:
        master_2:                             oracle:                               slave_1:
        slave_1:                              sqlserver:                            slave_2:
        slave_2:                              postgresql:                           oracle_1:
        slave_3:                              h2:                                   oracle_2:
```

复制

1. 使用 **@DS** 切换数据源。

**@DS** 可以注解在方法上或类上，**同时存在就近原则 方法上注解 优先于 类上注解**。

| 注解 | 结果 |
| :---: | :---: |
| 没有@DS | 默认数据源 |
| @DS("dsName") | dsName可以为组名也可以为具体某个库的名称 |


```plain
@Service
@DS("slave")
public class UserServiceImpl implements UserService {

  @Autowired
  private JdbcTemplate jdbcTemplate;

  public List selectAll() {
    return  jdbcTemplate.queryForList("select * from user");
  }
  
  @Override
  @DS("slave_1")
  public List selectByCondition() {
    return  jdbcTemplate.queryForList("select * from user where age >10");
  }
}
```

复制  


> 来自: [基础必读（免费） · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2264611)
>



> 更新: 2024-02-27 11:42:14  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/dmzptikqlfren375>