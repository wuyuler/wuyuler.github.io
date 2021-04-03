# 目录

[toc]

#  参考文献

[Innodb锁机制：Next-Key Lock 浅谈 - jyzhou - 博客园](https://www.cnblogs.com/zhoujinyi/p/3435982.html)

# 基础知识

## 事务隔离

[【MySQL （三） | 五分钟搞清楚MySQL事务隔离级别】](https://mp.weixin.qq.com/s/WIqoR0-l7h9SObIzmGDatQ)

![img](https://gitee.com/yyjjtt/picture_bed/raw/master/img/640)

修改事务隔离级别命令:

```
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
set session transaction isolation level repeatable read;
```

 

## 锁

[【MySQL （四） | 五分钟搞清楚InnoDB锁机制】](https://mp.weixin.qq.com/s?__biz=MzI1Mzg4OTMxNQ==&mid=100000550&idx=1&sn=8a5cdff008fc1eed7b5c623c1bdf4ed1&chksm=69ccdd6a5ebb547c7b7baf6be78763fc5065e0a58de202f3e25d8d8ed56e6d1c1146332cfec1#rd)

- 共享锁 Shared Locks  （简称 S 锁，属于行锁）
- 排他锁 Exclusive Locks（简称 X 锁，属于行锁）
- 意向共享锁 Intention Shared Locks （简称 IS 锁，属于表锁）
- 意向排他锁 Intention Exclusive Locks （简称 IX 锁，属于表锁）
- 自增锁 AUTO-INC Locks

### 行加锁和表加锁

InnoDB的行锁是通过给索引上的索引项加锁来实现的，只有通过索引条件进行数据检索，Innodb才使用行级锁。否则，将使用表锁（锁住索引的所有记录）。

借此我们是不是能联想到，如果我们的删除/修改语句是没有命中索引的，哪么，则会锁住整个表，这在性能上的影响还是挺大的。

### 意向共享锁和意向排他锁的意义

[详解 MySql InnoDB 中意向锁的作用](https://juejin.im/post/6844903666332368909)

当事务想去进行锁表时，可以先判断意向锁是否存在，存在时则可快速的返回，告知该表不能启用表锁（也就是会锁住对应会话），提高了加锁的效率。

### 自增锁 （AUTO -INC Locks）

[MySQL 的锁机制 - 自增锁 - 知乎](https://zhuanlan.zhihu.com/p/48207652)

该博客里为什么使用自增的小动画比较有意思

[java编程——数据库Mysql自增锁问题原来可以这么解决](https://juejin.im/post/6844903661194182669)

介绍innodb_autoinc_lock_mode参数

[Innodb锁机制探究(一)---自增锁(2) - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1540430)

举例

[MySQL自增主键详解_hxt的博客-CSDN博客](https://blog.csdn.net/qq_40378034/article/details/90736544?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.compare&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.compare)

没看完

## 间隙锁解决幻读

[避免幻读 : next-key锁与MVCC_李兆龙的博客-CSDN博客](https://blog.csdn.net/weixin_43705457/article/details/104849943)

 

## 乐观锁和悲观锁

[面试必备之乐观锁与悲观锁](https://juejin.im/post/6844903639207641096)

[MySQL/InnoDB中，乐观锁、悲观锁、共享锁、排它锁、行锁、表锁、死锁概念的理解_搜云库技术团队 - SegmentFault 思否](https://segmentfault.com/a/1190000015815061)

mvcc提到乐观锁，想知道两者的关系：

知乎上众说纷纭

[(80 封私信 / 85 条消息) 乐观锁和 MVCC 的区别？ - 知乎](https://www.zhihu.com/question/27876575)

这个给出比较抽象的理解

[InnoDB的MVCC是不是乐观锁？ - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/news/305510)

## MVCC

[【MySQL（5）| 五分钟搞清楚 MVCC 机制】](https://juejin.im/post/6844903778026536968)

概括:

> MVCC(`Multiversion concurrency control`) 就是 同一份数据临时保留多版本的一种方式，进而实现并发控制

注意点:

- 在读写并发的过程中如何实现多版本？
- 在读写并发之后，如何实现旧版本的删除（毕竟很多时候只需要一份最新版的数据就够了）？

优势：

MVCC最大的优势：读不加锁，读写不冲突。在读多写少的OLTP应用中，读写不冲突是非常重要的，极大的增加了系统的并发性能

缺点：

不足之处是每行记录都需要额外的存储空间，需要做更多的行检查工作，以及一些额外的维护工作

问题：

1）如果一个事务内连续修改两次，是不是追加两个重复版本号的记录，那怎么分辨

InnoDB为插入一行新记录，保存当前系统版本号作为行版本号，同时保存当前系统版本号到原来的行作为**行删除标识**。

2）MVCC能解决幻读吗？

下面这个博客的评论区比较有用

[MYSQL MVCC实现原理 - 简书](https://www.jianshu.com/p/f692d4f8a53e)

幻读要通过mvcc+Next-Keys Lock实现

# 实验

## 共享锁s锁

会话1

```
start transaction;
select * from t_transaction where id = 1 lock in share mode;
-- 等待会话2
commit;
```

会话2

```
start transaction;
update t_transaction set account=account+200 where id=1;
-- 等待会话2
commit;
```

## 排他锁x锁

会话1

```
start transaction;
select * from t_transaction where id = 1 for update;
-- 等待会话2
commit;
```

会话2

```
start transaction;
select * from t_transaction where id = 1 lock in share mode;--获取s锁失败
-- 等待会话2
commit;
```

## Next-Key locks行锁算法

```
insert into t_transaction (id,account) values(4,100);
insert into t_transaction (id,account) values(7,100);
insert into t_transaction (id,account) values(10,100);
```

会话1

```
start transaction;
select * from t_transaction where id>5 and id<9 for update;
commit;
```

会话2

```
start transaction;
select * from t_transaction where id=4 for update;
select * from t_transaction where id=7 for update;
```

## 快照的存在

```
create table `test_zq` (
    `id` int (11),
    `test_id` int (11)
);
insert into `test_zq` (`id`, `test_id`) values('1','18');
insert into `test_zq` (`id`, `test_id`) values('4','8');
insert into `test_zq` (`id`, `test_id`) values('7','4');
insert into `test_zq` (`id`, `test_id`) values('10','1234');
```

会话1:

```
begin;
-- 更新 id 为 1 的数据
UPDATE test_zq SET test_id = 20 WHERE id = 1;
```

会话2:

```
begin;
--查询 id 为 1 的数据
SELECT * FROM test_zq WHERE id = 1;
```

 

根据事务隔离级别来看，我们理论上对获得 X 锁（关于锁的概念可以参考 [【MySQL （四） | 五分钟搞清楚InnoDB锁机制】](http://mp.weixin.qq.com/s?__biz=MzI1Mzg4OTMxNQ==&mid=100000550&idx=1&sn=8a5cdff008fc1eed7b5c623c1bdf4ed1&chksm=69ccdd6a5ebb547c7b7baf6be78763fc5065e0a58de202f3e25d8d8ed56e6d1c1146332cfec1#rd)）的数据行是不能再被获取读锁而访问的，但是事实上我们依然访问到了这个数据！

**通过结果说明**：我们可以在一个事务未进行 commit/rollback操作之前，另一个事务仍然可以读取到数据库中的数据，只不过是读取到的是其他事务未改变之前的数据。此处是利用了MVCC多数据做了多版本处理，读取的数据来源于快照。