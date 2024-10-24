# 事务常见问题 · dynamic-datasource · 看云

+ [原生Spring@Transational能和@DS一起使用吗？](https://www.kancloud.cn/tracy5546/dynamic-datasource/2311847#SpringTransationalDS_8)
+ [本地事务和Spring原生事务不能混用是什么意思？](https://www.kancloud.cn/tracy5546/dynamic-datasource/2311847#Spring_24)
+ [本地事务标准使用。](https://www.kancloud.cn/tracy5546/dynamic-datasource/2311847#_63)

建议先阅读基础知识。

核心知识： **spring原生事务会保证在事务下整个线程后续拿到的都是同一个connection。**  
核心知识： **spring原生事务会保证在事务下整个线程后续拿到的都是同一个connection。**  
核心知识： **spring原生事务会保证在事务下整个线程后续拿到的都是同一个connection。**

## 原生Spring@Transational能和@DS一起使用吗？
```plain
public class AService {
    
    @DS("a")
    @Transational
    public void dosomething(){
    //some code
    }
}
```

复制

可以的，其会先切换使用数据源a再开启事务，整个原生事务内部不管是注解切换还是手动调用代码切换都不能切换，会一直使用a数据源。

所以确认整个事务下不再切换其他数据源，用原生 @Transational 是建议的，毕竟其更完善。

## 本地事务和Spring原生事务不能混用是什么意思？
场景一：先使用的Spring@Transational 内部方法调用了本地@DSTransactional。

```plain
public class AService {
    
    @DS("a")
    @Transational
    public void dosomething(){
        Bservice.dosomething(); //B是另外数据源，然后注解了@DS("b")和@DSTransactional
        Cservice.dosomething(); //C是另外数据源，然后注解了@DS("c")和@DSTransactional
    }
}
```

复制

基于核心知识： 整理事务下都是a数据源，内部无论做什么都改变不了使用a数据源。

场景二： 先使用的本地@DSTransactional内部方法调用了Spring@Transational 。

```plain
public class AService {
    
    @DS("a")
    @DSTransactional
    public void dosomething(){
        Amapper.updateSomeThing();
        Bservice.dosomething(); //B是另外数据源，然后注解了@DS("b")和@Transational
        Cservice.dosomething(); //C是另外数据源，然后注解了@DS("b")和@Transational
    }
}
```

复制

B和C都是独立的事务。C发生错误B会回滚吗？不会B已经提交了。A会回滚吗？会。

不建议混用，除非你确实非常理解事务，能随心所欲掌握你代码的执行流程。

---

## 本地事务标准使用。
请结合阅读本地事务章节。

```plain
public class AService {
    
    @DS("a")
    @DSTransactional//最外层开启即可
    public void dosomething(){
        Amapper.updateSomeThing();
        Bservice.dosomething(); //B是另外数据源，然后注解了@DS("b")
        Cservice.dosomething(); //C是另外数据源，然后注解了@DS("b")
    }
}
```

复制  


> 来自: [事务常见问题 · dynamic-datasource · 看云](https://www.kancloud.cn/tracy5546/dynamic-datasource/2311847)
>



> 更新: 2024-02-27 11:51:51  
> 原文: <https://www.yuque.com/janeyork/dynamic-datasource/mrdizlehhogbyhza>