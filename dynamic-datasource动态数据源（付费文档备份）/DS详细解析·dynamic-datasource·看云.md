# DS详细解析 · dynamic-datasource · 看云

# DS详细解析
复制链接

腾讯QQ

新浪微博

微信扫一扫

![]()

    - [问：DS放在哪里合适？](https://www.kancloud.cn/tracy5546/dynamic-datasource/2398947#DS_1)
        * [注解在Controller的方法上或类上](https://www.kancloud.cn/tracy5546/dynamic-datasource/2398947#Controller_9)
        * [注解在service的实现类的方法或类上](https://www.kancloud.cn/tracy5546/dynamic-datasource/2398947#service_13)
        * [注解在mapper上](https://www.kancloud.cn/tracy5546/dynamic-datasource/2398947#mapper_17)
    - [其他使用方式](https://www.kancloud.cn/tracy5546/dynamic-datasource/2398947#_21)
        * [继承抽象类上的DS](https://www.kancloud.cn/tracy5546/dynamic-datasource/2398947#DS_23)
        * [继承接口上的DS](https://www.kancloud.cn/tracy5546/dynamic-datasource/2398947#DS_28)

## 问：DS放在哪里合适？
DS作为切换数据源核心注解，我应该把他注解在哪里合适？这其实是初次接触多数据源的人常问的问题。 这其实没有一定的要求，只是有一些经验之谈。

---

首先开发者要了解的基础知识是，DS注解是基于AOP的原理实现的，aop的常见失效场景应清楚。 比如内部调用失效，shiro代理失效。 具体见切换数据源失败。

作者通常建议DS放在serviceImpl的方法上，如事务注解一样。

### 注解在Controller的方法上或类上
并不是不可以，作者并不建议的原因主要是controller主要作用是参数的检验等一些基础逻辑的处理，这部分操作常常并不涉及数据库。

### 注解在service的实现类的方法或类上
这是作者建议的方式，service主要是对业务的处理， 在复杂的场景涉及连续切换不同的数据库。 如果你的方法有通用性，其他service也会调用你的方法。 这样别人就不用重复处理切换数据源。

### 注解在mapper上
通常如果你某个Mapper对应的表只在确定的一个库，也是可以的。 但是建议只注解在Mapper的类上。

## 其他使用方式
### 继承抽象类上的DS
比如我有一个抽象Service,我想实现继承我这个抽象Service下的子Service的所有方法除非重新指定，都用我抽象Service上注解的数据源。 是否支持？

答：支持。

### 继承接口上的DS
3.4.1开始支持， 但是需要注意的是，一个类能实现多个接口，如果多个接口都有DS会如何？

不知道，别这么干。。。。

上一篇：[基础必读（免费）](https://www.kancloud.cn/tracy5546/dynamic-datasource/2264611)下一篇：[连接池集成](https://www.kancloud.cn/tracy5546/dynamic-datasource/2270655)  


> 来自: [DS详细解析 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2398947)
>



> 更新: 2024-02-27 11:42:36  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/gefftmfmvcz8gmuu>