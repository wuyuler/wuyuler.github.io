# 	安装

## 环境

github仓库: https://github.com/facebook/rocksdb/

  操作系统：CentOS 7.x gcc 4.8

  编译前依赖：cmake，gflags，liblz4

## 依赖安装

liblz4

```
yum -y install lz4-devel
```

gflags

源码仓库：https://github.com/gflags/gflags，下载稳定版即可，目前是2.2.2

安装:

```
tar -xvzf gflags-2.2.2.tar.gz
cd gflags-2.2.2/
mkdir build
cd build/
```

开始编译

```
cmake -DBUILD_SHARED_LIBS=ON -DBUILD_STATIC_LIBS=ON -DINSTALL_HEADERS=ON -DINSTALL_SHARED_LIBS=ON -DINSTALL_STATIC_LIBS=ON ..
make
make install
```

 上面的这些选项就是动态库和静态库的选项，都打开就可以了，然后默认安装目录是：/usr/local，可以使用命令： ls /usr/local/lib/libgflags* -l 验证一下动态库是不是存在

  如果动态库都正常则安装成功了，默认头文件目录是/usr/local/include，库目录是/usr/local/lib，如果其他软件依赖于gflags那么在编译阶段系统会自动调用/usr/和/usr/local下面对应的库；在**运行阶段可能会提示so不存在的情况**，这时候只需要将动态库目录加入LD_LIBRARY_PATH或者添加/etc/ld.so.conf配置均可，比如 `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib `然后就可以了.

## RocksDB安装

  解压安装包并创建编译目录：

```
tar -xvzf rocksdb-5.18.3.tar.gz
cd rocksdb-5.18.3/
mkdir build
cd build
```

然后开始编译源代码：

```
cmake -DCMAKE_INSTALL_PREFIX=/usr/local/rocksdb ..
make
make install
```

设置头文件和lib环境变量

```
# rocksdb
export CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH:/usr/local/rocksdb/include/
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/rocksdb/lib64/
export LIBRARY_PATH=$LIBRARY_PATH:/usr/local/rocksdb/lib64/
# gflags
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
```

## 头文件设置

避免每次重新输入export命令, 我们将其写入`bash_profile`

```
vi ~/.bash_profile
```

在`bash_profile`中添加一下命令:

```
export CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH:/usr/local/rocksdb/include/
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/rocksdb/lib64/
export LIBRARY_PATH=$LIBRARY_PATH:/usr/local/rocksdb/lib64/
```



# 简单使用

## 测试代码

```c++
/*
 * @Author: your name
 * @Date: 2021-03-17 21:13:29
 * @LastEditTime: 2021-03-17 21:22:40
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /rocksdb-master/yjt_demo/first.cc
 */
#include <cstdio>
#include <string>

#include "rocksdb/db.h"
#include "rocksdb/slice.h"
#include "rocksdb/options.h"

using namespace std;
using namespace rocksdb;

const std::string PATH = "/rocksdb_tmp";

int main(){
    DB* db;
    Options options;
    options.create_if_missing = true;
    Status status = DB::Open(options, PATH, &db);
    assert(status.ok());
    Slice key("foo");
    Slice value("bar");
    
    std::string get_value;
    status = db->Put(WriteOptions(), key, value);
    if(status.ok()){
        status = db->Get(ReadOptions(), key, &get_value);
        if(status.ok()){
            printf("get %s\n", get_value.c_str());
        }else{
            printf("get failed\n"); 
        }
    }else{
        printf("put failed\n");
    }

    delete db;
}
```

## 编译运行

```shell
g++ -std=c++11 first.cc -o rocksdbtest -lpthread -lrocksdb
```

