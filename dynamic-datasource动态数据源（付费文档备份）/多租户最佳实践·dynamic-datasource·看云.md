# 多租户最佳实践 · dynamic-datasource · 看云

+ [概述](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791#_2)
+ [要点](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791#_7)
+ [场景](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791#_22)
    - [场景一： 租户少比较固定](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791#__26)
    - [场景二： 租户多但比较固定](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791#__30)
    - [场景三： 租户多且不固定](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791#__38)
    - [场景四： 租户多且不固定且多服务](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791#__47)

## 概述
多租户有多种方案  
[https://zhuanlan.zhihu.com/p/143737554](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791) 。  
我们是多数据源，只支持不同数据库的方案。

## 要点
假设我们设计一个多租户系统，每个城市一个数据库，已有shanghai,chengdu,guangzhou

不同租户如何动态路由到对应数据库？

如果你一个请求下不会涉及到多个租户，建议参考无注解方案里的拦截器。  
用户登录后返回租户信息给前端，前端后续请求都带一个header 如tenant: shanghai。  
拦截器里手动设置数据源，那后续的请求都是shanghai这个库。

如果也会涉及在其他库也没有关系，仍然可以采用上面的方案，在需要切换其他库的时候用@DS 切换即可。

看的出来作者比较倾向无注解，因为总的来说@DS是基于AOP的，开销和代码侵入性都相对来说比拦截器大一些。  
不喜欢拦截器就喜欢@DS的，可以阅读 **进阶使用-动态解析数据源**。

## 场景
每一个场景都是上一个场景的递进。

## 场景一： 租户少比较固定
比如你租户比较固定，可能半年才会有一个新租户，就正常在yml配置即可。

## 场景二： 租户多但比较固定
需要关注的的文档

1. 自定义-自定义数据源来源
2. 进阶使用-无数据源启动

结合以上内容： 我们可以启动的时候不配置数据源，所有数据源动态从数据库中加载出来。减少yml的配置。

## 场景三： 租户多且不固定
有些系统租户需要动态的增减。

需要关注的文档  
进阶使用-动态添加移除数据源

基于以上文档可以在系统运行过程中动态的新增移除数据源。

## 场景四： 租户多且不固定且多服务
在更大型系统，单机多租户已经不能满足运行需求，需要拆分多个服务。  
这个时候动态新增就会遇到一个问题？ 我一个新增数据源请求只会路由到一台服务，怎么让所有机器都新增一个数据源？

需要关注的文档  
进阶使用-手动注入多数据源  
知识库-辅助知识

答案是： 结合拦截器。

有一个表是非租户的主数据库，里面有个表存租户的信息，添加租户的使用就往租户表里插入新租户的相关数据库信息即可。

在拦截器中手动注入多数据源， 前端传来租户的header并解析，比如传来guangzhou。  
我们从多数据源里取出现有所有数据库的名称，判断有没有，没有就去主库里sql查找出来并动态添加到多数据源里。

这样所有机器都会逐渐加载新租户的数据源了。

但是怎么让所有服务都减少一个数据源呢？ 可以尝试结合redis的listener。 所有机器监听一个事件消息。

  


> 来自: [多租户最佳实践 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2395791)
>



> 更新: 2024-02-27 11:53:32  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/dqt5hb8wuapfk6gy>