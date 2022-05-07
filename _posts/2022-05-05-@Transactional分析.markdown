---
layout: post
title:  "Spring @Transactional分析"
date:   2022-05-15 18:00:00 +0800
author: Vito
categories: ["Spring","@Transactional"]
---
## 概念
在使用Spring，Springboot的业务代码中，作为一个精致的crub boy，事务开发是不可避免的，一般来说，有两种写事务的方式，他们的优缺点都非常明显。
1. 使用@Transactional注解来进行标注
优点：代码比较简洁，因为spring框架进行了较好的切面封装
缺点：如果对该注释的原理了解不深，容易导致各种各样隐蔽的bug，例如事务注释失效，事务隔离级别，传递性设置错误等等情况
2. 在代码中显示编写事务调用，代码如下图

```
```

@Transactional


### 参考资料
https://www.1024sou.com/article/8523.html
