# 调试源码 · dynamic-datasource · 看云

## 调试源码
1. 开启动态数据源的debug日志。

```plain
logging:
  level:
    com.baomidou.dynamic: debug
```

复制

检查日志输出是否正确。

1. 断点调试DynamicDataSourceAnnotationInterceptor。

```plain
public class DynamicDataSourceAnnotationInterceptor implements MethodInterceptor {

    private static final String DYNAMIC_PREFIX = "#";

    private final DataSourceClassResolver dataSourceClassResolver;
    private final DsProcessor dsProcessor;

    public DynamicDataSourceAnnotationInterceptor(Boolean allowedPublicOnly, DsProcessor dsProcessor) {
        dataSourceClassResolver = new DataSourceClassResolver(allowedPublicOnly);
        this.dsProcessor = dsProcessor;
    }

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        try {
            //这里把获取到的数据源标识如master存入本地线程
            String dsKey = determineDatasourceKey(invocation);
        ●  DynamicDataSourceContextHolder.push(dsKey);
            return invocation.proceed();
        } finally {
            DynamicDataSourceContextHolder.poll();
        }
    }

    private String determineDatasourceKey(MethodInvocation invocation) {
        String key = dataSourceClassResolver.findDSKey(invocation.getMethod(), invocation.getThis());
        return (!key.isEmpty() && key.startsWith(DYNAMIC_PREFIX)) ? dsProcessor.determineDatasource(invocation, key) : key;
    }
}
```

复制

1. 断点调试DynamicRoutingDataSource。

```plain
public class DynamicRoutingDataSource extends AbstractRoutingDataSource {

    private Map<String, DataSource> dataSourceMap = new LinkedHashMap<>();

    private Map<String, DynamicGroupDataSource> groupDataSources = new ConcurrentHashMap<>();

    @Override
    public DataSource determineDataSource() {
        //从本地线程获取key解析最终真实的数据源
     ●  String dsKey = DynamicDataSourceContextHolder.peek();
        return getDataSource(dsKey);
    }

    private DataSource determinePrimaryDataSource() {
        log.debug("从默认数据源中返回数据");
        return groupDataSources.containsKey(primary) ? groupDataSources.get(primary).determineDataSource() : dataSourceMap.get(primary);
    }

    public DataSource getDataSource(String ds) {
        if (StringUtils.isEmpty(ds)) {
            return determinePrimaryDataSource();
        } else if (!groupDataSources.isEmpty() && groupDataSources.containsKey(ds)) {
            log.debug("从 {} 组数据源中返回数据源", ds);
            return groupDataSources.get(ds).determineDataSource();
        } else if (dataSourceMap.containsKey(ds)) {
            log.debug("从 {} 单数据源中返回数据源", ds);
            return dataSourceMap.get(ds);
        }
        return determinePrimaryDataSource();
    }
}
```

复制  


> 来自: [调试源码 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2264597)
>



> 更新: 2024-02-27 11:52:42  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/fnnn97bgsi2r1469>