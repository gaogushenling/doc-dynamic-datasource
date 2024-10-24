# 常见问题之-切换数据源失败 · dynamic-datasource · 看云

    - [1.开启了spring的事务。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733#1spring_2)
    - [2.方法内部调用。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733#2_10)
    - [3.shiroAop代理。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733#3shiroAop_36)
    - [4.PostConstruct初始化顺序。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733#4PostConstruct_56)
    - [5.Druid版本过低。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733#5Druid_93)
    - [6.@Async或者java8的ParallelStream并行流之类方法。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733#6Asyncjava8ParallelStream_97)
    - [扩展阅读](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733#_103)
        * [shiro失效原因](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733#shiro_105)

## 1.开启了spring的事务。
原因： spring开启事务后会维护一个ConnectionHolder，保证在整个事务下，都是用同一个数据库连接。

请检查整个调用链路涉及的类的方法和类本身还有继承的抽象类上是否有@Transactional注解。

如强烈需要事务保证多个库同时执行成功或者失败，请查看事务专栏的解决办法。

## 2.方法内部调用。
查看以下示例 回答 外部调用 userservice.test1() 能在执行到 test2() 切换到second数据源吗？

```plain
public UserService {

    @DS("first")
    public void test1() {
        // do something
         test2();
    }

    @DS("second")
    public void test2() {
        // do something
    }
}
```

复制

答案：**！！！不能不能不能！！！！** 数据源核心原理是基于aop代理实现切换，内部方法调用不会使用aop。

解决方法:

把test2()方法提到另外一个service,单独调用。

## 3.shiroAop代理。
在shiro框架中(UserRealm)使用@Autowire 注入的类, 缓存注解和事务注解都失效。

```plain
@Component
public class UserRealm extends AuthorizingRealm {

    @Lazy
    @Autowired
    private IUserService userService;
	//... 省略其他无关的内容
}
```

复制

解决方法:  
1.在Shiro框架中注入Bean时, 不使用@Autowire, 使用ApplicationContextRegister.getBean()方法,手动注入bean。

2.使用@Autowire+@Lazy注解,设置注入到Shiro框架的Bean延时加载(推荐)。

## 4.PostConstruct初始化顺序。
初始化包括: @PostConstruct 注解, InitializingBean接口, 自定义init-method

```plain
@Component
public class MyConfiguration {
    @Resource
    private UserMapper userMapper;
    @DS("slave")
    @PostConstruct
    public void init(){
        // 无法选择正确的数据源
        userMapper.selectById(1);
    }
}
```

复制

解决方法：监听容器启动完成事件, 在容器完成后做初始化。

```plain
@Component
public class MyConfiguration {

    @DS("slave")
    @EventListener
    public void onApplicationEvent(ContextRefreshedEvent event) {
        // 成功选择正确的数据源
        userMapper.selectById(1);
    }
}
```

复制

相关spring源码 : `org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#initializeBean

在初始化完成后, bean 进入增强阶段, 所以这个阶段的任何AOP都是无效的。

## 5.Druid版本过低。
请升级druid1.1.22及以上版本，这个版本修复了在高并发下偶发的切换失效问题。

## 6.@Async或者java8的ParallelStream并行流之类方法。
这种情况都是新开了线程去处理，不受当前线程管控了。 可以在新开的方法上加对应的DS注解解决。

---

## 扩展阅读
### shiro失效原因
在spring初始化bean的阶段中,大致上分为三段: BeanFactoryPostProcessor -> BeanPostProcessor -> Bean

这三种都是单例bean. 只不过会按照这个优先级进行初始化, shiro引起AOP失效的原因:

ShiroFilterFactoryBean 是一个 BeanPostProcessor , 比普通的单例Bean都优先加载, 所以他依赖注入的bean都无法正确的进行切面。

ShiroFilterFactoryBean 实际的依赖情况:  
ShiroFilterFactoryBean -> SecurityManager -> UserRealm -> IUserService

IUserService依赖的其他 service 也会失效  
IUserService-> MenuService -> RoleService  


> 来自: [常见问题之-切换数据源失败 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304733)
>



> 更新: 2024-02-27 11:52:51  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/onrx0w6dfl5vi0bk>