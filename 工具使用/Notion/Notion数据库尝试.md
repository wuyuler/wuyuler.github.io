# 目录

[toc]

# Notion概述

Notion算是最近很火的一个笔记软件, 我尝试了一段时间, 多次因为种种缺陷放弃, 又屡屡因为其独有的惊艳效果而重新拾起. 几次折腾后, 渐渐也佛系了, 没有一开始想用它实现one for all的心气, 而是和其他笔记软件搭配, 在部分场景使用它.

# 优缺点比较

缺点:

1. 不支持悬浮目录. 意味着对长笔记不友好. 虽然可以使用Toggle list将整篇笔记按照标题收缩, 但太不优雅了. 我都是使用Typora来写比较长的笔记.
2. 加载速度慢, 加载图片的速度爆炸. 意味着不要将一些急用的东西记在上边, 比如要乘坐的火车的时刻表截图
3. 对markdown的支持不舒服. 不支持`[toc]`目录语法, 但有替代组件. 还有加粗, 斜体语法有点奇怪等等, 这方面说不上缺陷, 只能说习惯typora的人用起来不太舒服

优点:

1. 多人协作. 感觉很厉害, 但我没用过
2. 颜值高. 不解释
3. 数据库.  适合有存在丰富单位的知识整合. 例如电影记录, 音乐记录, nba球员信息等

# 音乐记录-数据库

目前我自己对notion的使用主要是数据库功能, 下面用一个音乐记录的构建演示一下基本使用, 数据使用B站八七音乐的盘点视频:

[1965年的乐坛有多夸张？巅峰神作一首接一首，被震撼到了！_哔哩哔哩 (゜-゜)つロ 干杯~-bilibili](https://www.bilibili.com/video/BV1hU4y147Nq)

## 案例意义

* 理解relation的使用

## 创建

###创建relation关联的两个table

1. 盘点数据我们可以分为演唱者和歌曲两个实体, 我们创建两个table inline, 并添加好我们需要的基本字段

2. 演唱者和歌曲是一对多的关系, 我们在演唱者里添加歌曲字段, 使用relation组件

添加歌曲列, 使用Relation组件

![image-20210208160000775](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208161305.png)

选择要关联`歌曲`表

![image-20210208160219653](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208160219.png)

将对应歌曲添加到该列, 可添加多个

![image-20210208160337289](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208160337.png)

同时我们可以发现在`歌曲`表中自动生成了对`演唱者`者表的依赖列

![image-20210208160435475](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208160435.png)

至此我们完成了两个关联的演唱者和歌手数据库

### 模板插入

表格适合展示简短的信息, 我们如果要添加长内容, 可以点击第一列`OPEN`, 展开新page进行编辑

![image-20210208161804674](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208161804.png)

我们可以在该页面添加`简介`, `歌词`这种长内容, 但每次打开默认是空page, 而标题是通用的, 这里我们可以常见一个`Template`, 如下

![image-20210208162055924](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208162055.png)

`New`一个, 然后编辑

![image-20210208162129976](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208162130.png)

`OPEN`时使用该模板

![image-20210208162654031](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208162654.png)

### 创建视图

同一份数据,我们可能希望有不同的展示方式或只查看部分数据的需求, 这些可以通过创建不同的view实现

例如我们希望创建一个`民谣`歌曲视图

首先, `Add a new view`

![image-20210208174915874](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208174916.png)

![image-20210208185702598](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208185702.png)

进入新view, 点击table右上角三个点, 添加一个filter

![image-20210208175115423](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208175115.png)

选择风格`民谣`, 即创建了一个只展示民谣题材的视图

![image-20210208185732639](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208185732.png)

 

### Gallery View模式

Gallery是比较美观的展示方式, 重点是添加一列图片列

![image-20210208212811004](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208212811.png)

将图片列设置为preview

![image-20210208215450911](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208215450.png)

展示效果

![image-20210208215507219](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210208215507.png)

# 参考文献

[实践 Notion 构建作者与内容管理系统 - Linmi](https://linmi.cc/4480.html)

这篇文章将作者和内容通过relation整合在一起的案例比较有参考价值, 可以推演到很多场景

例如: 球队-球员, 乐队-歌曲

[大一统效率利器 Notion，我的使用技巧 | 年度征文 - 少数派](https://sspai.com/post/58436)

里面一个稿件整理的案例挺有意思的

[notion formula和relation+rollup的使用方法-以GPA计算为例_哔哩哔哩 (゜-゜)つロ 干杯~-bilibili](https://www.bilibili.com/s/video/BV1264y1F7PZ)

介绍relation和rollup的使用

[Notion Database 实践：使用 Notion 管理软件与服务订阅 - Linmi](https://linmi.cc/3373.html)

一个软件安装信息统计的