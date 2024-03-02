---
title: MySQL高级
date: 2024-3-1
tags:
  - java
description: MySQL索引、SQL优化、存储、视图、锁、引擎等
abbrlink: 10006
categories: 
  - Learn
  - MySQL
---

### 存储引擎

#### MySQl体系结构

![alt text](https://pic.imgdb.cn/item/65e201839f345e8d03418034.png)

#### 存储引擎简介

* 存储数据、建立索引、更新查询数据的实现方式
* 基于表
* 默认InnoDB引擎
* 创建表的时候指定引擎``engine=InnoDB``

#### 存储引擎特点

* InnoDB
  支持事务、行级锁、外键
  逻辑存储结构：![alt text](https://pic.imgdb.cn/item/65e2e23d9f345e8d03ffa8e3.png)
* MyISAM
  支持表锁，不支持行锁、事务、外键
* Memory
  存储在内存中，只能作为临时表或缓存
  hash索引

#### 存储引擎选择

![alt text](https://pic.imgdb.cn/item/65e2e2409f345e8d03ffb27f.png)

#### Linux下安装MySQL

### 索引

#### 索引概述

高效获取数据的数据结构

#### 索引结构

不同的存储引擎有不同的索引结构

* B+Tree索引 大部分引擎都支持
* Hash索引 InnoDB不支持
* R-tree 空间索引
* Full-text 全文索引--倒排索引

##### B+树索引

* 二叉树缺点：顺序插入容易形成一个链表，且大数据量下层级很深
* 红黑树缺点：本质还是二叉树，大数据量下层级深
* B树(多路平衡查找树)缺点：叶子节点和非叶子节点均储存数据，导致一页中存储的键值减少，增加树高度
* B+树 均在叶子节点上，且添加指针

**MySQL里的B+树**
增加了优化
![alt text](https://pic.imgdb.cn/item/65e2e2449f345e8d03ffbd0e.png)

##### Hash索引

通过hash算法计算值映射
InnoDB不支持hash索引，但有自适应功能，指定条件下根据B+索引自动创建
**只支持等值匹配，不支持范围匹配及排序**--所以选择B+树索引

#### 索引分类

* 主键索引
* 唯一索引
* 常规索引
* 全文索引

InnoDB也可以分为两种

* 聚集索引
  * 存在主键 主键索引为聚集索引
  * 不存在主键 第一个唯一索引为聚集索引
  * 均没有 自动省委rowid作索引
* 二级索引
* 回表查询
  ![alt text](https://pic.imgdb.cn/item/65e2e2499f345e8d03ffc98d.png)
  
#### 索引语法

* 创建索引
``create [unique|fulltext] index index_name on table_name (index_col_name,...);``
* 查看索引
``show index from table_name;``
* 删除索引
``drop index index_name on table_name;``

#### SQL性能分析

* 查看SQL执行频率
  ``show global status like 'Com_______'``
  七个下划线
  查看增删改查的频率--查询频率高要进行sql优化
* 慢查询日志
  定位sql语句，查看哪一句效率低
  * 首先在``/etc/my.cnf``中开启慢查询日志
  ``slow_query_log=1 #开启``
  * ``long_query_time=2 #设置超过时间``
  超时则会在慢查询日志中输出
  * 慢查询日志在``/var/lib/mysql/localhost-slow.log``里
  ``tail -f localhost-slow.log``命令可查看慢查询日志尾部信息
* profile详情
  * 打开开关
    ``select @@have_profiling;``
    ``set profiling=1;``
  * 查看
  
    ```sql
    show profiles;
    show profile for query query_id;
    show profile cpu for query query_id;
    ```

* explain执行计划
  * 查询性能
  ``explain select 字段 from 表...``
  * 查询结果字段含义
  ![alt text](https://pic.imgdb.cn/item/65e2e24c9f345e8d03ffd43c.png)

#### 索引使用

* 最左前缀法则
* 索引失效
* SQL提示
* 覆盖索引
* 前缀索引

#### 索引设计原则