# 参考文献

[MySQL8.0源码学习(8.0.18编译安装+debug基础调试)-阿里云开发者社区](https://developer.aliyun.com/article/727403)

2002错误解决
[mysql登录时报socket找不到终极解决方案_超人不会飞-CSDN博客](https://blog.csdn.net/u012062455/article/details/82349641)

# 编译环境

- MySQL-8.0.20的编译依赖gcc-5.3+/cmake3.5.2+
- gcc-5.3依赖库gmp/mpfc/mpc

## gcc

查看gcc版本

```
gcc --version
```

### 编译

[[Linux] 非root安装GCC9.1.0 - 小xuo生 - 博客园](https://www.cnblogs.com/jessepeng/p/11674780.html)

```
mkdir objdir
cd objdir
../configure --disable-checking --enable-languages=c,c++ --disable-multilib --prefix=/home/yjtang/module/gcc5 --enable-threads=posix
make -j64    # 多线程编译，否则很慢很慢很慢，能多开就多开几个线程
make install
```

### 修改环境变量

下面这篇文章在安装依赖处走了弯路, 按照前面那篇, 运行一下命令自动安装即可

[非root权限升级(修改当前用户)Linux gcc版本_OliverkingLi的博客-CSDN博客](https://blog.csdn.net/OliverkingLi/article/details/89645710?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)

```
export PATH=/home/yjtang/module/gcc5/bin:/home/yjtang/module/gcc5/lib64:$PATH
export LD_LIBRARY_PATH=/home/yjtang/module/gcc5/lib/:$LD_LIBRARY_PATH
```

 

## cmake

[Linux无root权限安装cmake - 简书](https://www.jianshu.com/p/16247d90fb3f)

/home/yjtang/module/cmake

```shell
cd cmakeXXXX
./bootstrap
./configure --prefix=/home/yjtang/module/cmake
make && make install
```

添加路径

vi ~/.bashrc

```
export PATH=/home/yjtang/module/cmake/bin:$PATH
```

source ~/.bashrc

测试完成

cmake -version

# 安装mysql

安装目录:

/home/yjtang/module/mysql

将设置默认端口号为33060

```
cmake ../ -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DSYSCONFDIR=/etc \
-DWITH_EXTRA_CHARSETS=all \
-DMYSQL_DATADIR=/usr/local/mysql/data \
-DWITH_BOOST=/usr/local/src/mysql-8.0.16/boost \
-DFORCE_INSOURCE_BUILD=1 \
-DCMAKE_CXX_COMPILER=/usr/local/gcc/bin/g++ \
-DDEFAULT_CHARSET=utf8
```

 

```
cmake ../ -DCMAKE_INSTALL_PREFIX=/home/yjtang/module/mysql \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=. \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DENABLED_LOCAL_INFILE=ON \
-DWITH_SSL=system \
-DMYSQL_DATADIR=/home/yjtang/module/mysql/data \
-DMYSQL_TCP_PORT=33060 \
-DCMAKE_CXX_COMPILER=/home/yjtang/module/gcc5/bin/g++ \
-DFORCE_INSOURCE_BUILD=1
 
 
#我的命令
cmake ../ -DCMAKE_INSTALL_PREFIX=/home/yjtang/module/mysql \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=. \
-DMYSQL_DATADIR=/home/yjtang/module/mysql/data \
-DMYSQL_UNIX_ADDR=/home/yjtang/module/mysql/mysql.sock \
-DSYSCONFDIR=/home/yjtang/module/mysql \
-DMYSQL_TCP_PORT=33060 \
-DCMAKE_CXX_COMPILER=/home/yjtang/module/gcc5/bin/g++ \
-DCMAKE_C_COMPILER=/home/yjtang/module/gcc5/bin/gcc \
-DFORCE_INSOURCE_BUILD=1
 
 
cmake ../ -DCMAKE_INSTALL_PREFIX=/home/yjtang/module/mysql -DMYSQL_DATADIR=/home/yjtang/module/mysql/data -DWITH_BOOST=1 -DFORCE_INSOURCE_BUILD=1
make && make install
```

 

```
make -j 12
make install
```

## 问题

### /usr/lib/libstdc++.so.6: version `CXXABI_1.3.9' not found

[非root权限解决/usr/lib64/libstdc++.so.6: version过低问题_Lemoneasy的专栏-CSDN博客](https://blog.csdn.net/liningeasy/article/details/21476217)

/home/yjtang/module/gcc5/lib64

export LD_PRELOAD=/home/yjtang/module/gcc5/lib64/libstdc++.so.6

### 无法找到socket

[mysql登录时报socket找不到终极解决方案_超人不会飞-CSDN博客](https://blog.csdn.net/u012062455/article/details/82349641)

# 使用mysql

[源码编译安装MySQL8.0 - 东瑜 - 博客园](https://www.cnblogs.com/zhangshengdong/p/9181650.html)

初始化数据库

```
./bin/mysqld --defaults-file=/home/yjtang/module/mysql/my.cnf --initialize-insecure
```

启动数据库

```
./bin/mysqld_safe --defaults-file=/home/yjtang/module/mysql/my.cnf &
./bin/mysql -uroot
```

关闭数据库

```
./bin/mysqld_safe --defaults-file=/home/yjtang/module/mysql/my.cnf &
```

 

完整的cnf配置文件

[单机多实例mysq 8.0l部署安装 - 东瑜 - 博客园](https://www.cnblogs.com/zhangshengdong/p/9181867.html)