# 知识库 · dynamic-datasource · 看云

## 知识库
复制链接

腾讯QQ

新浪微博

微信扫一扫

![]()

+ [如何注入多数据源？](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304734#_2)
+ [如何获取当前线程数据源名称？](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304734#_16)
+ [如何获取当前线程数据源？](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304734#_22)
+ [DynamicRoutingDataSource 其他常用方法](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304734#DynamicRoutingDataSource__30)

## 如何注入多数据源？
```plain
public class A{
    @Autowired
    private DataSource dataSource;

    public void dosomething{
         //使用的时候需要强转
        DynamicRoutingDataSource ds = (DynamicRoutingDataSource) dataSource;
    }
}
```

复制

## 如何获取当前线程数据源名称？
```plain
DynamicDataSourceContextHolder.peek()
```

复制

## 如何获取当前线程数据源？
```plain
public void dosomething{
    DynamicRoutingDataSource ds = (DynamicRoutingDataSource) dataSource;
    DataSource datasource=ds.determineDataSource();
}
```

复制

## DynamicRoutingDataSource 其他常用方法
![1709005998967-886d9910-c1dd-46c8-97cc-a0efd93bffea.webp](./img/SVHD91ugehFGR7JU/1709005998967-886d9910-c1dd-46c8-97cc-a0efd93bffea-168462.webp)  


> 来自: [知识库 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2304734)
>



> 更新: 2024-02-27 11:53:21  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/in8xbxcrxhokw5l4>