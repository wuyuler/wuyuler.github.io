# 概述

zotero是我常用的文献管理软件

zotfile是zotero的插件，主要作用是将自定义附件的存储位置和文件命名规则

坚果云是存储附件的云盘

 

为什么要麻烦的配置这一套呢，主要原因：

1. zotero自带的同步功能不方便，想在其他设备查看比较麻烦
2. 一开始我直接使用坚果云同步，没有配置zotfile，但这里一个非常坑的问题，zotero默认存储的居然是附件的压缩包，也就是说我只能通过zotero打开论文，这也直接导致我无法在移动设备上查看，非常难受
3. 使用zotfile自定义附件存储位置，保存的也是原始的pdf

# 配置zotfile

1. 设置zotero使用相对路径,并设置根目录(根目录需要于zotfile存储目录相同)
2. 设置zotfile存储目录(选择一个`坚果云同步的文件夹`即可)
3. (可选) 开启子文件夹, 设置子文件夹命名规则

我常用的命名规则

```
//%y year (extracted from Date field)
//%w name of the journal or publisher
{%y}{-%w}
```

效果示例:

```
2010-Proceedings of the VLDB Endowment
```

图示:

![image-20210210125823565](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210210125825.png)

论文pdf命名修改

![image-20210210163314183](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210210163314.png)

# 坚果云同步设置

基于zotfile存储后, 我们zotero只是需要配置坚果云`存储元数据`即可

![image-20210210130335178](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210210130335.png)