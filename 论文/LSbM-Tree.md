# 目录

[toc]

# 论文贡献

## 问题

主要优化LSM Tree中因为compactions导致的cache失效

![image-20210315205509522](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20210315205528.png)

如上图，RAM中关于a、b、c、d四条数据的cache因为HDD中两个level的合并失效（两个level合并，合并结果放到磁盘新位置）

## 成果

基于LevelDB进行修改，在LSM Tree的设计中添加了一个在disk上的compaction buffer。

这个compaction buffer会保存经常访问的数据。

# 设计细节

## 架构

基本设计方案是提供两个在磁盘的数据结构，分别是the underlying LSM-tree和compaction buffer。

the underlying LSM-tree: 和传统的LevelDB在磁盘保存的数据相同，即分level的保存所有数据，并且每层level数据是完全有序的。

compaction buffer: 保存访问频繁，但更新不频繁的数据。

具体架构图如图5所示。

![image-20210316100145180](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20210316100145.png)

在LSbM中，对于第i level，读请求会被发送到$B_i$（存储经常被访问的数据，命中概率高），然后去$C_i$, 获取其他数据。

如图5所示，在a, b被索引到$B_1$和$B_2$. 而c则要从$C_3$加载。

## Buffered merge

LSbM通过Buffered merge过程产生compaction buffer的数据，整体流程如6所示。

![image-20210316102151864](https://gitee.com/yyjjtt/picture_bed/raw/master/img/20210316102151.png)

当$C_i$满了后, 它所有数据都要merge到$C_{i+1}$, 同时将$C_i$加入到$B_{i+1}$, 作为buffer list中的0号单位, 即$B^0_{i+1}$.

值得注意的是, $B^0_{i+1}$是创建于$C_i$已存储与disk上的文件, 因此不会有多余的I/O操作.

这时我们注意到, $B^0_{i+1}$中已经包含了$B_i$所有的数据, 而且不同于$B_i$的是其数据是fully sorted, 这时$B^0_{i+1}$成为比$B_i$更好的查询对象, $B_i$变的不在必要, 可以**删除**.