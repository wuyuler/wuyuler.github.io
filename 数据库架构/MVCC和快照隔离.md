# MVCC

参考：

[深入理解MySQL中的Redo、Undo、MVCC](https://www.itdks.com/Home/Course/detail?id=1357)

[【MySQL（5）| 五分钟搞清楚 MVCC 机制】](https://juejin.cn/post/6844903778026536968)

本地文件：

"D:\jianguoyun\研究生事务\学习资料\mysql\Redo Undo MVCC.pdf"

《MySQL数据库InnoDB存储引擎Log漫游(1)》

《MySQL数据库InnoDB存储引擎Log漫游(2)》

《MySQL数据库InnoDB存储引擎Log漫游(3)》

《唐成－2016PG大会-数据库多版本实现内幕》

## MVCC实现效果

MVCC，Multi-Version Concurrency Control, 多版本并发控制。MVCC是一种并发控制的方法。

什么是并发控制？有人从数据库读的同时，有另外的人写数据，可能读数据的人会看到半写或不一致的数据，解决这一问题的方法就叫并发控制方法。

最简单的并发控制方法就是加锁，读的人等写的人结束才能读。显而易见，这样效率会很差。

MVCC使用不同的办法，每个连接到数据库的读者，**在某个瞬间看到的是数据库的一个快照（Snapshot）**，写者写操作在完成前，对于其他读者是不可见的。

## MVCC实现原理

MVCC在更新数据时不会直接用新数据覆盖旧数据，而是给新数据一个**标记（时间戳或事务ID）**，表示过时，在其他地方添加新数据。这样会存储多个版本的数据，但有一个是**最新**的。

这种处理可以让用户读取到在他读的时间点最新的数据，在他读的过程中，数据的修改可以正常进行， 不影响在读的用户。

当然这种处理需要定时的清理删除老的过时的数据。

MVCC下的**读事务**一般要携带时间戳或事物ID去标记当前读的数据库的状态（版本）。这种状态称为时点（point in time）一致性视图。读取这个版本的数据。读、写事务相互隔离，不需要加锁。

读写并存时，写操作会根据目前数据库的状态，创建一个新版本，并发的读依旧访问旧版本的数据。

## Mysql MVCC的抽象实现

mysql建表时，每个表有三列隐藏记录，其中和MVCC有关的有两列

- 数据行的版本号 （DB_TRX_ID）6字节
- 回滚段指针 (DB_ROLL_PT) 7字节
  - 从翻译可以推测其内涵，回滚操作就是将修改撤销，涉及增删改
    - 增 直接对应事务ID删除即可
    - 删 将与待回滚事务ID相同的回滚段指针置空，表示撤销删除
    - 改 以上两个操作的组合

| id   | test_id | DB_TRX_ID | DB_ROLL_PT |
| ---- | ------- | --------- | ---------- |
|      |         |           |            |

DB_ROLL_PTR:

![img](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20210316193154.jpeg)

### 插入

在插入数据的时候，假设系统的全局事务ID从1开始，以下SQL语句执行分析参考注释信息：

```sql
begin;-- 获取到全局事务ID
insert into `test_zq` (`id`, `test_id`) values('5','68');
insert into `test_zq` (`id`, `test_id`) values('6','78');
commit;-- 提交事务
```

执行完以上SQL语句，表格内容会变成：

| id   | test_id | DB_TRX_ID | DB_ROLL_PT |
| ---- | ------- | --------- | ---------- |
| 5    | 68      | 1         | NULL       |
| 6    | 78      | 1         | NULL       |

可以看到，插入的过程中会把全局事务ID记录到列 DB_TRX_ID 中去

### 删除

对上述表格做删除逻辑，执行以下SQL语句（假设获取到的事务逻辑ID为 3）

```sql
begin；--获得全局事务ID = 3
delete test_zq where id = 6;
commit;
```

执行完上述SQL之后数据并没有被真正删除，而是对删除版本号做改变，如下所示：

| id   | test_id | DB_TRX_ID | DB_ROLL_PT |
| ---- | ------- | --------- | ---------- |
| 5    | 68      | 1         | NULL       |
| 6    | 78      | 1         | 3          |

### 修改

修改逻辑和删除逻辑有点相似，修改数据的时候 会先复制一条当前记录行数据，同事标记这条数据的数据行版本号为当前是事务版本号，最后把原来的数据行的删除版本号标记为当前事务。

```sql
begin;-- 获取全局系统事务ID 假设为 10
update test_zq set test_id = 22 where id = 5;
commit;
```

执行完后：

| id   | test_id | DB_TRX_ID | DB_ROLL_PT |
| ---- | ------- | --------- | ---------- |
| 5    | 68      | 1         | 10         |
| 6    | 78      | 1         | 3          |
| 5    | 22      | 10        | NULL       |

### 查询

查询规则：

* **数据行版本号**要小于等于当前事务的版本号，确保读取到的是事务开始前已经存在的，或自身事务改变过的数据
* **删除版本号**要么为NULL，要么大于当前事务的版本号，

```sql
begin;-- 假设拿到的系统事务ID为 12
select * from test_zq;
commit;
```

执行结果:

| id   | test_id | DB_TRX_ID | DB_ROLL_PT |
| ---- | ------- | --------- | ---------- |
| 6    | 22      | 10        | NULL       |

## 引用undo redo日志进一步看到MVCC实现

### 修改操作

![image-20210129105720624](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20210129151231.png)

进行查询时，根据mysql判断当前最新记录可能不是自己应该看到的，那就根据DB_ROLL_PTR去undo日志查找以前的数据

### 事务比较

很多人理解为根据事务ID进行比较，其实并不是那么简单的

假定一下场景：

在RR隔离级别（可重复读）：

| session1                        | session2                        |
| ------------------------------- | ------------------------------- |
| Start transaction;// trx_id=100 |                                 |
| Select c1 from t ; return 2     | Start transaction;// trx_id=101 |
|                                 | Select c1 from t ; return 2     |
| Insert into t values (3);       |                                 |
|                                 | Select c1 from t; return 2      |
| Commit;                         |                                 |
|                                 | Select c1 from t; return 2      |

如果简单通过事务ID判断可见性, 那session1插入的3，是可以被session2看到，但在RR隔离级别下我们知道是不会被看见的

当事务开始时，会根据当前活跃的事务构造一个列表（Read View），这个集合中存储事务ID，会有一个最大的事务ID和一个最小的事务ID。当我们查询到某一条记录时根据行记录上的TRX_ID与Read View中的**最大TRX_ID**，**最小 TRX_ID**比较来判断是否可见

1.比较TRX_ID 是否< read view中的最小TRX_ID

a) 如果是则说明此事务早于read view中的所有事务结束则可以输出返回

b) 如果否则判断TRX_ID 是否 > 最大TRX_ID

* 如果是则根据行上的回滚指针找到回滚段中的对应的记录取出TRX_ID 赋值给当前TRX_ID并执行步骤1
* 如果否则判断TRX_ID是否在read view中
  * 如果是则根据行上的回滚指针找到回滚段中的对应的记录取出TRX_ID
  * 如果否则返回记录

### 针对不同隔离级别Read View的创建

* Read-Committed

该隔离级别每条语句都创建一个Read view，自然就会出现上面的案例中前后读取同一条记录，结果不同的情况，也就是不可重复读

* Repeatable read

这一隔离级别解决的不可重复读，主要通过在事务一开始就创建Read View，在事务结束这段时间查询不会重建Read View，从而实现可重复读

 

这里需要注意的是，`读未提交`隔离级别下直接返回记录上的最新值，没有视图概念，也就是图中丙丙那一栏，脏读，幻读，不可重复读都有可能发生。

而`串行化`隔离级别下直接用加锁的方式来避免并行访问。

# 快照隔离

参考：

[PolarDB-X 强一致分布式事务原理 - 知乎](https://zhuanlan.zhihu.com/p/329978215)

[事务隔离级别和MVCC的关系 - 知乎](https://zhuanlan.zhihu.com/p/150212956)

[MVCC事务机制：Snapshot Isolation | NoSQL漫谈](http://www.nosqlnotes.com/technotes/mvcc-snapshot-isolation/)

## 隔离性级别发展历史

隔离级别的发展比较有故事性，不同数据库产商有各自的实现，缺乏统一的标准。

1. 在1992年ANSI首先尝试指定统一的隔离级别标准，当时主要是**基于锁机制**来实现事务的并发控制隔离，定义了脏读、不可重复读、幻读的异像，ANSI 92标准中指出可以解决这三类异像的事务隔离称之为"可串行化"。
2. 在1995年，微软的Jim Gray研究员们在《A Critique of ANSI SQL Isolation Levels》论文中批判了ANSI标准，新增了**Lost Update（更新丢失）、Read/Write Skew(读写偏序)**的异像场景，同时正式提出基于MVCC的**快照隔离级别SI**。 而当时的Oracle是第一个在商业数据库中应用MVCC的技术，将其基于MVCC的SI隔离技术称之为"可串行化"，主要是因为ANSI 92标准中对解决不可重复读、幻读异常定义的技术称之为可串行化，属于特定的文字游戏。
3. 在1999年，在《Generalized Isolation Level Definitions》论文中提出了有向串行图DSG(Direct Serialization Graph)的隔离定义，可以在SI隔离级别的基础上解决Read/Write Skew(读写偏序)的异像问题，称之为SSI(Serializable Snapshot Isolation)，主要的代表数据库为PostgreSQL/CockroachDB。

## 四种隔离性级别

* 读未提交
  * 一个事务未提交，它的提交可以被其他事务看见
* 读已提交
  * 一个事务已提交，它的提交才能被其他事务看见
* 可重复读
  * 一个事物执行中看到的数据总是跟这个事务开始时看到的该数据相同
  * 在可重复度的隔离级别，未提交的变更对其他事务也是不可见的
* 串行化
  * 对同一行记录，写会加写锁，读会加读锁，当出现读写锁冲突，后访问的事务要等待前一个事务执行完成

## 快照隔离原理

* 每个数据多个版本，读写能够并发进行
* 每个事务相当于看见数据的一个快照
* 写写不能并发，写写操作需要行锁
* 谁加行锁谁可以顺利执行

快照隔离存在**写偏序问题**，因此无法达到串行化的事务隔离级别

写偏序

两个并行事务都基于自己读取的数据与覆盖另外一部分数据，在串行化情况下两个事务无论何种先后顺序，最终将达到一致状态，在SI隔离级别下无法保证一致性。

```
数据库约束： A1+A2>0
A1，A2 实际值都为100
事务T1：
If （read （A1） +read （A2） >= 200）
{
Set A1 = A1 – 200
}
事务T2：
If （read （A1） +read （A2） >= 200）
{
Set A2 = A2 – 200
}
```

事务T2 与事务T1 并发执行相同的语句，两个事务都会执行，执行成功后A1= -100 ，A2= -100 ， A1+A2=-200，显然违背完整性约束。