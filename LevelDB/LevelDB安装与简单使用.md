# git clone

```
git clone --recurse-submodules https://github.com.cnpmjs.org/wuyuler/leveldb
```

注意：`因为依赖子模块，不能简单git clone地址`

如果服务器下载速度太慢，可以本地下载上传

```
scp /mnt/c/Users/97168/Desktop/leveldb.zip root@42.193.181.16:/home/lighthouse/demo/read_levelDB
```

```
unzip leveldb.zip
```

# 编译

```
cd leveldb/
mkdir -p build && cd build
cmake -DCMAKE_BUILD_TYPE=Release .. && cmake --build .
```

# 安装

这样，在leveldb/build/目录下生成了一个静态库libleveldb.a，我们把这个静态库复制到/usr/local/lib/, 并把leveldb相关的头文件复制到/usr/local/include/

```
sudo cp build/libleveldb.a /usr/local/lib/
sudo cp -r include/leveldb/ /usr/local/include/
```

# 测试

```c++
#include <cassert>
#include <iostream>
#include <string>
#include <leveldb/db.h>
 
int main() {
  leveldb::DB* db;
  leveldb::Options options;
  options.create_if_missing = true;
  leveldb::Status status = leveldb::DB::Open(options, "/tmp/testdb", &db);
  assert(status.ok());
 
  std::string key = "apple";
  std::string value = "A";
  std::string get;
 
  leveldb::Status s = db->Put(leveldb::WriteOptions(), key, value);
 
  if (s.ok()) s = db->Get(leveldb::ReadOptions(), key, &get);
  if (s.ok()) std::cout << "读取到的与(key=" << key << ")对应的(value=" << get << ")" << std::endl;
  else std::cout << "读取失败!" << std::endl;
 
  delete db;
 
  return 0;
}
 
```

编译、链接(注: 缺少-pthread选项会报错undefined reference to `pthread_create’)：

```
g++ -o demo demo.cc -pthread -lleveldb -std=c++11
```

```
$ ./demo
```

读取到的与(key=apple)对应的(value=A)