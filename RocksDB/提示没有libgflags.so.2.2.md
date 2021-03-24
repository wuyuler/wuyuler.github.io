# 问题定义

一般被这个问题困扰都是因为都是已经安装gflag了, 但rocksdb却提示没有这个文件.

# 解决方法

- 编辑 vim /etc/profile 配置环境变量

- 设置以下内容

  ```
  export CPLUS_INCLUDE_PATH=${CPLUS_INCLUDE_PATH}:/usr/local/include
  export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/lib
  export LIBRARY_PATH=${LIBRARY_PATH}:/usr/local/lib
  ```

- 编辑后保存文件，source /etx/profile 配置生效

# 原因

首相我们要清楚linux系统是怎么管理共享库(*.so)的?

在 Linux 下面，共享库的寻找和加载是由 /lib/ld.so 实现的。 ld.so 在标准路经(**/lib**, **/usr/lib**) 中寻找应用程序用到的共享库。

但是，如果需要用到的共享库在非标准路经，ld.so 怎么找到它呢？传统上，Linux 的先辈 Unix 还有一个环境变量：**LD_LIBRARY_PATH** 来处理非标准路经的共享库。ld.so 加载共享库的时候，也会查找这个变量所设置的路经。

而rocksdb默认安装，include路径将为 **/usr/local/include** ，lib路径将为 **/usr/local/lib**

因此我们通过上面的方法解决问题

## LD_LIBRARY_PATH和LIBRARY_PATH的区别

LIBRARY_PATH和LD_LIBRARY_PATH是Linux下的两个环境变量，二者的含义和作用分别如下：

LIBRARY_PATH环境变量用于在***程序编译期间***查找动态链接库时指定查找共享库的路径，例如，指定gcc编译需要用到的动态链接库的目录。设置方法如下（其中，LIBDIR1和LIBDIR2为两个库目录）：

```
export LIBRARY_PATH=LIBDIR1:LIBDIR2:$LIBRARY_PATH
```

LD_LIBRARY_PATH环境变量用于在***程序加载运行期间***查找动态链接库时指定除了系统默认路径之外的其他路径，注意，LD_LIBRARY_PATH中指定的路径会在系统默认路径之前进行查找。设置方法如下（其中，LIBDIR1和LIBDIR2为两个库目录）：

```
export LD_LIBRARY_PATH=LIBDIR1:LIBDIR2:$LD_LIBRARY_PATH
```

# 参考文献

[(1条消息) LD_LIBRARY_PATH作用_GetnextWindow的专栏-CSDN博客](https://blog.csdn.net/GetNextWindow/article/details/23948485)

[(1条消息) LIBRARY_PATH和LD_LIBRARY_PATH环境变量的区别 LIBRARY_PATH和LD_LIBRARY_PATH是Linux下的两个环境变量，二者的含义和作用分别如下： LIBRAR_janeqi1987的专栏-CSDN博客](https://blog.csdn.net/janeqi1987/article/details/74346986?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242)