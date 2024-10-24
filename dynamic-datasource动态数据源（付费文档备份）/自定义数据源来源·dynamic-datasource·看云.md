# 自定义数据源来源 · dynamic-datasource · 看云

## 自定义数据源来源
## 基础介绍
数据源来源的默认实现是YmlDynamicDataSourceProvider，其从yaml或properties中读取信息并解析出所有数据源信息。

```plain
public interface DynamicDataSourceProvider {

    /**
     * 加载所有数据源
     *
     * @return 所有数据源，key为数据源名称
     */
    Map<String, DataSource> loadDataSources();
}
```

复制

## 自定义示例
可以参考 AbstractJdbcDataSourceProvider （仅供参考）来实现从JDBC数据库中获取数据库连接信息。

```plain
@Bean
public DynamicDataSourceProvider jdbcDynamicDataSourceProvider() {
    return new AbstractJdbcDataSourceProvider("org.h2.Driver", "jdbc:h2:mem:test", "sa", "") {
        @Override
        protected Map<String, DataSourceProperty> executeStmt(Statement statement) throws SQLException {
            ResultSet rs = statement.executeQuery("select * from DB");
            while (rs.next()) {
                String name = rs.getString("name");
                String username = rs.getString("username");
                String password = rs.getString("password");
                String url = rs.getString("url");
                String driver = rs.getString("driver");
                DataSourceProperty property = new DataSourceProperty();
                property.setUsername(username);
                property.setPassword(password);
                property.setUrl(url);
                property.setDriverClassName(driver);
                map.put(name, property);
            }
            return map;
        }
    };
}
```

复制

PS: 从3.4.0开始，可以注入多个DynamicDataSourceProvider的Bean以实现同时从多个不同来源加载数据源，注意同名会被覆盖。  


> 来自: [自定义数据源来源 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2268603)
>



> 更新: 2024-02-27 11:49:29  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/ywi6wa7d1uz2t2uo>