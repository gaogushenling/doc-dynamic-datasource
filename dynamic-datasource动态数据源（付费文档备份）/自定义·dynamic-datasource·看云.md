# 自定义 · dynamic-datasource · 看云

**自定义注解**：不满足于默认DS注解，希望自定义注解。

---

**自定义数据源来源**：默认的数据源来源是基于yml或者properties配置来的，组件支持同时从多个地方来加载初始化数据源。 常用于多租户系统，从一个租户信息库多加载不同租户的数据源。

---

**自定义负载均衡策略**：常用的有轮询，随机。支持扩展。

---

**自定义切面**：支持**不使用注解**，通过配置来切换数据源。  
如某个包下所有service方法以select*开头的都走slave库，以add*开头的都走master库。

---

  


> 来自: [自定义 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2264593)
>



> 更新: 2024-02-27 11:49:13  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/zohmv14lq8chslb2>