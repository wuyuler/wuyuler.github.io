# 目录

[toc]

#参考文献

##网课

[麻省理工公开课 线性代数 MIT 18.06 Linear Algebra, Spring 2005 中英双语字幕](https://www.bilibili.com/video/BV1zx411g7gq?p=3)

## 参考书

# 课程记录

## 第5课 转置-置换-向量空间R

### 置换矩阵

行重新排列的单位矩阵

n\*n置换矩阵的格式: n!=n(n-1)..3\*2\*1

### 转置(transposes)

![image-20200829183416233](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829183416.png)

![image-20200829183506266](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829183506.png)

### 对称矩阵

![image-20200829183604515](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829183604.png)

![image-20200829183623086](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829183623.png)

得到对称矩阵一种方法,矩阵转置乘矩阵

![image-20200829183806610](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829183806.png)

证明

![image-20200829184449109](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829184449.png)

### 向量空间

空间的概念: 有很多向量,一整个空间的向量,但不是任意向量的组合

能被称为空间所包含的向量必须能进行加法和数乘运算

举例:

![image-20200829185115321](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829185115.png)

R表示实数,2表示两个维度的向量

通过绘图,我们发现R2其实就是下x,y平面,不能缺任何一个点 ,例如缺少(0,0) ,则0*某个向量则有问题

举个非向量空间的例子:

![image-20200829193804702](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829193804.png)

上半区满足加法,但不满足乘以负数的情况

### 子空间

子空间: 满足空间的定义,但又不用包括所有向量

举例R2的子空间:

1)R2本身

2)一条过原点的直线

![image-20200829194056279](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829194056.png)

3)原点,只包含零向量

### 矩阵的列空间

![image-20200829194552799](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829194553.png)

列空间( column space): C(A) 各列线性组合构成的空间

绘图:

![image-20200830085550908](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830085558.png)

两列构成了三维空间的一个平面.

## 第7课 求解Ax=0 主变量 特解

### 消元&主元（pivot）&秩（rank）

![image-20200825203756706](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825203757.png)

消元：col2=col2-2*col1

每行第一个非零值为主元

主元最终呈现阶梯型

秩：主元的个数，也表示起作用的方程数，本例有2个

本例中主元个数为2

### 主列&自由列&特解&通解

主列：主元在的列

自由列：表示可以分配任意数值，该例中即x2和x4  我们可以发现，自由列可以由主列线性组合得到

特解：给自由列分配任意值后得到的解

![image-20200825204123965](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825204124.png)

例如，取0，1和1，0（两者线性无关，且便于计算）

![image-20200825204533179](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825204533.png)

取倍数可以得到解的直线，这是整个零空间吗？No,有两个自由变量，可以取任意值

![image-20200825204706625](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825204706.png)

零空间：所有特解的线性组合,每个自由变量对应一个特解

解法说明：他求出了零空间的两个独立向量，这样整个零空间就可以用这两个向量线性组合得来

![image-20200825205604558](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825205604.png)

### 最简行阶梯阵(reduced row echelon form)

化简：

1. 将主元上下简化为0
2. 将主元化简为1
3. 用R表示

![image-20200826095019770](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826223742.png)

用处：非常清晰的看矩阵

看出主列，主元 看出有一个**2X2的单位阵**，位于主行和主列的交界处

整个简化过程：

Ax=0 -> Ux=0 -> Rx=0

典型简化行阶梯形式

![image-20200826104545211](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826104545.png)

对这种最简化形式可以快速求解

![image-20200826105442158](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826105442.png)

### 案例1 转置矩阵的消元

**定理**：矩阵主列的个数与其转置相同

![image-20200826110317277](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826110317.png)

第一次消元过后，第二行为000，与第三行置换（对求解x1,x2,x3没有影响的，可以带入方程理解下）

主元=2

特解=自由变量=3-2=1

求零空间，设自由变量为1，得解如下：

![image-20200826111054932](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826111055.png)

the whole null space，加c

![image-20200826111138161](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826111138.png)

求最简行阶梯矩阵

![image-20200826111309331](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826111309.png)

与我们求特解相对应

![image-20200826111355945](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826111356.png)

## 第8课 求解Ax=b,可解性和解的结构

目标：Ax=b,上节课Ax=0的进阶

与Ax=0的区别是可能无解

### 增广矩阵

同时考虑A和b

![image-20200826204437600](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826204437.png)

###  有解的条件

说法1：b属于A的列空间，即b必须为A中各列的线性组合

说法2：行组合为0向量时，b为0

### 求解Ax=0

1）找特解

对所有自由变量取0，求解Ax=b的主元

![image-20200826205438287](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826205438.png)

2）加上零空间内任意向量

![image-20200826205659598](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826205659.png)

证明：

![image-20200826205739219](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826205739.png)

案例求解

![image-20200826210049398](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826210049.png)

### 从秩的角度分析可解性

从秩的定义可知秩小于等于m,n

![image-20200826210755580](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826210755.png)

讨论满秩的情况

列满秩，r=n：没有自由变量，如果解存在，只有唯一解 0或1个解

行满秩，r=m<n: 进行消元时，不会出现**全零行**，因此恒有解 自由变量有n-r即n-m个

![image-20200826212126212](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200826212126.png)

r=m=n:

有且只有唯一解

 

 

## 第9课 线性相关性、基、维数

### Ax=0在m<n时必有非0解

首选明确,Ax=0,必定有解

r<m<n,必定存在自由变量,对自由变量取任意值求解,必有非零解

### 线性相关性（Independent）

![image-20200825200455034](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825200540.png)

#### 例1 零向量

两个向量中，零向量和任意非零向量也是线性相关

![image-20200825201453889](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825201453.png)

#### 例2 三个二维向量

二维空间的3个向量是线性相关的

![image-20200825201553342](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825201553.png)

构造矩阵

![image-20200825201735998](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200825201736.png)

r<m<n列数大于行数,Ax=0必有非零解

我们认为

### 判定线性相关的方法

对向量构建矩阵,判断Ax=0是否有解

Ac=0 c为非零向量,则线性相关

从秩的角度:

线性无关 r=n

线性相关:r<n

### 生成子空间

![image-20200827110352002](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200827110400.png)

### 基

向量空间的一组基是指:一系列的向量,它们具有两个性质

1. 线性无关
2. 他们生成空间

如果需要确定一个子空间,需要告诉我基是什么

#### 例1 三维空间的基

一个基

![image-20200827112119086](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200827112119.png)

只有两个向量，是三维空间的一个平面

![image-20200827204130508](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200827204131.png)

构成Rn空间的nXn矩阵需要是可逆的

### 维数

Space is C(A)

对给定空间，R3,Rn或其他，任意基的个数相同，该个数称为维数

矩阵的秩时列空间的维数

![image-20200828092257172](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829094820.png)

零空间的维数是自由变量的个数,即n-r

矩阵的秩=主列的个数=列空间的维数

![image-20200828093215503](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828093215.png)

![image-20200828092607331](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828092607.png)

#### 例1 列空间的基

![image-20200828093024866](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828093024.png)

首先,矩阵一定能构成列空间,该列空间的基可以是1,2或1,3等等

我们知道要处理的列空间的维数,只需要找到对应维数个数的线性不相关的向量即是基

#### 例2 零空间的基

![image-20200828093638651](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828093638.png)

零空间的维数是自由变量的个数

## 第10课 四个基本子空间

### 基本子空间

列空间

零空间

行空间

A转置的零空间,A的左零空间

![image-20200828102458523](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828102458.png)

简要图示:

![image-20200828102909015](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828102909.png)

### 基本问题

1. 它们的基
2. 它们的维数

行空间和列空间有相同的维数(转置后主元位置不变,主元个数不变,行秩等于列秩)

行空间的基是最简阶梯的前r行

![image-20200828122410267](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828122410.png)

左零空间

![image-20200828122528433](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828122528.png)

将行初等变化等价为乘矩阵

![image-20200828145005926](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828145006.png)

### 例1 单位矩阵化为E,求左零空间的基

![image-20200828150152569](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828150152.png)

该矩阵m=3,r=2,则左零矩阵的维数为3-2=1

求零向量的基,就是求一个向量使矩阵向量组合为零向量,该例钟即为[-1 0 1]

该方法的好处使不用转置

### 矩阵空间

代表符号**M**

例:一个3*3的矩阵空间

一个子空间:

* 所有上三角矩阵
* 所有对称矩阵
* 两个子空间的交集也是子空间,即对角矩阵(只有对角线上有元素) 比前两者更小的空间

对角矩阵的维数:3

![image-20200828151351831](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828151351.png)

## 第11课 矩阵空间、秩1矩阵和小世界图

### 矩阵空间

#### 例1 3*3矩阵空间

基数=9

![image-20200828200401405](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828200401.png)

对称矩阵S：

基数=6

单纯对角线位置三个，对角线上方每一个都确定下面对应的一个

上三角矩阵U：

基数=6

S交U 对角阵D：

基数=3

S并U：我们不感兴趣，因为不是子空间，它不封闭

S+U（与S并U不同，应该是与S并U不同的另一批向量，没有谁包括谁之说）: S和U的和, 取S中任意元素加上U任意元素

S+U的子空间是ALL 整个3*3矩阵空间

定理：如果我有两个子空间，一个维度加上另一个维度，等于他们的交的维度加上他们和的维度

![image-20200828212253368](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200828212253.png)

### 例1 高阶导数 解空间的基

![image-20200828215834253](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829094801.png)

### 例2 秩1矩阵

![image-20200829094745717](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829094753.png)

所有秩一矩阵都可以看成主列乘以一行

![image-20200829094921143](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829094921.png)

![image-20200829095034568](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829095034.png)

秩1矩阵像是积木,可以搭建起任意矩阵

5*17的矩阵,需要4个秩1矩阵

### 例3 所有秩4矩阵能构成子空间吗

不能,首先不包括0向量,其次两个秩4矩阵相加通常不是秩4矩阵

### 图

图是结点和边的组合,边连接结点

![image-20200829101801723](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200829101801.png)

小世界的意思是根据六度分离理论,我们和任意一个人的关系都能通过6个结点联系到,然人感叹"世界真小!"

## 第12课 图和网络