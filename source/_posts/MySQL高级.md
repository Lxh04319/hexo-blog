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
* 基于**表**
* 默认**InnoDB引擎**
* 创建表的时候指定引擎``engine=InnoDB``

#### 存储引擎特点

* InnoDB
  支持事务、行级锁、外键
  逻辑存储结构：![alt text](https://pic.imgdb.cn/item/65e326f39f345e8d03e11cc0.jpg)
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

* 二叉树缺点：顺序插入容易**形成一个链表，且大数据量下层级很深**
* 红黑树缺点：本质还是二叉树，**大数据量下层级深**
* B树(多路平衡查找树)缺点：叶子节点和非叶子节点均储存数据，导致一页中存储的键值减少，增加树高度
* B+树 均在**叶子节点**上，且添加双向指针

**MySQL里的B+树**
增加了优化
![alt text](https://pic.imgdb.cn/item/65e326ab9f345e8d03dfc3b6.jpg)

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
  * 存在主键 **主键索引**为聚集索引
  * 不存在主键 第一个唯一索引为聚集索引
  * 均没有 自动生成rowid作索引
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
  * 定位sql语句，查看哪一句效率低
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
  * 联合索引遵守最左前缀法则，从最左列开始且不跳过索引中的列，**跳过的话后面的字段索引会失效**
  * 最左索引存在即可，与后面字段位置顺序无关
  * 联合索引如果出现范围查询(>,<)，范围查询右侧的索引失效--**规避：尽量采用大于等于或小于等于**
* 索引失效
  * 在索引列上计算，会导致索引失效
  * 字符串类型字段使用查询不加引号，会导致索引失效
  * 尾部模糊匹配索引不失效，**头部模糊匹配**索引失效
  * or连接：如果or前的列有索引但后面的列没有，所涉及的索引均失效。**两侧均有索引**才会有效
  * 数据分布情况：如果评估全表扫描比索引效率高，则不使用索引
* SQL提示
  * 加入人为的提示来优化，在from 表名后指定
  ``use index`` 使用某个索引
  ``ignore index``  不使用某个索引
  ``force index``  强制使用某个索引
  * use只是建议mysql使用该索引，最终使用哪个**取决于mysql的判断**
* 覆盖索引
  * 尽量不写``select *``，容易产生回表查询(二级索引没覆盖到需要查询的字段，需要根据返回的id回查聚集索引)，性能低
  * 查询性能extra字段：
    ![alt text](https://pic.imgdb.cn/item/65e312039f345e8d038bf803.png)
* 前缀索引
  * 字段类型为字符串或大文本时需要索引很长的字符串--只将一部分前缀建立索引
  * ``create index idx_xxx on table table_name(column(n));``n为截取前缀长度
  * 长度的选择：根据不重复的索引值与数据表总记录数的**比值**(选择性)决定
* 联合索引
  * 如果有多个查询条件，考虑根据查询字段尽力联合索引，避免回表查询
  * 创建联合索引要**考虑字段顺序**--参考最左前缀法则

#### 索引设计原则

* ![alt text](https://pic.imgdb.cn/item/65e321d79f345e8d03c9457e.jpg)
* ![alt text](https://pic.imgdb.cn/item/65e322199f345e8d03ca4c62.jpg)
* ![alt text](https://pic.imgdb.cn/item/65e3223c9f345e8d03cad74d.jpg)

### SQL优化

#### 插入数据

插入多条数据优化

* 批量插入
  大批量数据采用insert插入性能低，可以采用mysql提供的**load指令**插入

  ```sql
  -- 客户端连接服务端时，加上参数 -–local-infile
  mysql –-local-infile -u root -p
  -- 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
  set global local_infile = 1;
  -- 执行load指令将准备好的数据，加载到表结构中
  load data local infile '/root/sql1.log' into table tb_user fields
  terminated by ',' lines terminated by '\n' ;
   ```

* 手动事务提交
  
  ```sql
  start transaction;
  insert into... values...;
  insert...;
  commit;
  ```

* 主键顺序插入

#### 主键优化

* InnoDB中表数据是根据主键顺序存放的
* **页分裂，页合并**：主键乱序插入时可能会造成页分裂，删除可能会造成页合并
* 主键设计原则
  * 尽量降低主键长度
  * 尽量**顺序插入**
  * 尽量不使用UUID或自然主键如身份证号作主键

#### order by优化

* extra字段
  * using filesort
  通过表的索引或全表扫描，在排序缓冲区完成排序
  * using index
  通过有序索引扫描直接返回，不需要额外排序
  前提：使用覆盖索引
* 优化
  * 尽量使用**有序索引**扫描，且**使用覆盖索引**
  * 使用不了有序索引--大数据情况下，可以增大排序缓冲区大小

#### group by优化

* 优化
建立适当索引提高分组效率(联合索引)
满足最左前缀法则

#### limit优化

* 优化
  * 数据量大的情况下，使用limit分页查询很慢，使用orderby排序查询后再查询，降低查询时间
  * 创建**覆盖索引+子查询**
    ``select * from tb1 a,(select id from tb2 order by limit 10000000,10) b where a.id=b.id;``
    说明：直接进行子查询，包含了limit字段会报错，所以采用多表查询，将子查询得到的结果视为一张表，进行多表查询

#### count优化

* count()字段说明
  ![alt text](https://pic.imgdb.cn/item/65e349e99f345e8d03863358.jpg)
* 优化
  效率：字段<主键<1≈*(不需要取值则效率高)
  尽量使用``count(*)``
  
#### update优化

* 优化
  * 根据索引字段更新
  * InnoDB行锁针对索引加锁，如果索引失效或不使用该索引，就会从行锁升级为表锁，并发性能降低

### 视图/存储过程/触发器

#### 视图

##### 介绍

* 一种虚拟存在的表，不存储数据，保存sql语句，数据均在基表中
* 语法
  * 创建
  ``CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [CASCADED | LOCAL ] CHECK OPTION ]
  ``
  * 查询
  ``
  查看创建视图语句：SHOW CREATE VIEW 视图名称;
  查看视图数据：SELECT * FROM 视图名称 ...... ;
  ``
  * 修改
  ``
  方式一：CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH
  [ CASCADED | LOCAL ] CHECK OPTION ]``
  ``方式二：ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED |LOCAL ] CHECK OPTION ]
  ``
  * 删除
  ``DROP VIEW [IF EXISTS] 视图名称 [,视图名称] ..``

##### 检查选项

* 使用``with check option``创建视图，mysql通过视图检查每个更改行，使其符合视图定义
* 允许基于一个视图创建另一个，检查**依赖视图**中的规则保持一致性
* 检查范围
  * cascaded(默认)
    检查当前视图和其依赖的视图
    ![alt text](https://pic.imgdb.cn/item/65e352589f345e8d03a8cb53.jpg)
  * local
    若依赖视图无条件检查，则不检查
    ![alt text](https://pic.imgdb.cn/item/65e353469f345e8d03ac091a.jpg)

##### 更新

* 更新视图条件：
  视图行与基础表行必须**存在一对一的关系**
* 包含以下则视图不可更新：
  聚合/窗口函数、distinct、group by、having、union
* 作用
  * 简单 简化操作与理解
  * 安全 只查询修改所见到的数据
  * 数据独立 屏蔽真实表结构变化带来的影响

#### 存储过程

#### 存储函数

#### 触发器