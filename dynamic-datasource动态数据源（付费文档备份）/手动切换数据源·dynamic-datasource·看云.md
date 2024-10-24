# 手动切换数据源 · dynamic-datasource · 看云

在某些情况您可能需要手动切换数据源。

```plain
import com.baomidou.dynamic.datasource.toolkit.DynamicDataSourceContextHolder;

@Service
public class UserServiceImpl implements UserService {
  @Autowired
  private JdbcTemplate jdbcTemplate;

  public List selectAll() {
    DynamicDataSourceContextHolder.push("slave");//手动切换
    return  jdbcTemplate.queryForList("select * from user");
  }
 
}
```

复制

需要注意的是手动切换的数据源，最好自己在合适的位置 调用DynamicDataSourceContextHolder.clear()清空当前线程的数据源信息。  
如果你不太清楚什么时候调用，那么可以参考下面写一个拦截器，注册进spring里即可。

```plain
@Slf4j
public class DynamicDatasourceClearInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
//入口处清空看个人，有的新人在异步里切了数据源但是忘记清除了，下一个请求遇到这个线程就会带进来。
        //DynamicDataSourceContextHolder.clear(); 
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) {
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        DynamicDataSourceContextHolder.clear();
    }

}
```

复制  


> 来自: [手动切换数据源 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2273419)
>



> 更新: 2024-02-27 11:48:52  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/ec5qm84p5ytokxis>