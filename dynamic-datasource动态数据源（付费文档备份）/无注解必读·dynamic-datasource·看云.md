# 无注解必读 · dynamic-datasource · 看云

不管是出于注解侵入性还是代码整洁等理由，我们都有需求不想使用注解的需求。

但是需要理解本多数据源实现的核心。

```plain
public class DynamicRoutingDataSource {
@Override
public DataSource determineDataSource() {
    //这里是核心，是从ThreadLocal中获取当前数据源。
    String dsKey = DynamicDataSourceContextHolder.peek();
    return getDataSource(dsKey);
}
```

复制

所以我们就可以根据需求，选择合适的时机调用DynamicDataSourceContextHolder.push("对应数据源")。

默认的@DS注解来切换数据源是根据spring AOP的特性，在方法开启前设置数据源KEY，方法执行完成后移除对应数据源KEY。  


> 来自: [无注解必读 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2327437)
>



> 更新: 2024-02-27 11:50:10  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/dqgn31rxwzwk9osm>