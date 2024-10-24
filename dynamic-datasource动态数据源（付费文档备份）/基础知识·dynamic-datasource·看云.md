# 基础知识 · dynamic-datasource · 看云

## 基础知识
### 问：使用了事务如@Transational 无法切换数据源？
答： 是的，本组件是基于springAop的方案来进行多数据源的管理和切换的，要想保证多个库的整体事务则需要分布式事务。

### 问：为什么使用了事务如@Transational就无法切换数据源？
答：开启了事务后，spring事务管理器会保证在事务下整个线程后续拿到的都是同一个connection。

### 问：事务下无法切换数据源我知道了，那我单库的事务的可以用吗？
答：完全可以的。 只要事务下不切换数据源就OK。

### 问：我的业务必须要保证事务，还有什么解决办法？
方案一：seata就是解决此问题，本组件已完成和seata的自动集成。

方案二：本组件从3.3.0开始支持本地多数据源事务，无需第三方。

查看左侧边栏对应的详细文档。

### 还有没有其他方案？
答： 如果数据源只有几个，又不怕复杂，可以放弃本组件。

尝试手动构建数据源结合JTA方案 如 [https://www.cnblogs.com/cicada-smile/p/13289306.html。](https://www.cnblogs.com/cicada-smile/p/13289306.html%E3%80%82)

本文不做详解，如果连接失效或者还是不清楚可以自行各渠道搜索 _**多数据源JTA**_。

另外建议不理解或不清楚分布式事务的同学，自行先通过搜索引擎 _**分布式事务**_ 和 _**CAP定理**_ 学习相关概念。

程序员小灰的分布式事务 [https://blog.csdn.net/bjweimengshu/article/details/79607522](https://blog.csdn.net/bjweimengshu/article/details/79607522)

程序员小灰的CAP定理 [https://blog.csdn.net/bjweimengshu/article/details/79338859](https://blog.csdn.net/bjweimengshu/article/details/79338859)  


> 来自: [基础知识 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268605)
>



> 更新: 2024-02-27 11:51:19  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/qiotm3fmxy4ztk1k>