---
layout: post
title:  "类内部调用@Transactional失效原因"
date:   2021-06-16 16:28:00 +0800
author: Vito
categories: ["aop","数据库事务失效"]
---
## AOP类型
1.JDK
2.Cglib


## 类内部调用导致@Transactional注解失效
```
@Service("userService")
public class UserServiceImpl implements UserService {
    
    @Autowired
    UserMapper userMapper;
    
    public void addInfo() {
        addOne();
    }
    
    @Transactional
    public void addOne() {
        User record = new User();
        record.setLoginName("tom");
        record.setPwd("111111");
        record.setMobile("13913913913");
        record.setUsable(1);
        record.setCreateTime(new Date());
        userMapper.insertSelective(record);
        int i = 1/0;    // 测试事物的回滚
    }
}
```
如上所示，外部调用addOne()方法时，数据库插入操作会产生回滚，但是调用addInfo()时，则不会产生回滚，@Transactional失效，这是为什么呢？

### 原因
@Transactional注解，默认是基于spring aop（基于jdk）来进行的aop的，而jdk进行aop的原理，是基于InvocationHandler以及Proxy类来创建原接口的代理类，在SpringApplication运行期间，@Autowired注入时，实际上注入的是proxy代理类，所以我们从外部调用时，会有aop逻辑被执行到，也就是transaction会生效

但是在类内部调用时，此时并没有使用proxy来调用方法，也就不会执行aop的切面逻辑，@Transactional所以就会失效了。
解决方法：
1.使用AspectJ AOP，其支持编译时织入增强且不需要生成代理类，而jdk包括cglib动态代理都是运行时增强。
2.实现一个在ContextRefreshedEvent完成后，把所有实现自动代理织入接口BeanSelfProxyAware的Bean，注入到自身的插件，详见《精通Spring4.X-企业应用开发实战》。
```
public void onReady() {
    Map<String, BeanSelfProxyAware> proxyAwareMap = applicationContext.getBeansOfType(BeanSelfProxyAware.class);
    if (proxyAwareMap != null) {
        for (BeanSelfProxyAware beanSelfProxyAware : proxyAwareMap.values()) {
            beanSelfProxyAware.setSelfProxy(beanSelfProxyAware);
        }
    }
}
```