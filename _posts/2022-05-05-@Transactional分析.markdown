---
layout: post
title:  "Spring @Transactional分析"
date:   2022-05-15 18:00:00 +0800
author: Vito
categories: ["Spring","@Transactional"]
---
## 两种编写事务的方式
在使用Spring，Springboot的业务代码中，作为一个精致的crub boy，事务开发是不可避免的，一般来说，有两种写事务的方式，他们的优缺点都非常明显。
1. 使用@Transactional注解来进行标注
优点：代码比较简洁，因为spring框架进行了较好的切面封装
缺点：如果对该注释的原理了解不深，容易导致各种各样隐蔽的bug，例如事务注释失效，事务隔离级别，传递性设置错误，误解commit，rollback时机等等情况
2. 在代码中显示编写事务调用，代码如下图

```
public class Test {
    @Autowired
    private PlatformTransactionManager txnManager;

    @Autowired
    private DataSource dataSource;

    public void doTransaction() {
        DefaultTransactionDefinition txnDef = new DefaultTransactionDefinition();
        txnDef.setIsolationLevel(TransactionDefinition.ISOLATION_READ_UNCOMMITTED);
        txnDef.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
        TransactionStatus transactionStatus = txnManager.getTransaction(txnDef);
        try {
            // do biz logic
            txnManager.commit(transactionStatus);
        } catch (Throwable t) {
            // error handling
            txnManager.rollback(transactionStatus);
        }
    }
}
```
这种方式的缺点很明显，事务逻辑入侵代码，虽然可以通过封装把影响尽量减小，但是相对于@Transactional这种方式来说，还是显得稍微笨重一些，但是由此带来的优势也非常显著，程序员可以非常清楚的知道，代码是什么时候commit，什么时候rollback的，而且会让编程者不由自主的去思考，1.这段业务逻辑应该用什么样的隔离性，传递性，2.什么代码应该放到事务哪，什么东西不应该放进去，从而减少事务的时间，预防可能出现的卡db连接的情况。

其中，使用@Transactional带来问题的一个case，https://www.1024sou.com/article/8523.html 里有非常详尽的源码分析，推荐可以阅读一下，这里就不赘述了，另外这篇文章里的例子，还有一种解决方案是可以通过select的时候用select for update来替代lock。
