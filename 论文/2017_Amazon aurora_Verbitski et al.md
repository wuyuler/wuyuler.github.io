# 参考文献

 

[Amazon Aurora 深度探索 - 知乎](https://zhuanlan.zhihu.com/p/33603518)

对仲裁举例讲解

[Amazon Aurora 冉冉升起：我们是如何设计原生云关系数据库的 | 亚马逊AWS官方博客](https://aws.amazon.com/cn/blogs/china/amazon-aurora-design-cloud-native-relational-database/)

[Aurora论文理解 - 简书](https://www.jianshu.com/p/dd6aa53c3af5)

[分布式存储研究——Aurora | Ink Pot](http://hotlinkqiu.github.io/2019/08/15/%E5%88%86%E5%B8%83%E5%BC%8F%E5%AD%98%E5%82%A8%E7%A0%94%E7%A9%B6%E2%80%94%E2%80%94Aurora/)

[Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases - fxjwind - 博客园](https://www.cnblogs.com/fxjwind/p/6904141.html)

[Amazon新一代云端关系数据库Aurora（上） - 网易数帆 - 博客园](https://www.cnblogs.com/163yun/p/9020693.html)

# 理解

## 总体问题

* Aurora相对于传统数据库的缺点是什么?

### 存算分离

[存储计算分离的一点儿思考 - 简书](https://www.jianshu.com/p/9d5010f784c3?from=timeline)

## Abstract

* 面临问题:

We believe the central constraint in high throughput data processing has moved from compute and storage to the network.

[华为数据中心网络产品总监邓一鸥：华为AI Fabric，引领数据中心进入极速无损的高性能时代--2018开放数据中心峰会](http://www.caict.ac.cn/pphd/zb/2018ODCS/16/201810/t20181015_186863.htm)

* Aurora解决方式:

Aurora brings a novel architecture to the relational database to address this constraint, most notably by pushing redo processing to a multi-tenant scale-out storage service,

* 存算分类的原理和意义

* 什么叫the outlier storage node, disk or network path

* Although most operations in a database can overlap with each other, there are several situations that require synchronous  operations

感觉overlap应该是可以同时执行的意思, 同步应该是保证数据一致性

* stall 和 context switches是指什么情况

[向量化与编译执行浅析 - 知乎](https://zhuanlan.zhihu.com/p/63996040)

Memory Stall指的是CPU执行指令时，内存取数的等待时间。

[Context Switches上下文切换性能详解_onlyForCloud的专栏-CSDN博客](https://blog.csdn.net/onlyForCloud/article/details/46457343)

* 怎么进行下面的建模?

> We observe that a failure in durability can be modeled as a long-
> lasting availability event, and an availability event can be modeled
> as a long-lasting performance variation – a well-designed system
> can treat each of these uniformly

* 什么叫quorum systems

 

* 什么叫dirty page

[Cache的基本原理 - 知乎](https://zhuanlan.zhihu.com/p/102293437?utm_source=qq)

[SQL Server中数据的修改是如何落盘的? - 中道学友 - 博客园](https://www.cnblogs.com/awpatp/p/4110060.html)

* 这句话是什么意思

>How to leverage smart storage by offloading the lower
>quarter of a traditional database to this tier

 

* failover(故障转移)是什么?

![image-20201105192845061](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20201105192852.png)

* IOPS

**IOPS**（Input/Output Operations Per Second）是一个用于计算机[存储设备](https://baike.baidu.com/item/存储设备)（如[硬盘](https://baike.baidu.com/item/硬盘)（HDD）、[固态硬盘](https://baike.baidu.com/item/固态硬盘)（SSD）或[存储区域网络](https://baike.baidu.com/item/存储区域网络)（SAN））[性能测试](https://baike.baidu.com/item/性能测试)的量测方式，可以视为是每秒的读写次数。

* 数据库中同步和异步
* [数据库的同步和异步执行模式 _lihong22的专栏-CSDN博客](https://blog.csdn.net/lihong22/article/details/84241576)

## 2. DURABILITY AT SCALE

### 主要内容

How to reason about durability at cloud scale and how to design quorum systems that are resilient to correlated failures.

### 2.1 Replication and Correlated Failures

 

*  DURABILITY AT SCALE是什么意思

*  `reduction of jitter` 抖动是什么意思

*  `the operational issues of managing a storage fleet at scale.` storage fleet是什么

*  Replication and Correlated Failures

*  怎么得出适合存算分离的?

>Instance lifetime does not correlate well with storage lifetime.
>Instances fail. Customers shut them down. They resize them up
>and down based on load. For these reasons, it helps to decouple
>the storage tier from the compute tier.

数据库实例的存活周期与存储的数据的存活周期无必然联系。数据库实例故障时，客户可以将其关闭。并且可以根据负载上下调整实例大小。因此这种特性是有利于实现存储计算分离

6) low level background noise of node , disk and network path  failures是什么?

7)这一段中提到的各个名词对应具体设备是什么

>For example, one can have a transient lack of
>network availability to a node, temporary downtime on a reboot,
>or a permanent failure of a disk, a node, a rack, a leaf or a spine
>network switch, or even a data center.

8) quorum-based voting protocol原理理解

写入仲裁的基本原理十分简单：写入尽可能多的副本以确保仲裁读取始终可以找到最新的数据。

2/3仲裁,Vr=2 Vw=2, 我们只要理解根据这一约束, Vr时至少有一个节点保有最新的write

![image-20201105153004867](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20201105153216.png)

4/6仲裁

![image-20201105153015199](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20201105153220.png)

9) Availability Zone (AZ) 是什么

10) MTTF和MTTR

[MTTR/MTTF/MTBF图解_Kevin的博客-CSDN博客](https://blog.csdn.net/starshinning975/article/details/102893787)

### 2.2 Segmented Storage

分段存储是怎么提供数据库可用性的?

我理解是将分成小segment后, 以segment为恢复单元, 10g需要10s内, 即将同时出现两次以上故障这一要求限制在10s, 就几乎不会出现

作为反例, 如果不分段, 某一大块节点出事, 可能修复时间很长, 那就很可能与第二次故障撞上

### 2.3 Operational Advantages of Resilience

1) heat management 是什么?

## 3. The log is the database

### 主要内容

How to leverage smart storage by offloading the lower quarter of a traditional database to this tier. (Section 3).

### 3.1 The Burden of Amplified Writes

主要讲问什么不用传统数据库

* mysql中的write-ahead log (WAL)是什么?

* 什么叫before-image after-image

每个redo log记录都包含了被修改页的前后差异。可以将redo log应用于旧页从而产生新页。

* primary-standby模式是什么

* 数据库Jitter抖动是什么意思?

[延时、丢包、抖动是什么，该怎么办？ - 知乎](https://zhuanlan.zhihu.com/p/21968527)

[12 mysql性能抖动 - 春困秋乏夏打盹 - 博客园](https://www.cnblogs.com/yhq1314/p/11319737.html)

* mysql 4/4裁决的4个结点分别是什么?

### 3.2 Offloading Redo Processing to Storage

* 为什么在Aurora中，后台处理程序与前台业务处理具有负相关性。

* 如何理解下面这句话, 传统数据库为什么不能将工作移到后台

>这不同于传统的数据库，在传统的数据库中，data page的后台写入和check point与系统上的前台数据写入负载具有正相关，也就是说后台的操作会直接影响前台的数据操作的性能 。由于在aurora中的各个分片是极度平均的分散在整个集群的各个存储节点上的，而且在存储服务上还存在4/6的quorum模型进行仲裁，因此后台操作对前台的读写性能的影响是可控的。

* 下面这句

> Since
> segments are placed with high entropy across the various storage
> nodes in our system, throttling at one storage node is readily
> handled by our 4/6 quorum writes, appearing as a slow node.

* 数据库throttle(节流)是什么意思?

[Debounce，Throttle概念及应用_Codeages的博客-CSDN博客](https://blog.csdn.net/weixin_43757847/article/details/84637409)

* 2PC协议的内容

[分布式理论(三) - 2PC协议](https://juejin.im/post/6844903621495095309)

# 4. THE LOG MARCHES FORWARD

## 4.1 Solution sketch: Asynchronous Processing

1) 下面这句话的理解

> At a high level, we maintain points of consistency and
> durability, and continually advance these points as we receive
> acknowledgements for outstanding storage requests.

2) 下面这句话的理解

>The runtime state maintained by the
>database lets us use single segment reads rather than quorum reads
>except on recovery when the state is lost and has to be rebuilt.

3) 下面这几个状态分别代表什么?

the durable state, the runtime state, and the
replica state are always consistent.

**4) 为什么要用VDL取代VCL**

## 4.2 Normal Operation

1) 写放大是什么

[(79 封私信 / 85 条消息) 如何理解SSD的写放大? - 知乎](https://www.zhihu.com/question/31024021/answer/905678264)

# 总结

* 实践了“日志即数据库”的理念。

通过仅将redo log写入存储服务，aurora能够将网络IOPS降低一个数量级。

可以理解为Aurora实际上只是将 MySQL 的 redo log 设计成一个独立的存储服务, 从而提高了MySQL的吞吐

* 存算分离

Aurora的计算节点和存储节点分离，分别位于不同的VPC（Virtual Private Cloud）中。

解耦计算和存储负载,系统负载均衡调度更加灵活.在存储和计算耦合的架构中,我们很难在存储负载和计算负载之间达成比较好的平衡,原因在于这两种负载对计算机资源的诉求不同

* 基于quorum模型的数据复制机制

 

为了实现数据的可靠性，Aurora在多个可用域内部署了多个数据副本

写入仲裁的基本原理十分简单：写入尽可能多的副本以确保仲裁读取始终可以找到最新的数据。

设复制拓扑中有V个节点，每个节点有一个投票权，读 或 写 必须拿到Vr 或 Vw个投票才能返回

Vr + Vw > V，这个保证了每次读都能读到拥有最新数据的节点

Vw > V/2，每次写都要保证能获取到上次写的最新数据，避免写冲突。

2/3仲裁,Vr=2 Vw=2, 我们只要理解根据这一约束, Vr时至少有一个节点保有最新的write

![image-20201105153004867](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20201105153216.png)

第1种： 读写均可用。

如图1-9，当一个AZ出现问题，即2个副本不可用，Aurora仍然能够保证读写可用，保障数据一致。设置V=6，读多数派为Vr = 3，写多数派为Vw = 4，所以一个AZ出现故障，或者3个AZ中的两个数据中心出现故障，Aurora依然能够向外提供服务。

![img](https://pic1.zhimg.com/80/v2-30f3dffb9eb7dac95e833c8e7c91fa30_1440w.jpg)

第2种： 至少读可用。

当写服务不可用，至少还可以提供读服务。设置V=6，读多数派为Vr = 3，写多数派为Vw = 4时，一个AZ出现故障依旧能够提供读服务，如图1-10甚至跨不同AZ的3个数据中心出现故障（概率非常小），读服务依旧能够提供。

![img](https://pic3.zhimg.com/80/v2-b42619c3e66bd534c3a6456db256303e_1440w.jpg)

确保读取仲裁使我们能够通过添加其他副本副本来重建写入仲裁。

* 分段存储

Aurora将存储进行分片（segment）管理，每个分片10G，6个10G副本构成一个PGs(Protection Groups)。

分片后，每个分片作为一个故障单位

在10Gbps网络下，一个10G的分片可以在10s内恢复，因此当前仅当10s内同时出现大于2个的分片同时故障，才会影响数据库服务的可用性，实际上这种情况基本不会出现

* 将redo log的处理下推到存储层

 

Aurora的存储容量分片模型、6副本、多数派quorum共同保证了aurora的高度弹性。不幸的是，若只是简单地将此模型应用在传统数据库（如MySQL）上，会导致数据的性能极不稳定，因为每当应用执行一次数据库写入，数据实际上会产生多次不同的I/O操作。而这种高I/O操作又通过复制进行了放大。而且，很多I / O都是同步操作，从而使数据处理流程停滞并增加延迟。

在Aurora中的数据库实例唯一通过网络执行的写入数据就是redo log record，无论从数据库层还是从后台都不会写入任何数据页，因此也不会有check point和cache的换入换出。Aurora数据库实例中的log applicator会直接将redo log record推送给存储层（storage service），而storage service则会在后台或者需要的时候基于redo log生成数据库的data page。

因此，Aurora的存储服务会不断在后台去应用redo log 从而生成数据库data page，以避免每次都从头开始重新生成它们，这个过程称之为后台物化（background materialization）

Aurora在提供了强大的高可用性背后，实际放大了整个数据库读写的操作。为了解决上述的问题，在Aurora中，在网络中传播的写数据只有redo log。在将log传递到数据存储层后，再在后台或按需生成数据库的页。

纵观Aurora的设计，一个核心的设计原则就是将数据页看成是日志的一个缓存，通过牺牲一定的读，换取了很好的写性能，这是所有基于log-structured system 共性。