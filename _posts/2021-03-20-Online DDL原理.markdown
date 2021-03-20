---
layout: post
title:  "Online DDL"
date:   2021-03-20 21:59:19 +0800
author: Vito
categories: MySQL
---
[Online DDL原理](https://www.cnblogs.com/cchust/p/4639397.html)

Online DDL的过程
1. 拿MDL写锁
2. 降级成MDL读锁
3. 真正做DDL
4. 升级成MDL写锁
5. 释放MDL锁

详细版
* Prepare阶段
1. 创建新的临时frm文件
2. 持有EXCLUSIVE-MDL锁，禁止读写
3. 根据alter类型，确定执行方式(copy,online-rebuild,online-norebuild)
4. 更新数据字典的内存对象
5. 分配row_log对象记录增量
6. 生成新的临时ibd文件
* ddl执行阶段
7. 降级EXCLUSIVE-MDL锁，允许读写
8. 扫描old_table的聚集索引每一条记录rec
9. 遍历新表的聚集索引和二级索引，逐一处理
10. 根据rec构造对应的索引项
11. 将构造索引项插入sort_buffer块
12. 将sort_buffer块插入新的索引
13. 处理ddl执行过程中产生的增量(仅rebuild类型需要)
* commit阶段
14. 升级到EXCLUSIVE-MDL锁，禁止读写
15. 重做最后row_log中最后一部分增量
16. 更新innodb的数据字典表
17. 提交事务(刷事务的redo日志)
18. 修改统计信息
19. rename临时idb文件，frm文件
20. 变更完成  