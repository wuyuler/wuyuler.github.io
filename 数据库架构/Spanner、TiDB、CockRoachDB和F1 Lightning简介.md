# 目录

[toc]

# 四种数据库简介

## Spanner

### 背景与目的

在google的**BIGTABLE**论文中，提到过Bigtable后续计划支持多Master的方向，由于BIGTABLE的架构中，只有一个Master服务器，因此一个Bigtable分布式数据库的扩展能力，始终是由一定的限制，数据量增加后，势必需要就会出现瓶颈，如何提升数据库的数据管理能力，解决数据规模不断增加后带来的问题。同时Bigtable丢失传统RDMS系统的一些特点，比如多数据表之间的**事务一致性**，这些都是数据库必须也是应该具备的特性。是否能够满足分布式数据库的数据分布、负载和吞吐量的同时，还具备关系数据库特性，这不就是分布式数据库的智高无上的理想吗？但分布式数据库CAP理论告诉我们，分布式数据库**一致性，可用性、分区容忍性**此特性必然只能取其二，是不可能同时具备，这个魔咒能够被打破吗？

聪明的Google人找到了解决方案，并在技术上得以实现，Spanner就是在这个场景下面诞生。

Spanner 是Google的全球级的分布式数据库 (Globally-Distributed Database) 。

Spanner的扩展性达到了令人咋舌的全球级，可以扩展到数百万的机器，数已百计的数据中心，上万亿的行。除了夸张的扩展性之外，他还能同时通过同步复制和多版本来满足**外部一致性**，**可用性**也是很好的。冲破CAP的枷锁，在三者之间完美平衡。

### Spanner在Google中的定位

要搞清楚Spanner原理，先得了解Spanner在Google的定位。

![img](https://ask.qcloudimg.com/http-save/yehe-2194270/yp6dr081br.jpeg?imageView2/2/w/1620)

从上图可以看到。Spanner位于F1和GFS之间，承上启下。

F1:

F1是谷歌开发的可容错可拓展的RDDBMS。Spanner是F1至关重要的底层存储技术。

GFS：

GFS是Google的分布式文件系统，开源世界对应HDFS。

### 设计理念

**Spanner是个可扩展，多版本，全球分布式还支持同步复制的数据库。**

它是google第一次实现了两个要求的数据库:

1. 全球拓展
2. 支持外部一致性

作为一个全球化的分布式系统，Spanner提供一些特别的性质。

1. 细粒度的对数据副本数进行动态控制。
   1. 哪些数据中心包含哪些数据
   2. 数据离用户多远（为了保证读延时）(读延迟取决于最近的拷贝)
   3. 每个数据副本之间的距离有多远（为了保证写延时）(写延迟取决于最远的拷贝)
   4. 总共维护多少个数据副本（为了保证可用性和读性能）(多写几份，可以抵御更大的事故)
2. 读写的[外部一致性](https://www.zhihu.com/question/56073588/answer/251047079)
3. 基于时间戳的全球一致性读

后两种特性是一般分布式数据库所不具备的，这两个特性让Spanner可以实现**一致的备份**，**一致的MapReduce**，还有**原子的Schema修改**

这些写特性得益于Spanner有一个全球时间同步机制，可以在数据提交的时候给出一个时间戳。因为时间是系列化的，所以才有外部一致性。这个很容易理解，如果有两个提交，一个在T1,一个在T2。那有更晚的时间戳那个提交是正确的。

提供这种全球时间同步机制的是具有GPS和原子钟的[TrueTime API](https://www.cnblogs.com/yyjjtt/p/14410039.html)

### 系统架构

Spanner由于是全球化的，所以有两个其他分布式数据库没有的概念。

* Universe。一个Spanner部署实例称之为一个Universe。目前全世界有3个。一个开发，一个测试，一个线上。因为一个Universe就能覆盖全球，不需要多个。
* Zones. 每个Zone相当于一个数据中心，一个Zone内部物理上必须在一起。而一个数据中心可能有多个Zone。可以在运行时添加移除Zone。一个Zone可以理解为一个BigTable部署实例。

![img](https://upload-images.jianshu.io/upload_images/7111776-92bcb69fbddf9b4b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1197/format/webp)

如图所示。一个Spanner有上面一些组件。实际的组件肯定不止这些，比如TrueTime API Server。如果仅仅知道这些知识，来构建Spanner是远远不够的。但Google都略去了。那笔者就简要介绍一下。

* Universemaster: 监控这个universe里zone级别的状态信息
* Placement driver：提供跨区数据迁移时管理功能
* Zonemaster：相当于BigTable的Master。管理Spanserver上的数据。
* Location proxy：存储数据的Location信息。客户端要先访问他才知道数据在那个Spanserver上。
* Spanserver：相当于BigTable的ThunkServer。用于存储数据。

可以看出来这里每个组件都很有料，但是Google的论文里只具体介绍了Spanserver的设计，笔者也只能介绍到这里。下面详细阐述Spanserver的设计。

#### Spanserver

本章详细介绍Spanserver的设计实现。Spanserver的设计和BigTable非常的相似。参照下图

![image-20210218172910200](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210218173036.png)

从下往上看。每个**数据中心**会运行一套**Colossus (GFS II) **。每个机器有100-1000个**tablet**。Tablet概念上将相当于数据库一张表里的一些行，物理上是数据文件。打个比方，一张1000行的表，有10个tablet，第1-100行是一个tablet，第101-200是一个tablet. 但和BigTable不同的是BigTable里面的tablet存储的是Key-Value都是string，Spanner存储的Key**多了一个时间戳**：

```
(Key: string, timestamp: int64) ->string。
```

因此spanner天生就支持多版本，tablet在文件系统中是一个**B-tree-like的文件**和一个**write-ahead日志**。

每个Tablet上会有一个Paxos状态机。Paxos是一个分布式一致性协议。Table的**元数据**和**log**都存储在上面。Paxos会选出一个replica做**leader**，这个leader的寿命默认是10s,10s后重选。Leader就相当于复制数据的master，其他replica的数据都是从他那里复制的。读请求可以走任意的replica，但是写请求只有去leader。这些replica统称为一个**paxos group**。

每个leader replica的spanserver上会实现一个**lock table**来管理并发。Lock table记录了两阶段提交需要的锁信息。但是不论是在Spanner还是在BigTable上，但遇到冲突的时候长时间事务会将性能很差。所以有一些操作，如事务读可以走lock table，其他的操作可以绕开lock table。

对于每个处于leader位置的副本，每个SpanServer都会实现一个**事务管理器(transaction manager)**来支持分布式事务。该事务管理器作为**participant leader**；Paxos Group中的其他的副本（leader之外的副本）作为**participant slaves**。若一个事务只涉及到一个Paxos group（大部分的事务都只涉及到一个Paxos Group），该事务可以直接跳过事务管理器，因为lock table和Paxos一起就足以保证事务性。若一个事务涉及到多个Paxos Group，这些Paxos Group的leader就会相互协调，从而完成二阶段提交。其中的一个参与Paxos Group被选为coordinator：该Group的participant leader（实际上就是spanserver为每个leader副本实现的事务管理器） 就是coordinator leader, 而该Group的slaves就是coordinator slaves。每个事务管理器的状态都存储在底层的Paxos group中(也就是存储在一个Paxos Group的各个副本中)。

### 参考

[GOOGLE分布式数据库技术演进研究--从Bigtable、Dremel到Spanner（三）-南方的熊熊-51CTO博客](https://blog.51cto.com/802796/1355302)

[Google Spanner原理：地球上最大的单一数据库 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1131036)

## TiDB

### 背景与目的

RDBMS因其关系模型，事务保证和SQL接口而大受欢迎。但由于它不提供可拓展性和高可用性。因此21世纪初互联网应用程序倾向于NoSQL系统，例如Google BigTable和DynamoDB.。NoSQL放宽一致性的要求，同时提供更高的可拓展性和用于替代的数据模型，例如键值对、图和文档。然而许多应用仍然需要事务处理能力、数据一致性和SQL接口，因此出现了NewSQL系统，如CockroachDB和GoogleSpanner之力诶的NewSQL系统为OLTP提供NoSQL般的我高扩展性，同时也为事务处理提供ACID保证。此外，像许多架构在 Hadoop 之上的 SQL 系统一样，基于 SQL 的在线分析处理（OLAP）系统正在迅速发展。

这些系统认为 “不存在万应良方”（one size does not fit all），故而针对 OLAP 和 OLTP 的不同目标采用不同的数据模型和技术方案。然而，同时开发、部署并维护多个业务系统的成本非常高。除此之外，对最新的数据进行实时分析是一件非常诱人的事情。故而工业界和学术界开始探索 OLTP 和 OLAP 混合系统（**HTAP**）的可行性 。HTAP 系统应像 NewSQL 系统一样实现可扩展性，高可用性和强一致性。此外，HTAP 系统还要能高效地读取最新数据，以确保 OLTP 和 OLAP 系统的高并发和低延迟访问，并满足数据实时更新和隔离。

数据实时更新（**Freshness**），是指分析查询处理最新的数据的方式。实时分析最新数据具有巨大的业务价值。但是在某些 HTAP 解决方案中，例如基于提取-转换-加载（**ETL**）的解决方案中，不能保证分析的实时性。通过 ETL 过程，OLTP 系统会定期将最新的一批数据推送到 OLAP 系统中。ETL 往往需要花费数小时或数天的时间，因此无法提供实时的数据分析。可以将最新数据通过数据流方式传输到 OLAP 系统来替换 ETL 过程，以减少同步时间。但是，由于这两种方法都缺少全局数据治理模型，因此一致性语义会更加复杂。而且与多个系统接口进行交互会带来额外的开销。

隔离（Isolation），是指为单独的 OLTP 和 OLAP 查询保证不受影响的性能。

TiDB认为HTAP需要隔离地处理事务查询和分析查询，以消除它们之间的干扰。为实现这一点，有必要针对两种查询指定的数据维护不同的副本。

但是，为分布式副本提供一致性的视图是一项挑战。但只有实现这种一致性，分析请求才能大规模高效的从事务性工作负载中读取一致且新鲜的数据。

为了应对这一挑战，我们想**拓展基于状态机的复制共识算法，以为HTAP工作负载提供一致的副本**。基于这个新颖的想法，我们提出了基于Raft的HTAP数据库：TiBD.

PS： 主要参考论文的Introduction

### 设计理念

TiDB是一个有列存储和行存储组成的multi-Raft storage system。

TiDB向 Raft 共识算法引入了专用的学习者节点 Learner 。Learner 将会异步地复制领导者节点 Leader 的事务日志，并为 OLAP 查询构造新的副本即将行格式tuple转化为列格式tuple，从而实现**real-time updatable column store**

该column store让分析查询能有效的读取fresh and consistent数据，并行存储的事务高度隔离。基于此存储系统，我们单独设计的**SQL查询引擎**，以处理大规模分布式事务和昂贵的分析查询。该查询引擎最佳地访问航格式和列格式副本。

TiDB 展现了一种创新的解决方案，可以帮助**基于共识算法的 NewSQL 系统发展成为 HTAP 系统**。NewSQL 系统通过复制其的数据库（如 Google Spanner 和 CockroachDB）确保 OLTP 请求的高可用性、可扩展性和数据持久性。通过复制机制（通常来自共识算法）在数据副本之间同步数据。基于日志复制，NewSQL 系统可以提供一个专用于 OLAP 请求的列式副本，这样就可以像 TiDB 一样独立支持 HTAP 请求。

### 系统架构

论文架构图：

![](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20210227144026.png)

如论文提供的架构图所示，三个核心组件分别是：分布式存储层、布局驱动（Placement Driver, PD）和计算引擎层。

分布式存储层有行式存储（TiKV）和列式存储（TiFlash）组成。

**计算引擎层**：

计算引擎层是无状态、可扩展的。定制的 SQL 引擎由基于成本的查询优化器（cost-based query optimizer）和分布式查询执行器（distributed query executor）组成。TiDB 基于 Percolator [33] 实现了两阶段提交（2PC）协议，以支持事务处理。查询优化器可以根据查询最佳地选择从 TiKV 还是 TiFlash 中读取。

**布局驱动（PD）**:

布局驱动（PD）负责管理区块，包括提供每个键的区块和物理位置，以及自动移动区块以平衡工作负载。PD 也是时间戳分配器，提供**严格递增且全局唯一的时间戳**。这些时间戳也可以作为事务的标识符。为了提高稳定性和性能，布局驱动同样可以包含多个布局驱动成员。布局驱动**不具有持久状态**，启动时布局驱动成员会从其他成员和 TiKV 节点读取所有必要的状态信息。

**分布式存储层**:

分布式存储层由行式存储（TiKV）和列式存储（TiFlash）组成。从逻辑上讲，存储在 TiKV 中的数据是一个有序的键值映射。每个元组都映射成一个键值对。关键字由行数据的表标识符（table ID）和行标识符（row ID）组成，值对应实际的行数据，其中表标识符和行标识符是唯一的整数，行标识符来自主键列（primary key column）。四列元组的 key-value 示例：

```
Key: {table{tableID} record{rowID}}
Value: {col0, col1, col2, col3}
```

为了能够横向扩展，采用范围分区（range partition）策略，将大键值映射分割成许多连续的范围（range），每个范围称为一个区块（region）。每个区块都存有多个副本来保证高可用性。Raft 共识算法用于维护每个区块的副本之间的一致性，从而形成 Raft 组。不同 Raft 组中的 Leader 会从 TiKV 异步复制数据到 TiFlash 。TiKV 和 TiFlash 都可以部署在单独的物理资源中，因此在处理事务和分析查询时可以提供隔离保证。

![image-20210227143904269](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20210227143904.png)

### 参考

[TiDB 水平扩展推荐配置与高可用 - ercom - 博客园](https://www.cnblogs.com/farwish/articles/12570864.html)

[GitHub - PsiACE/TiDB-A-Raft-based-HTAP-Database: Unofficial! English original and Chinese translation of the paper.](https://github.com/PsiACE/TiDB-A-Raft-based-HTAP-Database)

[CAP 定理的含义 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2018/07/cap.html)

[读 TiDB 论文有感 | 数据强一致性且资源隔离的 HTAP 数据库 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1694994)

## CockRoachDB

### 背景与目的

假设一家大型公司在欧洲和澳大利亚拥有核心用户群，而在美国则拥有快速增长的用户群。为了在降低运营成本的同时为其全球平台提供支持，该公司已做出战略决策，决定迁移到基于云的数据库管理系统（DBMS）。它具有以下要求：为了符合欧盟的通用数据保护条例（GDPR），其欧洲用户的个人数据必须在欧盟范围内注册。为避免跨大陆通信引起的高延迟，数据应位于最常访问数据的用户附近，并在旅行时遵循这些规则（在法规限制内）。用户期望“always on”体验，因此DBMS必须具有容错能力，即使在区域性全面故障中幸存下来。 最后，为了避免数据异常并简化应用程序开发，DBMS必须支持带有可序列化的SQL的事务。

CockroachDB（简称CRDB）是设计用于支持所有上述要求的商业DBMS。

### 设计理念

- CockroachDB是一个分布式关系型数据库，主要设计目标是可扩展，强一致和高可靠。
- 在无人干预情况下, 能以极短的中断时间容忍磁盘、主机、机架甚至整个数据中心的故障。
- 采用**完全去中心化架构**, 集群中各个节点的地位完全对等。
- 所有功能封装在一个二进制文件中, 可以做到尽量不依赖盘配置文件直接部署。
- 对外提供标准SQL接口，集群中任意节点都可以作为接入节点处理用户的SQL请求。
- 支持水平扩展
  - 添加更多节点可以提升整个集群的存储容量, 理论上最大可以支撑4EB的数据存储
  - 客户端的查询请求可以发送到集群任意节点, 且每个查询可独立并发执行, 集群的吞吐能力可以随着节点数的增加线性提升。
  - 查询以分布式任务的方式在各个数据节点并发执行，可以通过增加节点数来提升单个查询的性能。
- 支持强一致性
  - Range的多个副本之间使用Raft一致性协议, 所有一致性状态都存储在RocksDB中。
  - 对同一个Range内数据的单一或批量修改, 由Raft保证Range操作的ACID语义。
  - 涉及多个Range的操作, CockroachDB使用高效的无锁分布式事务保障ACID语义。
- 支持高可用
  - 将Range副本分布在一个数据中心, 可以确保低延迟复制, 同时能容忍磁盘或机器故障。
  - 如果将副本分布在不同机架, 即使某些网络交换机故障, CockroachDB仍可提供服务。
  - Range副本可以跨数据中心和跨地域分布, 以应对来自数据中心电源中断或网络中断, 以及区域电力故障等问题。
- 隔离级别
  - 基于历史快照时间和当前时间, 提供外部一致的无锁读写。
  - 快照隔离(SI) [Snapshot Ioslation]
    - SI提供无锁读写, 但是存在写偏序问题(write skew)
  - 串行快照隔离(SSI) [Serializable Snapshot Isolation]
    - SSI消除了写偏序, 但在竞争激烈的系统中会存在性能问题
  - SSI是默认的隔离级别, 用户须根据实际性能情况, 选择合适的隔离级别。CockroachDB只提供有限的linearizability(严格的顺序一致性)。

### 系统架构

![img](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20210227200959.jpeg)

采用分层架构

* SQL层
  * CockroachDB支持标准SQL, 当CockroachDB集群的某个节点收到SQL请求时，会经过SQL解析、SQL执行计划生成、SQL执行等重要步骤。
  * CockroachDB兼容PostgreSQL协议，对于报文的封装和解析完全按照PostgreSQL的方式进行，所以用户可以直接使用PostgreSQL的客户端访问CockroachDB。
  * CockroachDB对于用户的SQL语句按照PostgreSQL的语法进行解析，解析完成后生成抽象语法树(AST)
  * CockroachDB 会根据不同的语法树生成对应的执行计划。目前执行计划基本是基于规则的方式来生成的。
    - 对于OLAP的SQL Statement, CockroachDB会将逻辑计划转化为物理执行计划，即通过分布式任务的方式进行并行执行。
  * 当执行计划生成完毕后，CockroachDB会按照约定的方式开始执行，此时CockroachDB将调用事务性的KV接口。执行完成后通过协议层将执行结果返回给客户端。
* 分布式KV存储
  - 负责Range路由寻址，提供统一的key-value存储。
  - 可以由任意数量的CockroachDB物理节点组成，每个节点包含一个或多个Store(通常一个Store独占一块磁盘)
  - 每个Store包含多个Range，Range为KV层数据管理的最小单元，每个Range的多个副本之间使用Raft协议进行同步
  - 节点和Range可以根据不同的物理网络拓扑结构进行编排，从而在可靠性和性能之间折衷。
  - 假设一个Range包含三个副本，每个副本可以位于不同的位置：
    - 如果副本分布于同一台服务器上的多块磁盘，可以容忍单块磁盘故障。
    - 如果副本分布于同一机架上的不同服务器，可以容忍单台服务器故障。
    - 如果副本分布于同一个数据中心不同机架，可以容忍单个机架电源和网络故障。
    - 如果副本分布于不同数据中心，可以容忍大规模网络中断或断电。

### 参考

[CockRoachDB简介 - wellDoneGaben - 博客园](https://www.cnblogs.com/ronnieyuan/p/12565977.html)

[评测分布式 SQL 数据库 CockroachDB | Wolfogre's Blog](https://blog.wolfogre.com/posts/test-of-cockroachdb/)

[最受关注的NewSQL之一：CockroachDB在百度的实践(有彩蛋) - 更多 - dbaplus社群：围绕Data、Blockchain、AiOps的企业级专业社群。技术大咖、原创干货，每天精品原创文章推送，每周线上技术分享，每月线下技术沙龙。](https://dbaplus.cn/news-160-2651-1.html)

## F1 Lightning

### 背景与目的

在业界HTAP的热度越来越高, 出现很多HTAP相关工作. 但这些工作大部分之回答了这样的问题"如果我们从头开始为HTAP设计系统, 它将是什么样的"。

现有 HTAP 的研究领域，大多数项目都是以所谓 Greenfield 方案（不受前序方案约束）为假设，但 F1 Lightning 需要在对现有业务不做任何迁移且设计方案最大程度不对 TP 系统做修改（组织架构层面，F1 Lightning 团队也不管 TP 系统的代码）。

### 设计理念

现有的 HTAP 系统被分为两种设计类型：

1. 单一系统承载 OLTP 和 OLAP（Single System for OLTP and OLAP）
2. 分离的 TP 与 AP 系统（Separate OLTP and OLAP System）

分离系统优点是可以单独针对TP和AP系统设计，但在既有的架构下，往往需要离线ETL转运数据，F1 Lightning的设计也落在**分离系统**的范畴上，但F1 Lightning使用**CDC**进行实时数据勾兑而非离线ETL。

F1 Lightning是一种松散耦合的HTAP架构（loosely coupled HTAP），论文中称为"HTAP-as-a-service"

### 系统架构

由于项目立项的前提是对TP系统无入侵性，F1 Lightning的设计比较保守：通过 CDC 做 HTAP。所以实际上，F1 Lightning 是一个 CDC + 可变更列存的方案。其系统架构图如下：

![image-20210215172102605](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210215172153.png)

**F1 Lightning**的HTAP解决方案主要包含3个组成部分：

1. 一个OLTP引擎, 作为the source of truth, 并暴露一个change data capture(CDC)或其他change replay接口
2. F1 Query, 分布式SQL查询引擎
3. Lightning, 维护并提供读取优化的副本

其中**Lightning**的主要包含4个组件：

1. 数据存储：持续接受 CDC 的变更信息并存储到 Lightning 的读优化（列存）副本。
2. 变更复制：一个分布式的 CDC 通道，从 OLTP 系统接受事务日志，并将他们分发到不同的存储服务器，并且按需对新接入表进行数据历史回放。
3. Metadata 数据库：存放存储节点和变更复制的状态。
4. Lightning Master：全组件协同和管理。

### 参考

[读论文 - F1 Lightning: HTAP as a Service - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1718993)

 

 

 