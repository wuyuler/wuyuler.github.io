简单介绍一下Spanner中用来保证相关事务之间先后顺序的truetime

# 解决问题

下面假设我们有3个partition，自上而下我们称其为A,B,C，为了简化问题A，B，C各自有**一个值**可以更改，分别在3台机器上

![img](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210217215135.jpg)

**先看Tx1的蓝色图：**

在真实时间50的时候，Tx1发生，Tx1是跨A和C的事务，把A的值update为a，把C的值update为x， 分布式事务里，需要一个coordinator收集参加事务的partition的时间戳，假设它得到A的时间戳为104， C的时间戳为50，由于我们只能选一个时间戳来做整个事务的commit时间戳，假设我们总是选最大的为commit时间戳(Spanner就是选最大的，为了篇幅这里不解释为什么了)。则Tx1的commit时间戳为104；那么C记录下x这个值具有104的时间戳。

**再看Tx2的红色图：**

**10个单位时间之后**，Tx2发生在真实时间60，Tx2是跨B和C的事务，把B的值update为b，把C的值update成y，C的时间戳已经进步到60， 而如果B此刻的时间戳为100，根据选最大时间戳原则，Tx2的commit时间戳为100. 那么C记录下y这个值具有100的时间戳。

**可以看出Tx1发生在Tx2 之前，它的时间戳反而比Tx2要大。当有人读C的值时，会读到x而不是y，因为x看起来比y要新。而实际上y比x新，决定正确的新/老值是分布式数据库必须解决的问题。**

#TrueTime应用

Spanner的TrueTime利用原子钟(和标配的石英钟相比非常昂贵)，**保证了机群里的任意机器的时间戳差距不会超过一个上限值：ε**，且当coordinator选择好时间戳之后，会**等待一个ε的时间**才告诉参与transaction的partition这个时间戳来commit。

我们假设ε=60个单位时间再来看上图：

![img](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210217215139.jpg)

 

 

Truetime机制总结:

1. 原子钟保证机器间的时间差不超过ε

2. Coordinator选完commit时间戳之后等待ε的时间才广播commit，这样保证所有参加此事务的partition的本地时钟已经超过commit时间戳的值(由于1. 所有机器的本地时间不小于commit时间戳减去ε),所以任何有这些partition参与的，任何之后发生的新transaction的commit时间戳，必大于之前发生commit的时间戳

Truetime优点:

1. 保证相关事务之间的先后顺序还可以使用全局序列号生成器,这种方法优点是简单,但全局序列号生成器本身就是典型的单点, 会成为整个系统的瓶颈
2. Truetime使用时间判断事件的先后避免单点, 同时可以知道某个时间点整个系统的snapshot

Truetime缺点:

1. TrueTime 需要依赖 atomic clock 和 GPS，这属于硬件方案，而 Google 并没有论文说明如何构造 TrueTime，对于其他用户的实际并没有太多参考意义。
2. TrueTime 也会有误差范围，虽然非常的小，在毫秒级别以下，所以我们需要等待一个最大的误差时间，才能确保事务的相关顺序。

# 参考

[简单解释Spanner的TrueTime在分布式事务中的作用 - 知乎](https://zhuanlan.zhihu.com/p/44254954)