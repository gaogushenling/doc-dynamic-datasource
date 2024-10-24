# mybatis下读写分离 · dynamic-datasource · 看云

## 自动读写分离
场景：

1. 在纯的读写分离环境，写操作全部是master，读操作全部是slave。
2. 不想通过注解配置完成以上功能。

答：在mybatis环境下可以基于mybatis插件结合本数据源完成以上功能。

手动注入插件。

```plain
@Bean
public MasterSlaveAutoRoutingPlugin masterSlaveAutoRoutingPlugin(){
    return new MasterSlaveAutoRoutingPlugin();
}
```

复制

默认主库名称master,从库名称slave。

暂不支持更多，源码简单可参考重写。

```plain
@Intercepts({@Signature(type = Executor.class, method = "query", args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class}),
        @Signature(type = Executor.class, method = "update", args = {MappedStatement.class, Object.class})})
@Slf4j
public class MasterSlaveAutoRoutingPlugin implements Interceptor {

    private static final String MASTER = "master";

    private static final String SLAVE = "slave";

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        Object[] args = invocation.getArgs();
        MappedStatement ms = (MappedStatement) args[0];
        try {
            DynamicDataSourceContextHolder.push(SqlCommandType.SELECT == ms.getSqlCommandType() ? SLAVE : MASTER);
            return invocation.proceed();
        } finally {
            DynamicDataSourceContextHolder.clear();
        }
    }

    @Override
    public Object plugin(Object target) {
        return target instanceof Executor ? Plugin.wrap(target, this) : target;
    }

    @Override
    public void setProperties(Properties properties) {
    }
}
```

复制  


> 来自: [mybatis下读写分离 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2358946)
>



> 更新: 2024-02-27 11:50:56  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/dw2h6pufe60ss1rv>