# 目录

[toc]

# 论文贡献

本文将HTAP问题视为一个调度问题来处理.通过定义一组state, 系统根据workload对fresh data的要求，以及计算和内存资源的可用性，可以在引擎之间进行交换，从而进行调整

提出的HTAP系统架构由3部分组成, OLTP, OLAP和资源及数据交换引擎(Resource and Data Exchange, RDE).

提出一种调度算法, 通过弹性资源管理穿越了HTAP的设计频谱, 满足工作负载数据新鲜度的要求.

提出一种对OLTP和OLAP引擎无干扰的内存系统设计, 并用来来评估我们的方法的性能

# 重要内容

## 三种工作负载

一次查询需要的访问的fresh data取决于workload

论文划分为3种工作负载.

1. reporting workload
   1. run daily and in batches
   2. ETL就可以支持这种负载
2. real-time statistics workload
   1. 要求立即访问fresh data
3. monitoring workload
   1. 需要访问部分fresh data

## 数据新鲜度度量标准

**fresh data**: 我们将OLTP引擎执行的修改所产生的数据定义为fresh data，当分析查询到达时，这些数据不存在于OLAP私有存储中

因此，OLAP引擎可以通过先将新鲜数据复制到其私有存储中，或者通过OLTP引擎暴露的访问路径直接访问OLTP存储

**freshness rate**: 我们用freshness rate度量data freshness，定义为两个引擎私有存储之间相同的tuple对总体tuple数量的比率。

因此两种引擎共享一份数据, freshness rate保持为1

两种引擎存储相互独立, 则freshness rate一般小于1

在HTAP中，分析性查询在freshness rate等于1的数据快照执行。具体有两种实现方式.

1. 通过两个引擎共享同一个数据存储
2. 在查询执行前将相应的delta从事务性存储转移到分析性存储

在本文中，我们研究了每一种方法在TP引擎和AP引擎上的性能权衡，并根据新鲜度率指标来调整我们的系统设计，我们在查询层面对新鲜度率进行了测量。

# Adaptive HTAP System架构

图2是我们HTAP系统的架构, 展示了在各个引擎性能要求下, 按需实现资源的隔离与共享.

![image-20210319192707654](https://gitee.com/yyjjtt/picture_bed/raw/master/img/75QrxM2kyvtTNuY.png)

每个引擎都独立执行其工作负载，同时它们之间存在单向依赖关系：OLAP引擎从OLTP中读取新鲜数据。

由于会话**不跨引擎**，每个引擎都维护自己的请求队列，独立执行请求。

**RDE引擎**为引擎分配资源（CPU和内存）和数据。

通过引入RDE作为集成层(integration layer)，我们实现了自适应的HTAP系统(Adaptive HTAP system)设计，对现有OLTP和OLAP引擎的设计干扰最小。

## OLTP引擎

TP端主要由Storage Manager (SM), a Transaction Manager (TM) and a Worker pool Manager (WM) 三部分组成.

### Storage Manager

* 以列存格式存储在内存
  * 允许AP快速扫描数据, 而不用进行
* 多版本
* 维护两个instance
  * 任何时刻只有一个instance是active的
* 维护一种索引, 使用cuckoo hashing实现
  * 索引总是指向两个实例中的任何一个实例中最后更新的记录。因此，即使不活动的实例将保持过时，在切换时，数据将始终来自最新的版本。
* SM的active instance根据request进行切换
  * SM提供了一个切换实例的API，当不再有活动的OLTP工作线程使用该实例时，会返回不活动实例的起始地址。
* SM维护每一列的instance statistics,包含
  * 切换时记录数
  * 标志该列是否存在updated-tuples的flag
  * epoch number
* SM为每条record维护一个update indication bit, 当记录被更新时，该位被设置
  * 对更新指示位的访问使用原子操作同步进行，因为系统的不同部分可以访问这些位。

## OLAP引擎

论文主要介绍OLAP的引擎的存储管理器（SM）和查询执行器（QE），是如何工作的。

### Storage Manager

在我们的HTAP设置中，我们依靠Proteus对跨多个（连续）内存区域的分区数据的支持，只访问来自OLTP存储的新鲜数据，其余数据则来自OLAP存储。

## RDE引擎

资源和数据交换引擎(The Resource and Data Exchange engine, RDE)是OLTP和OLAP引擎之间的集成层，支持HTAP所需的操作。

从根本上来说，有两种方式可以实现HTAP，它们需要两种不同的设计选择。

1. 第一种方式是让两个引擎隔离运行，我们假设这两个引擎是在单一的、可扩展的服务器中的套接字边界。因此，在执行查询之前，通过将新鲜数据从OLTP传输到OLAP来支持HTAP。
2. 第二种方式是让两个引擎共享资源，实际上是拥有一个具有事务处理和分析处理能力的单一HTAP系统。

我们的HTAP系统设计依靠弹性资源管理(elastic resource management)来穿越上述两种方法之间的设计空间，通过RDE引擎

### 三种系统状态

我们描述了系统三种离散状态为:

1. 𝑆1(Co-located OLTP and OLAP 同位OLTP和OLAP)
2. 𝑆2(Isolated OLTP and OLAP 隔离OLTP和OLAP)
3. 𝑆3(Hybrid 混合)

我们解释了RDE引擎如何在状态之间弹性迁移。每一个时间点上移动哪个状态的决定是由系统调度器(system scheduler)做出的，并由RDE引擎执行。我们的系统设计与调度算法无关，因此，这里我们只关注我们的系统如何适应不同HTAP工作负载所需的不同配置。

### S1_Co-located OLTP and OLAP

两引擎共享the memory and the CPUs of all the sockets

当一个OLAP查询到达时，RDE引擎指示OLTP引擎切换其活动实例。OLTP引擎返回其非活动实例的指针，然后由OLAP引擎用来执行查询。这样一来，两个引擎在**硬件上相互干扰，但在软件层面上不相互干扰**，因为OLAP查询是在OLTP引擎不使用的内存的一部分上执行的。OLTP引擎在自己的数据实例上继续执行事务。具体参考图2.

**实例:**

图2中左手边描述了一个例子。其中黑色的CPU用于OLTP，而剥离的CPU用于OLAP。黑色的CPU只访问OLTP实例#1，而剥离的CPU只访问OLTP实例#2，RDE引擎在引擎之间改变内存的每个部分的所有权。

在这种设置下，OLTP引擎对这两个实例都有访问权，因为索引可能指向OLAP引擎使用的实例中存储的数据。由于两个引擎都只从第二个实例中读取数据(TP将新数据读取到实例1, AP操作是纯读取)，不涉及冲突操作，因此不需要同步。

<img src="https://gitee.com/yyjjtt/picture_bed/raw/master/img/image-20210321135000235.png" alt="image-20210321135000235" style="zoom:50%;" />

### S2_Isolated OLTP and OLAP

在这种情况下，两种引擎在最高隔离度下运行，干扰最小。

当一个OLAP查询到达时，RDE引擎指示OLTP引擎切换其活动实例。在OLTP引擎返回指向非活动实例的指针后，RDE引擎传输自上次更新OLAP引擎实例以来**插入的数据和更新的数据**。更新后的数据由OLTP引擎设置的更新指示位来识别。对于

对于传输到OLAP引擎的每一条记录，RDE引擎都会清除相应的位。

即使在整个查询执行时间内实现了**CPU级别的隔离**，但当从执行OLTP引擎的socket读取数据时，在内存级别上还是存在干扰。这种干扰受限于the interconnect bandwidth，而the interconnect bandwidth通常比内存带宽小几倍，而OLTP引擎因为随机内存访问而无法充分利用内存带宽

**实例:**

如图2所示，考虑到OLTP引擎占据左侧全部插座，OLAP引擎占据右侧全部插座，长虚线表示内存隔离边界。

假设查询到达时OLTP实例#2处于活动状态，切换后，其数据由RDE引擎通过socket互连传输给OLAP实例。

考虑到分析查询的执行在所有数据传输完毕之前无法开始，RDE引擎使用OLAP的资源来传输数据。因此，数据传输时间被计入整体查询执行时间，因为此时伤害OLTP引擎的性能没有任何好处。

### S3_Hybrid OLTP and OLAP

在这种情况下，两个引擎**共享内存，如果需要，还共享CPU资源**。混合方法的关键方面是。

㈠ OLAP引擎只访问每次查询所需的新鲜数据

㈡ OLAP引擎通过互连或直接从执行OLTP引擎的socket访问fresh data，从OLTP引擎获取CPU。

与前面的状态类似，当一个OLAP查询到达时，RDE引擎指示OLTP切换其活动实例，并将非活动实例的指针传递给OLAP引擎。然后，OLAP引擎要么通过interconnect,访问新鲜数据**或者**它获得OLTP引擎socket上的CPU，并从这些CPU的全内存带宽中访问新鲜数据。调度器决定使用这两种方案中的哪一种。

在混合方法中，内存级别的干扰在下边由互连带宽约束，在CPU级别的干扰在上边由允许从OLTP传递到OLAP引擎的CPU数量约束。

**实例:**

从图 2 中，通过考虑情况 A 和 B 分别指的是第一种和第二种方案。

在情况A中，OLAP引擎使用自己的socket，从互连中访问新鲜数据，如长虚线箭头所示。

在情况B中，OLAP引擎使用从OLTP引擎的socket中剥离出来的CPU，在对该socket进行一些操作后，将数据送回OLAP主socket，如短虚线箭头所示。情况B对于**具有大的还原系数的查询运算符（例如像计数这样的聚合）**特别有用，如果按照情况A，会给互联带来压力。

![image-20210321134646645](https://gitee.com/yyjjtt/picture_bed/raw/master/img/image-20210321134646645.png)

### S2和S3(ISOLATED, IS)的区别

S2每次查询需要从实例2中获取自上次更新OLAP引擎实例以来**插入的数据和更新的数据**, 然后AP端在本地执行查询

而S3(IS)只是根据查询需要去获取fresh data, 不会在AP端更新数据,而仅仅应付查询

# 弹性HTAP调度

系统设计层面的弹性由调度器驱动，调度器向OLTP和OLAP引擎分配资源，主要考虑每次查询的数据新鲜率。相应地，调度器选择系统状态，OLAP引擎调整其资源分配和数据访问方式，为每次分析查询提供最大的数据新鲜度。

由于执行范围跨越三种状态:co-location, isolation, and hybrid，调度器通过改变每个引擎的工作池的大小和工作线程的affinity来请求系统迁移到一种状态。相应地，工作负载执行每次都会适应引擎可用的资源。

## 弹性资源调度算法

我们的系统通过细粒度的弹性资源调度，实现不同状态下的迁移，从而实现自适应性。弹性使得RDE引擎可以为OLAP引擎提供不同的数据访问路径，以达到最大的数据新鲜度，同时对OLTP性能的干扰可控。在[33]中，我们对依赖于系统弹性的状态迁移算法进行了详细描述。

算法伪代码:

<img src="https://gitee.com/yyjjtt/picture_bed/raw/master/img/image-20210321105917293.png" alt="image-20210321105917293" style="zoom:50%;" />

算法1描述了系统在不同状态下迁移的调度策略。算法的决策是基于每个查询的新鲜度率度量的。回想一下，在我们的HTAP系统中，新鲜度率指标是指当查询到达时，在OLAP和当前活动的OLTP实例中相同的元组的比率。算法1只计算每次查询都会访问的列的新鲜度率指标。

调度器从RDE引擎中检索OLAP引擎需要从OLTP实例中获取的fresh data, 以满足当前查询$N_{fq}$的freshness-rate达到1

以及能满足更新整个OLAP实例, 让$N_{ft}$为1的fresh data

参数$\alpha$定义区间[0,1], 当它减少时，调度器更倾向于通过迁移到**𝑆2**来做ETL. 

基于性能考虑的S2的执行

* $N_{ft}$过大, 执行ETL更新耗时过多, 不如在TP端去查数据
* $N_{fq}$所指示的query中fresh data**过多**, 说明查询需要的大量数据都要去TP端查, 这时如果不进行ETL更新对TP性能影响太大了

如果由于TP端fresh data过大而不喜欢ETL，调度器将检查是否允许弹性，用标志$F_{el}$表示。

* 如果不允许, 则会指示OLAP引擎从OLTP实例远程读取查询所需的数据，迁移到状态后𝑆3-ISOLATED(只共享内存不共享CPU)
* 如果允许弹性, 如果允许弹性，那么系统将迁移到状态𝑆3 - 𝑁𝑂 - 𝐼𝑆𝑂𝐿𝐴𝑇𝐸𝐷（𝑆3 - 𝑁𝐼）或者迁移到𝑆1。取决于OLTP引擎对性能的需求

**算法理解:**

首先, 不管是否选择允许弹性, 在fresh data过大的情况下都要经历S2, 即S2是必须的

不过不允许弹性,

## 弹性资源调度算法的评价

鉴于HTAP系统的性能是由OLTP和OLAP引擎的性能决定的，算法1是一个启发式算法，**它试图在OLTP引擎的限制下优化OLAP的性能**

* 首先, 它倾向于让OLAP从OLTP获取计算资源(S3-NI),
* 然后是S1让AP和TP进行交易
* 最后才只做远程访问(S3-IS)
* 在所有情况下，当有足够多的新鲜数据时，正如𝛼所定义的那样，算法会迁移到𝑆2，以保持OLAP实例的新鲜度，并为将来的查询提供数据定位。

# 性能度量

## OLAP性能

纵坐标AP查询性能, 横坐标AP查询个数

S2是最慢的, 因为它必须为每个query进行ETL

<img src="https://gitee.com/yyjjtt/picture_bed/raw/master/img/image-20210321151703162.png" alt="image-20210321151703162" style="zoom:50%;" />

在所有情况下，我们观察到自适应性提高了性能，但代价是一个ETL，一个查询付出了额外的延迟。这个ETL的时间取决于𝛼的值，我们目前设置为0.5。

## OLTP性能

纵坐标TP吞吐量, 横坐标AP查询个数

<img src="https://gitee.com/yyjjtt/picture_bed/raw/master/img/image-20210321152051644.png" style="zoom:50%;" />

由于OLAP除了OLAP本地扫描外，还有通过互连访问数据的混合扫描，因此OLTP本地DRAM的内存压力增加，导致吞吐量略有下降。

在所有的自适应调度中，每次ETL之后，吞吐量都会增加。

𝑆3 - 𝑁𝐼由于重新分配CPU核心到OLAP引擎，与孤立状态相比，吞吐量较低。

最后，𝑆1由于OLTP和和OLAP在同一socket中的共同定位而产生差异。