[toc]

# 多维模型设计

## 多维模型

###参考文献

[数据仓库的多维数据模型](http://webdataanalysis.net/web-data-warehouse/multidimensional-data-model/)

###定义和作用

多维数据模型是为了满足用户从多角度多层次进行数据查询和分析的需要而建立起来的基于事实和维的数据库模型，其基本的应用是为了实现OLAP（Online Analytical Processing）。

当然，通过多维数据模型的数据展示、查询和获取就是其作用的展现，但其真的作用的实现在于，通过数据仓库可以根据不同的数据需求建立起各类多维模型，并组成数据集市开放给不同的用户群体使用，也就是根据需求定制的各类数据商品摆放在数据集市中供不同的数据消费者进行采购。

### 优点

**多维数据模型最大的优点就是其基于分析优化的数据组织和存储模式。**举个简单的例子，电子商务网站的操作数据库中记录的可能是某个时间点，某个用户购买了某个商品，并寄送到某个具体的地址的这种记录的集合，于是我们无法马上获取2010年的7月份到底有多少用户购买了商品，或者2010年的7月份有多少的浙江省用户购买了商品？但是在基于多维模型的基础上，此类查询就变得简单了，只要在时间维上将数据聚合到2010年的7月份，同时在地域维上将数据聚合到浙江省的粒度就可以实现，这个就是OLAP的概念，之后会有相关的文章进行介绍。

### 缺点

**多维模型的缺点就是与关系模型相比其灵活性不够，一旦模型构建就很难进行更改。**比如一个订单的事实，其中用户可能购买了多种商品，包括了时间、用户维和商品数量、总价等度量，对于关系模型而言如果我们进而需要区分订单中包含了哪些商品，我们只需要另外再建一张表记录订单号和商品的对应关系即可，但在多维模型里面一旦事实表构建起来后，我们无法将事实表中的一条订单记录再进行拆分，于是无法建立以一个新的维度——产品维，只能另外再建个以产品为主题的事实表。

　　所以，在建立多维模型之前，我们一般会根据需求首先详细的设计模型，应该包含哪些维和度量，应该让数据保持在哪个粒度上才能满足用户的分析需求。

## OLAP

### 参考文献

[数据立方体与OLAP](http://webdataanalysis.net/web-data-warehouse/data-cube-and-olap/)

### 定义

**OLAP（On-line Analytical Processing，联机分析处理）**是在基于数据仓库多维模型的基础上实现的面向分析的各类操作的集合。

###基本操作

OLAP的多维分析操作包括：钻取（Drill-down）、上卷（Roll-up）、切片（Slice）、切块（Dice）以及旋转（Pivot）

钻取（Drill-down）：在维的不同层次间的变化，从上层降到下一层，或者说是将汇总数据拆分到更细节的数据，比如通过对2010年第二季度的总销售数据进行钻取来查看2010年第二季度4、5、6每个月的消费数据，如上图；当然也可以钻取浙江省来查看杭州市、宁波市、温州市……这些城市的销售数据。

上卷（Roll-up）：钻取的逆操作，即从细粒度数据向高层的聚合，如将江苏省、上海市和浙江省的销售数据进行汇总来查看江浙沪地区的销售数据，如上图。

切片（Slice）：选择维中特定的值进行分析，比如只选择电子产品的销售数据，或者2010年第二季度的数据。

切块（Dice）：选择维中特定区间的数据或者某批特定值进行分析，比如选择2010年第一季度到2010年第二季度的销售数据，或者是电子产品和日用品的销售数据。

旋转（Pivot）：即维的位置的互换，就像是二维表的行列转换，如图中通过旋转实现产品维和地域维的互换。

### 优点

首先必须说的是，OLAP的优势是基于数据仓库面向主题、集成的、保留历史及不可变更的数据存储，以及多维模型多视角多层次的**数据组织**形式，如果脱离的这两点，OLAP将不复存在，也就没有优势可言。

* 数据展现方式
  基于多维模型的数据组织让数据的展示更加直观，它就像是我们平常看待各种事物的方式，可以从多个角度多个层面去发现事物的不同特性，而OLAP正是将这种寻常的思维模型应用到了数据分析上。

* 查询效率
  多维模型的建立是基于对OLAP操作的优化基础上的，比如基于各个维的索引、对于一些常用查询所建的视图等，这些优化使得对百万千万甚至上亿数量级的运算变得得心应手。

* 分析的灵活性
  我们知道多维数据模型可以从不同的角度和层面来观察数据，同时可以用上面介绍的各类OLAP操作对数据进行聚合、细分和选取，这样提高了分析的灵活性，可以从不同角度不同层面对数据进行细分和汇总，满足不同分析的需求。

##维和立方

[维（Dimension）和立方（Cube）](http://webdataanalysis.net/web-data-warehouse/dimension-and-cube/)

###维(dimension)

维是用于从不同角度描述事物特征的，一般维都会有多层（Level），每个Level都会包含一些共有的或特有的属性（Attribute）

以时间维为例，时间维一般会包含年、季、月、日这几个Level，每个Level一般都会有ID、NAME、DESCRIPTION这几个公共属性，这几个公共属性不仅适用于时间维，也同样表现在其它各种不同类型的维。其中ID一般被视为代理主键（Agent），它只被用于作为唯一性标志，并且是多维模型中关联关系的代理者，在业务层面并不具有任何意义；NAME一般是业务主键（Business），在业务层面限制唯一性，一般作为数据装载（Load）时的关联键；而DESCRIPTION则记录了详细描述信息，在多维展示和分析时我们都会选择使用DESCRIPTION来表述具体含义。这3个属性一般是所有Level都会共用的，而比如用于描述星期几的属性weekid可能只会用于“日期”这层，因为年月都不具备这一信息。所以图中我将Attributes放到了一个层面上，就如同是不同的Level从底层的多个Attributes中选取自身所需的属性，Attributes层是包含着各个Level的共有和特有属性的集合。

###Hierarchy
　　因为不知道怎么翻译好，所以还是用英文吧。Hierarchy（等级、层级的意思），中文的OLAP相关文档中普遍翻译为“层次”，而上面的Level被普遍翻译为“级别”，我经常会被这样的翻译搞混淆，所以我上面也一直用Level，至少对我来说这样看起来反而清晰点 :) 。

　　因为上面这个结构的维是无法直接应用于OLAP的，我前面的文章有介绍，其实OLAP需要基于有层级的自上而下的钻取，或者自下而上地聚合。所以每一个维必须有Hierarchy，至少有一个默认的，当然可以有多个，见下图：

![Hierarchy](https://github.com/wuyuler/pictureBed/raw/master/Hierarchy-1580371229752.png)

有了Hierarchy，维里面的Level就有了自上而下的树形结构关系，也就是上层的每一个成员（Member）都会包含下层的0个或多个成员，也就是树的分支节点。这里需要注意的是**每个Hierarchy树的根节点一般都设置成所有成员的汇总（Total）**，当该维未被OLAP中使用时，**默认显示的就是该维上的汇总节点**，也就是该维所有数据的聚合（或者说该维未被用于细分）。Hierarchy中的每一层都会包含若干个成员（Member），还是以时间维，假设我们建的是2006-2015这样一个时间跨度的时间维，那么最高层节点仅有一个Total的成员，包含了所有这10年的时间，而年的那层Level中包含2006、2007…2015这10个成员，每一年又包含了4个季度成员，每个季度包含3个月份成员……这样似乎顺理成章多了，我们就可以基于Hierarchy做一些OLAP操作了。

每个Hierarchy都包含了一个树形结构，但维中也可以包含多个Hierarchy，正如上图所示，维中的Hierarchy相互独立地构建了自己的树形结构。还是以时间维为例，时间维可以根据日历（Calendar）时间组建日历的Hierarchy，也可以根据财务（Fiscal）时间组建财务的Hierarchy，而其中财务季度的划分可能并不与日历一致，基于这种多样的Hierarchy，我们在组建多维模型时可以按需选择合适的，比如给财务部的数据分析模型选用财务Hierarchy，而其他部门的分析人员显然希望看到日历样式的Hierarchy，这样就完美地满足了不同的需求。多种的Hierarchy划分同样适用于产品维，根据产品类型、产品规格等划分 Hierarchy，对于按多种条件的产品筛选和检索是十分有效的，实例可以参见淘宝搜索商品界面和太平洋电脑中产品报价界面分类筛选模块，这里不再截图了。

### 立方

这里所说的立方其实就是多维模型中间的**事实表（Fact Table）**，它会引用所有相关维的维主键作为自身的联合主键，加上度量（Measure）和计算度量（Calculated Measure）就组成了立方的结构：

![Cube](https://github.com/wuyuler/pictureBed/raw/master/Cube.png)

度量是用于描述事件的数字尺度，比如网站的浏览量（Pageviews）、访问量（Visits），再如电子商务的订单量、销售额等。**度量是实际储存于物理表中的，而计算度量则没有**，计算度量是通过度量计算得到的，比如同比（如去年同期的月利润）、环比（如上个月的利润）、利率（如环比利润增长率）、份额（如该月中某类产品利润所占比例）、累计（如从年初到当前的累加利润）、移动平均（如最近7天的平均利润额）等，这些计算度量在Oracle中都可以借助分析函数直接计算得到，相信大部分的OLAP组件都会提供类似在时间序列上的分析功能。而这些计算度量往往对于分析而言更具意义，立方中借助与各个维的关联关系从不同的角度和层面来展现这些度量。

# SchemaWorkbench

## 学习目的

uniplore创建cube的功能无法使用,直接使用其原型,创建xml文件,导入后使用uniplore分析功能

## 参考文献

[pentaho schema workbench 图文教程](https://blog.csdn.net/neweastsun/article/details/43490663)

[Mondrian Schema Workbench中文问题修正](http://wiki.smartbi.com.cn:18081/pages/viewpage.action?pageId=8093898)

## 基本概念

### schema

Schema 定义了一个多维数据库。包含了一个逻辑模型，而这个逻辑模型的目的是为了书写 MDX 语言的查询语句。逻辑模型包括几个概念： Cubes （立方体）、维度（ Dimensions ）、层次（ Hierarchies ）、级别（ Levels ）、和成员（ Members ）。而一个 schema 文件就是编辑这个 schema 的一个 **xml 文件**。在这个文件中形成逻辑模型和数据库物理模型的对应。schemaworkbench 工具创建xml文件非常简单易用。

### MDX语句

MDX查询：多维模型的查询语言MDX(MDX是微软发布的多维查询语言标准),它的语法与SQL有很多相似之处：

`select {[Measures].[Salary]} on columns, {[Employee].[employeeId].members} on rows from CubeTest`

对于这条语句，`COLUMNS` 和 `ROWS`都代表查询轴，其中`COLUMNS`代表列轴，`ROWS`代表行轴。COLUMNS又可以写成0，ROWS又可以写成1，当只有两个查询轴时，可以理解为结果的展现格式是一个平坦二维表。这条语句的含义就是查询名字为CubeTest的立方体，列显示Measures维度的salary，行显示 Employee维度employeeId级别的所有成员，那么得出的结果就是employeeId所有成员的salary，也就是所有员工的薪酬。具体语法规范和帮助文档可以参考微软的用户文档。

### cube组成

[维（Dimension）和立方（Cube）](http://webdataanalysis.net/web-data-warehouse/dimension-and-cube/)

[数据仓库介绍 1 -- Cube（立方体）、Dimension（维度）、Hierarchy（层次）、Level（级](http://blog.sina.com.cn/s/blog_51f45d410102xocs.html)

![Hierarchy](https://github.com/wuyuler/pictureBed/raw/master/Hierarchy.png)

## 技术细节

### 同一维度不同层次

![image-20200220111315057](https://github.com/wuyuler/pictureBed/raw/master/image-20200220111315057.png)

两者`all`集合命名无法区分,默认使用第一个

![image-20200220111407277](https://github.com/wuyuler/pictureBed/raw/master/image-20200220111407277.png)

例如type里叫`allBranch`,在address里叫`allBranch2`,allBranch2不存在

## 案例_基础步骤

[pentaho schema workbench 图文教程](https://blog.csdn.net/neweastsun/article/details/43490663)

### 连接数据库

创建数据库连接，需要事先拷贝数据库驱动程序到schema workbench目录下的drivers文件夹中，然后启动workbench程序，从Optionsàconnection…或者如下图所示点击工具栏按钮。

pgsql

[下载地址](https://jdbc.postgresql.org/download.html)

### 创建cube

一个 Cube 是一系列维度 (Dimension) 和度量 (Measure) 的集合区域。在 Cube 中， Dimension 和Measure 的共同地方就是共用一个事实表。

### 增加维度和层次

维度是一个层次（ Hierarchies ）的集合 , 维度一般有其相对应的维度表 . 他的组成是由层次（ Hierarchies ）而层次（ Hierarchies ）又是有级别（ Level ）组成 。在cube上右键增加维度，并命名dimCustomer保存。需要选择foreignKey，即在事实表中用于引用customer表的外键。
![img](https://github.com/wuyuler/pictureBed/raw/master/20150204185513761.png)

增加对应的层次，默认已经给我们建好了一个层次，展开dimCustomer维度看到。修改层次属性，名称可以为空，同时修改allMemberName,allMemberCaption以及primaryKey字段的值（维表的主键）；增加维表Customer。

![img](https://github.com/wuyuler/pictureBed/raw/master/20150204185530297.png)

维表

![image-20200314180743638](https://github.com/wuyuler/pictureBed/raw/master/image-20200314180743638.png)

### 增加级别

是组成 Hierarchy 的部分。属性很多，并且是 schema 编写的关键，使用它可以构成一个结构树， Level 的先后顺序决定了 Level 在这棵树上的的位置，最顶层的 Level 位于树的第一级，依次类推。

![image-20200314181039341](https://github.com/wuyuler/pictureBed/raw/master/image-20200314181039341.png)

 

### 添加度量

在cube上右键添加度量，输入字段、数据类型及计算方式。

 

### 运行MDX语句

![image-20200126114411068](https://github.com/wuyuler/pictureBed/raw/master/image-20200126114411068.png)

示例

```
select
{[Measures].saleNumber,[Measures].saleAmount,[Measures].avgPrice}
on columns,
{([dimProductType].[allProduct],[dimCustomer].[allCustomer])}
on rows
from[salesCube]
 
select
{[Measures].saleNumber,[Measures].saleAmount,[Measures].avgPrice}
on columns,
{([dimCustomer].[gender].[F])}
on rows
from[salesCube]
 
```

```
Axis #0:
{}
Axis #1:
{[Measures].[saleNumber]}
{[Measures].[saleAmount]}
{[Measures].[avgPrice]}
Axis #2:
{[dimProductType.New Hierarchy 0].[allProduct], [dimCustomer.New Hierarchy 0].[allCustomer]}
Row #0: 288
Row #0: 150,770.92
Row #0: $523.51
```

 

 

# MDX语法

## 参考文献

[MDX的基本语法及概念,偏理论](http://wiki.smartbi.com.cn:18081/pages/viewpage.action?pageId=3866643)

[csdn更实用的教程](https://blog.csdn.net/sgmcumt/article/details/87437459)

## 基础

### 基本示例

```sql
 
SELECT
  { [Measures].[Unit Sales], [Measures].[Store Sales] } ON   COLUMNS,
  { [Time].[1997], [Time].[1998] } ON ROWS
  FROM Sales
  WHERE ( [Store].[USA].[CA] )
```

基本的 MDX SELECT 语句包含一个 SELECT 子句和一个 FROM 子句，以及一个可选的 WHERE 子句。SELECT 子句决定 MDX SELECT 语句的轴维度。本 MDX 查询示例中定义了两个轴维度。FROM 子句决定当析取数据以填充 MDX SELECT 语句的结果集时将使用哪个多维数据源。可选用的 WHERE 子句决定哪个维度或成员用作切片器维度；这将数据的析取限制于特定维度或成员。本 MDX 查询示例使用 WHERE 子句来将轴维度的数据析取限制于 Store 维度的特定成员。

 

# 建模理论

## 4方面划分维度

**人员（who）**：这组包括dim_customer表和dim_staff表，分别代表租赁业务中的客户和员工。这是属于类型2的缓慢变化维度：维度表里用%_version_number、%_valid_from和%_valid_through列来跟踪同一客户或员工的历史记录。

**时间（when）**：这组中的维度表主要用来记录所有光盘租赁或归还的时间点，其中，维度表dim_date实际是日历，它是所谓的角色扮演维度，用来同时标记租赁日期和归还日期。而dim_time维度表则用来记录当天租赁的时间。

**地点（where）**：维度表dim_store用来记录DVD光盘是从哪个商店租赁的，和dim_staff、dim_customer一样，dim_store也是缓慢增长维，也有一组列用来记录同一个商店的不同历史版本。

**事件（what）**：这组包括dim_actor和dim_film两个维度表，它们是租赁业务的主题。只有dim_film表和fact_rental表直接关联，因为电影才是租赁和归还的实际对象。但是，一部电影由众多演员构成，这些演员在某种意义上也是租赁的对象。这就是所谓的桥接表dim_film_actor_bridge的由来，该表联系了演员和电影。另外，该表保存了一个权重因子，用来评估一个演员对影片的贡献值。通过原始指标值乘以权重因子就可以从演员的角度分析租赁收入，而把原来的指标值看成附加值。例如，我们就可以回答这样的问题：上个月Robert De Niro或Al Pacino的电影获得了多少租金收益？在租赁业务的星型模型中，从源数据库sakila模型中派生出来的每个维度（除了表dim_date和dim_time）都对应着sakila数据模型中的某个表。例如，维度表dim_store对应着业务系统中的store表，维度表dim_actor对应着actor表。

一般结构:

维度

* 人员（who）
* 时间（when）
* 地点（where）
* 事件（what）

度量值

# 附录代码

## cube案例1

###建表

```sql
/**销售表*/
 
create table Sale (
    saleId int not null,
    proId int null,
    cusId int null,
    unitPrice float null,
    number int null,
    constraint PK_SALE primary key (saleId)
)
 
       /**用户表*/
 
create table Customer(
    cusId int not null,
    gender char(1) null,
    constraint PK_CUSTOMER primary key (cusId)
)
 
/**产品表*/
 
create table Product(
    proId int not null,
    proTypeId int null,
    proName varchar(32) null,
    constraint PK_PRODUCT primary key (proId)
)
 
/**产品类别表*/
 
create table ProductType (
    proTypeId int not null,
    proTypeName varchar(32) null,
    constraint PK_PRODUCTTYPE primary key (proTypeId)
)
 
```

 

###数据

```sql
insert into Customer(cusId,gender) values(1,'F');
insert into Customer(cusId,gender) values(2,'M');
insert into Customer(cusId,gender) values(3,'M');
insert into Customer(cusId,gender) values(4,'F');
insert into producttype(proTypeId,proTypeName)values(1,'电器');
insert into producttype(proTypeId,proTypeName)values(2,'数码');
insert into producttype(proTypeId,proTypeName)values(3,'家具');
insert into product(proId,proTypeId,proName)values(1,1,'洗衣机');
insert into product(proId,proTypeId,proName)values(2,1,'电视机');
insert into product(proId,proTypeId,proName)values(3,2,'mp3');
 
insert into product(proId,proTypeId,proName)values(4,2,'mp4');
 
insert into product(proId,proTypeId,proName) values(5,2,'数码相机');
 
insert into product(proId,proTypeId,proName)values(6,3,'椅子');
insert into product(proId,proTypeId,proName)values(7,3,'桌子');
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(1,1,1,340.34,2);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(2,1,2,140.34,1);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(3,2,3,240.34,3);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(4,3,4,540.34,4);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(5,4,1,80.34,5);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(6,5,2,90.34,26);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(7,6,3,140.34,7);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(8,7,4,640.34,28);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(9,6,1,140.34,29);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(10,7,2,740.34,29);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(11,5,3,30.34,28);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(12,4,4,1240.34,72);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(13,3,1,314.34,27);
 
insert into sale(saleId,proId,cusId,unitPrice,number)values(14,3,2,45.34,27);
 
```