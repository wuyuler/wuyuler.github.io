# 目录

[TOC]

# 常用功能
## 初始化数组为某个数值

### 初始化char数组为空

>(1) char str[10]="";
>(2) char str[10]={'\0'};
>(3) char str[10]; str[0]='\0';

第(1)(2)种方式是将str数组的所有元素都初始化为'\0'，而第(3)种方式是只将str数组的第一个元素初始化为'\0'。如果数组的size非常大，那么前两种方式将会造成很大的开销。所以，除非必要（即我们需要将str数组的所有元素都初始化为0的情况），我们都应该选用第(3)种方式来初始化字符串数组。
### 实现_memset
#### 应用场景
通常用于对一块已分配的内存初始化

#### 注意事项

1. memset命令的第三个参数，是以字节为单位的，因此不同类型的数组，要计算`完整的内存字节长度`做为第三参数。这里利用的是数组的内存连续性
2. memset是按照`字节`对待初始化空间进行初始化的，也就是说，函数里面的第二个参数的那个初值（一般为0）是按照一个一个字节往第一个参数所指区域赋值的，所以，对于`单字节`数据类型（char）可以初始化为任意支持的值，都没有问题，但是对于非多字节数据类型`只能初始化为0和-1`，而不能初始化成别的初值，因为对所有字节按任意顺序赋值0的结果都是0，而如果初始化为其他的值，就会一个字节一个字节的进行赋值，从而出现奇怪的结果。比如说，上面的例3之所以没有出错就是因为初始化为0，但是如果初始化为1，那么因为int一般是4个字节，那么相当于将一个int元素初始化成了**0000 0001 0000 0001 0000 0001 0000 0001**
   ####语法

```c
#include <cstring>
memset(s,0,sizeof(s));
```

### 实现_fill
#### 应用场景
按照单元赋值，将一个区间的元素都赋同一个值

#### 语法

```c
#include <algorithm>
fill(arr, arr + n, 要填入的内容);
```

例子:

```c
#include <cstdio>
#include <algorithm>
using namespace std;
int main() {
    int arr[10];
    fill(arr, arr + 10, 2);
    return 0;
}
```

### memset和fill对比
memset函数按照字节填充，所以一般memset只能用来填充char型数组，（因为`只有char型`占一个字节）如果填充int型数组，除了0和-1，其他的不能。因为只有00000000 = 0，-1同理(1000000000000001取反后是1111111111111110，再+1即1111111111111111,补码表示的11111111111111111,C里面数是以补码方式来储存的)，如果我们把每一位都填充“1”，会导致变成填充入“11111111”
而fill函数可以赋值任何
##宏定义
define命令是C语言中的一个宏定义命令，它用来将一个标识符(宏名)定义为一个字符串，该标识符被称为宏名，被定义的字符串称为替换文本。程序编译之前，编译的时候所有的宏名都会被定义的字符串替换，这便是宏替换。

### 用法

```c
#define PI 3.14
float pi2 = PI * 2;//pi2 = 6.28
```

## 字符串与数字的转化
### 实现_基础加减

```c
int b = a - '0'; //字符转成数字
char ch=b+'0'; //数字转成字符
```

### 实现_sstream

两次变化不能使用同一个ss,使用完以此要清空 sstream.clear()

1. 数字转字符串

```c
#include <string>
#include <sstream>
int main(){
    double a = 123.32;
    string res;
    stringstream ss;
    ss << a;
    ss >> res;//或者 res = ss.str();
    return 0;
}
```

2. 字符串转数字

```c
int main(){
    string a = "123.32";
    double res;
    stringstream ss;
    ss << a;
    ss >> res;
    return 0;
}
```

## 特殊值

### 最大最小

```java
long long inorder = (long long)INT_MIN - 1;
```

 

# 常用库

## string.h(c语言字符处理库 )
### API

* strcpy

```c
#include <stdio.h>
#include <string.h>
 
 
int main()
{
   char src[40];
   char dest[100];
   memset(dest, '\0', sizeof(dest));
   strcpy(src, "This is runoob.com");
   strcpy(dest, src);
   printf("最终的目标字符串： %s\n", dest);
   return(0);
}
```

* strlen

```c
size_t strlen(const char *str)
```

* strcmp

```c
int strncmp(const char *str1, const char *str2, size_t n)
```

返回值:
如果返回值 < 0，则表示 str1 小于 str2。
如果返回值 > 0，则表示 str2 小于 str1。

如果返回值 = 0，则表示 str1 等于 str2。
---

## algorithm

```c
#include <algorithm>
 
int *earliest = new int[n];
fill(earliest, earliest + n, 0);
```

### max,min

```c++
#include<iostream>
#include<algorithm>
using namespace std;
struct var {
    char *name;
    int key;
    var(char *a,int k):name(a),key(k){}
};
bool comp(const var& l, const var& r) {
    return l.key < r.key;
}
int main() {
    var v1("var1", 2);
    var v2("var2", 3);
    cout << std::min(v1, v2,comp).name << endl;
    return 0;
}
```

 

## string

头文件

```c
#include <string>
```

### 迭代器

```c
#include <iostream>
#include <string>    //要与c语言里面的#include <string.h>区分
using namespace std;
int main(){
string str("abcdefg");
string::iterator ite;
 
ite = str.begin();
for (size_t i = 0; i < str.size(); i++){
  cout << *ite;
  ite++;
}
ite = str.begin();
for (size_t i = 0; i < str.size(); i++){
  cout << ite[i];
}
cout << endl;
ite = str.begin();
for (; ite != str.end(); ite++){
  cout << *ite;
}
return 0;
}
```

### 初始化
语法

```c
  string();
  string( size_type length, char ch );
  string( const char *str );
  string( const char *str, size_type length );
  string( string &str, size_type index, size_type length );
  string( input_iterator start, input_iterator end );
```

字符串的构造函数创建一个新字符串，包括:

>以length为长度的ch的拷贝（即length个ch）
>以str为初值 (长度任意),
>以index为索引开始的子串，长度为length, 或者
>以从start到end的元素为初值.
>###操作符
>你可以用 ==, >, <, >=, <=, and !=比较字符串. 可以用 + 或者 += 操作符连接两个字符串, 并且可以用[]获取特定的字符.
>###添加文本
>语法

```c
basic_string &append( const basic_string &str );
  basic_string &append( const char *str );
  basic_string &append( const basic_string &str, size_type index, size_type len );
  basic_string &append( const char *str, size_type num );
  basic_string &append( size_type num, char ch );
  basic_string &append( input_iterator start, input_iterator end );
```

append() 函数可以完成以下工作:

>在字符串的末尾添加str,
>在字符串的末尾添加str的子串,子串以index索引开始，长度为len
>在字符串的末尾添加str中的num个字符,
>在字符串的末尾添加num个字符ch,
>在字符串的末尾添加以迭代器start和end表示的字符序列.
>###转为c字符串指针
>语法:

```c
const char *c_str();
```

### 删除(erase)
#### 注意事项
#### 基础

```c
iterator erase( iterator pos );
iterator erase( iterator start, iterator end );
basic_string &erase( size_type index = 0, size_type num = npos );
```

erase()函数可以:

>删除pos指向的字符, 返回指向下一个字符的迭代器,
>删除从start到end的所有字符, 返回一个迭代器,指向被删除的最后一个字符的下一个位置
>删除从index索引开始的num个字符, 返回*this.
>####循环删除
>#####注意事项

1. 每次删除ite自动指向下一个元素,不需要移动

   ##### code

```c
string::iterator ite;
  ite = s.begin();
  for (; ite != s.end();) {
   if (*ite >= 'a'&& *ite <= 'z' || (*ite >= 'A'&& *ite <= 'Z')||*ite==' ') {
    ++ite;
   }
   else {
    ite = s.erase(ite);
   }
  }
```

### 查找(find)
语法:

```c
  size_type find( const basic_string &str, size_type index );
  size_type find( const char *str, size_type index );
  size_type find( const char *str, size_type index, size_type length );
  size_type find( char ch, size_type index );
```

find()函数:

>返回str在字符串中第一次出现的位置（从index开始查找）。如果没找到则返回string::npos,
>返回str在字符串中第一次出现的位置（从index开始查找，长度为length）。如果没找到就返回string::npos,
>返回字符ch在字符串中第一次出现的位置（从index开始查找）。如果没找到就返回string::npos
>###插入
>语法:

```c
  iterator insert( iterator i, const char &ch );
  basic_string &insert( size_type index, const basic_string &str );
  basic_string &insert( size_type index, const char *str );
  basic_string &insert( size_type index1, const basic_string &str, size_type index2, size_type num );
  basic_string &insert( size_type index, const char *str, size_type num );
  basic_string &insert( size_type index, size_type num, char ch );
  void insert( iterator i, size_type num, const char &ch );
  void insert( iterator i, iterator start, iterator end );
```

insert()函数的功能非常多:

>在迭代器i表示的位置前面插入一个字符ch,
>在字符串的位置index插入字符串str,
>在字符串的位置index插入字符串str的子串(从index2开始，长num个字符),
>在字符串的位置index插入字符串str的num个字符,
>在字符串的位置index插入num个字符ch的拷贝,
>在迭代器i表示的位置前面插入num个字符ch的拷贝,
>在迭代器i表示的位置前面插入一段字符，从start开始，以end结束.
>###substr
>语法:

```c
basic_string substr( size_type index, size_type num = npos );
```

substr()返回本字符串的一个子串，从index开始，长num个字符。如果没有指定，将是默认值 string::npos。这样，substr()函数将简单的返回从index开始的剩余的字符串

***

### split

```c
#include <iostream>
#include <iterator>
#include <string>
#include <sstream>
#include <vector>
#include <algorithm>
vector<string> &split(const string &str, char delim, vector<string> &elems, bool skip_empty = true) {
    istringstream iss(str);
    for (string item; getline(iss, item, delim); )
        if (skip_empty && item.empty()) continue;
        else elems.push_back(item);
    return elems;
}
```

***

### istringstream
C++引入了ostringstream、istringstream、stringstream这三个类，要使用他们创建对象就必须包含<sstream>这个头文件。
istringstream类用于执行C++风格的串流的输入操作。
ostringstream类用于执行C风格的串流的输出操作。
strstream类同时可以支持C风格的串流的输入输出操作。
#### 应用场景
它的作用是从string对象str中读取字符。

#### 基础语法

```c
//istringstream的构造函数原形如下：
istringstream::istringstream(string str);
```

```c
#include<iostream>
#include<sstream>        //istringstream 必须包含这个头文件
#include<string>
using namespace std;
int main()
{
    string str="i an a boy";
    istringstream is(str);
    string s;
    while(is>>s)
    {
        cout<<s<<endl;
    }
 
}
输出是:
i
am
a
boy
```

***

## math.h
abs
原型：extern int abs(int x);
用法：#include <math.h>
功 能：求整数x的绝对值
说明：计算|x|, 当x不为负时返回x，否则返回-x

 

fabs
原 型：extern float fabs(float x);
用法：#include <math.h>
功 能：求浮点数x的绝对值
说明：计算|x|, 当x不为负时返回x，否则返回-x

 

floor原 型：extern float floor(float x);
用法：#include <math.h>
功 能：求不大于x的最达整数
说明：返回x的下限，如74.12的下限为74，-74.12的下限为-75。返回值为float类型。

 

pow原 型：extern float pow(float x, float y);
用法：#include <math.h>
功 能：计算x的y次幂。
说明：x应大于零，返回幂指数的结果。

 

sqrt原 型：extern float sqrt(float x);
用法：#include <math.h>
功 能：计算x的平方根。
说明：x应大于等于零。

***

## vector

```c++
#include<vector>
//初始化size,但每个元素值为默认值
vector<int> abc(10);    //初始化了10个默认值为0的元素
//初始化size,并且设置初始值
vector<int> cde(10，1);    //初始化了10个值为1的元素
//添加
vec.push_back(a);
//大小
vec.size();
```

### 基本操作
(1)头文件#include<vector>.
(2)创建vector对象，vector<int> vec;
(3)尾部插入数字：vec.push_back(a);
(4)使用下标访问元素，cout<<vec[0]<<endl;记住下标是从0开始的。
(5)使用迭代器访问元素.
vector<int>::iterator it;
for(it=vec.begin();it!=vec.end();it++)
    cout<<*it<<endl;
(6)插入元素： vec.insert(vec.begin()+i,a);在第i+1个元素前面插入a;
(7)删除元素： vec.erase(vec.begin()+2);删除第3个元素
vec.erase(vec.begin()+i,vec.end()+j);删除区间[i,j-1];区间从0开始
(8)向量大小:vec.size();
(9)清空:vec.clear();

pop_back()可以删除最后一个元素

vectorT.back();访问最后一个元素

### 初始化

1. vector<int> ilist1;
   默认初始化，vector为空， size为0，表明容器中没有元素，而且 capacity 也返回 0，意味着还没有分配内存空间。这种初始化方式适用于元素个数未知，需要在程序中动态添加的情况。
2. vector<int> ilist2(ilist);
   vector<int> ilist2  = ilist;
   两种方式等价 ，ilist2 初始化为ilist 的拷贝，ilist必须与ilist2 类型相同，也就是同为int的vector类型，ilist2将具有和ilist相同的容量和元素
3. vector<int> ilist = {1,2,3.0,4,5,6,7};
   vector<int> ilist {1,2,3.0,4,5,6,7};
   ilist 初始化为列表中元素的拷贝，列表中元素必须与ilist的元素类型相容，本例中必须是与整数类型相容的类型，整形会直接拷贝，其他类型会进行类型转换
   4.vector<int> ilist3(ilist.begin()+2,ilist.end()-1);
   ilist3初始化为两个迭代器指定范围中元素的拷贝，范围中的元素类型必须与ilist3 的元素类型相容，在本例中ilist3被初始化为{3,4,5,6}。注意：由于只要求范围中的元素类型与待初始化的容器的元素类型相容，因此迭代器来自不同的容器是可能的，例如，用一个double的list的范围来初始化ilist3是可行的。另外由于构造函数只是读取范围中的元素进行拷贝，因此使用普通迭代器还是const迭代器来指出范围并没有区别。这种初始化方法特别适合于获取一个序列的子序列
4. vector<int> ilist4(7);
   默认值初始化，ilist4中将包含7个元素，每个元素进行缺省的值初始化，对于int，也就是被赋值为0，因此ilist4被初始化为包含7个0。当程序运行初期元素大致数量可预知，而元素的值需要动态获取的时候，可采用这种初始化方式。
5. vector<int> ilist5(7,3);
   指定值初始化，ilist5被初始化为包含7个值为3的int

### 排序

```c
#include<algorithm>
sort(resKeys.begin(),resKeys.end());
```

默认**升序**

#### 自定义函数

```c
bool GreaterSort (Point2 a,Point2 b) { return (a.x>b.x); }
bool LessSort (Point2 a,Point2 b) { return (a.x<b.x); }
 
 
sort(aaa.begin(),aaa.end(),GreaterSort);//降序排列
sort(aaa.begin(),aaa.end(),LessSort);//升序排列
```

##数组
###使用数组函数参数
方式 1
形式参数是一个指针：
void myFunction(int *param)
方式 2
形式参数是一个已定义大小的数组：
void myFunction(int param[10])
方式 3
形式参数是一个未定义大小的数组：
void myFunction(int param[])

### 动态申请二维数组

```c
void DynamicCreate2Array()
{
    int m,n;
    cout<<"请输入行和列：";
    cin>>m>>n;
 
    //动态开辟空间
    int **p = new int*[m]; //开辟行
    for(int i = 0; i < m; i++)
        p[i] = new int[n]; //开辟列
 
    cout<<"请输入数据：";
    for(i = 0 ; i < m ; i++)
        for(int j = 0; j < n; j++)
            cin>>p[i][j];
 
    cout<<"输出数据："<<endl;
    for(i = 0; i < m; i++)
    {
        for(int j = 0; j < n; j++)
            cout<<setw(3)<<p[i][j];
        cout<<endl;
    }
 
    //释放开辟的资源
    for(i = 0; i < m; i++)
        delete[] p[i];
    delete[] p;
 
}
```

 

 

### 二维数组做函数参数

要指定第二维的层数,即第二维必须是**常数**

```c
 
int array[10][10];
 
函数声明：void fuc(int a[][10]);
 
函数调用：fuc(array);
 
 
 
 
int *array[10];
 
for(i = 0; i < 10; i++)
 
    array[i] = new int[10];
 
函数声明：void fuc(int *a[10]);
 
函数调用：fuc(array);
```

### 1. 获取数组长度


容量:即定义的大小

```c
sizeof(array) / sizeof(array[0])
 
```

字符数:

```c
#include<string.h>
cout<<strlen(buffer)<<endl;
```

### 2. 赋相同的初值
#输入输出

参考文献:
[C++获取字符cin,getchar,get,getline的区别](https://www.cnblogs.com/shrimp-can/p/5241544.html)
## 输入
### 1. cin>>
#### 1）最常见的是获取输入的**一个字符或数字**，如
int a,b;
cin>>a>>b；
注意：cin>>会自动过滤掉不可见字符（如空格 回车 tab等）。若不想过滤掉空白字符，可以用noskipws流进行控制。
如下程序，没有过滤掉不可见字符，输入的空格字符存入了input[1]中，也可输出。

#### 2）获取输入的字符串，可以用数组或string类型。如
char a[20];
cin>>a;
cout<<a<<endl;
或者string类型：
string s;
cin>>s;
cout<<s<<endl;
注意：遇到空格、回车等会结束获取输入的字符串，后面的字符串会过滤掉（存放在输入流中）。如果后面还需要输入字符串，则会从前面存放的字符串开始获取。

 

### 2. cin.get()

1）cin.get(字符变量名)，用来接收字符，只获取一个字符，可以接收空格，遇回车结束

 

2）cin.get(数组名，接收字符数目)，用来接收字符串，可以接收空格，遇回车结束。

注意：数组的最后一个字符会是‘\0’，设接收字符数目为n，如果输入的字符串大于等于n，则实际接收到的输入是字符串的前面n-1个字符，包括空格（不包括回车，遇到回车就结束了），会自动在后面增加一个‘\0’。

 

3）cin.get()，没有参数，主要用于舍弃输入流中不需要的字符，或者舍弃回车，即舍弃输入流中的一个字符。

没有用cin.get()，则后面的s会从输入流中继续读入

 

有cin.get()，则h后面的s被省略了。

 

### 实现_cin.getline()

#### 应用场景

接受char数组存储的字符串

#### 注意事项

1. cin.getline()可以接收空格，但不能接收回车

2. 不能接受到string类

3. 实际接收到的要比设置的接受数量少一个，因为最后一个字符为'\0'

#### 语法

```c
 
cin.getline(接收字符串到m，接收个数n，结束字符)
 
 
```

例子:

### 实现_getline()
#### 应用场景
`只用于string类`,输入一行数据,接受空格,回车

#### 注意事项

1. 接受回车,如果使用该方法前面有输入换行,要清除掉,可以使用getchar(),或者多调用一次getline
   ####语法

```c
#include< string >
getline(cin,string s)
istream& getline (istream&  is, string& str, char delim);
```

#### comp_cin.getline,getline()
1.cin.getline()接收输入字符串的是数组，getline（）是string类型。
2.cin.getline()可以接收空格，但不能接收回车；getline()可以接收空格和回车
3.cin.getline()会在数组结尾是'\0'，getline()不会
### 实现_scanf
#### 注意事项

1.  非指针,要带取地址符&
2.  scanf()的格式控制串可以使用其它非空白字符，但在输入时必须输入这些字符。

>scanf("%d,%d",&a,&b);
>输入： 3，4 ↙（逗号与"%d,%d"中的逗号对应）
>scanf("a=%d,b=%d",&a,&b);
>输入： a=3，b=4 ↙（"a=","b=",逗号与"%d,%d"中的"a=","b="及逗号对应）

3. 在用"%c"输入时，空格和“转义字符”均作为有效字符。

>scanf("%c%c%c",&c1,&c2,&c3);
>输入：a□b□c↙
>结果：a→c1，□→c2，b→c3 (其余被丢弃)
>####语法

* 格式字符:

>%c                 读入一个字符
>%d                 读入十进制整数
>%s                 读入一个字符串
>%f                 读入一个浮点数


* 输入单个数字或字符

```c
# include <stdio.h>
int main(void)
{
    int i, j;
    scanf("%d%d", &i, &j);
    printf("i = %d, j = %d\n", i, j);
    return 0;
}
```


* 输入字符串

```c
# include <stdio.h>
int main(void)
{
    char str[10];  //str是string的缩写, 即字符串
    printf("请输入字符串：");
    scanf("%s", str);  /*输入参数是已经定义好的“字符数组名”, 不用加&, 因为在C语言中数组名就代表该数组的起始地址*/
    printf("输出结果：%s\n", str);
    return 0;
}
```

### 多组输入

```c
//多个
while(cin>>n)
//多行
while(getline(cin,a))
```

 

 

## 输出

### 使用printf控制精度

%m.n : m指域宽，即对应的输出项在输出设备上所占的字符数。n指精度。用于说明输出的实型数的小数位数。为指定n时，隐含的精度为n=6位

```c
#include <stdio.h>
d格式：用来输出十进制整数。有以下几种用法：
%d：按整型数据的实际长度输出。
%md：m为指定的输出字段的宽度。如果数据的位数小于m，则左端补以空格，若大于m，则按实际位数输出
%02d，和%2d差不多，只不过左边补0
 
 
%s：例如:printf("%s", "CHINA")输出"CHINA"字符串（不包括双引号）
%ms：输出的字符串占m列，如果字符串本身长度大于m，则突破获m的限制,将字符串全部输出。若串长小于m，则左补空格。
%-ms：如果串长小于m，则在m列范围内，字符串向左靠，右补空格。
%m.ns：输出占m列，但只取字符串中左端n个字符。这n个字符输出在m列的右侧，左补空格。
%-m.ns：其中m、n含义同上，n个字符输出在m列范围的左侧，右补空格。如果n>m，则自动取n值，即保证n个字符正常输出。
 
f格式：用来输出实数（包括单、双精度），以小数形式输出。有以下几种用法：
%f：不指定宽度，整数部分全部输出并输出6位小数。
%m.nf：输出共占m列，其中有n位小数，若数值宽度小于m左端补空格。
%-m.nf：输出共占m列，其中有n位小数，若数值宽度小于m右端补空格。
 
e格式：以指数形式输出实数。可用以下形式：
%e：数字部分（又称尾数）输出6位小数，指数部分占5位或4位。
%m.ne和%-m.ne：m、n和”-”字符含义与前相同。此处n指数据的数字部分的小数位数，m表示整个输出数据所占的宽度。
```

关于printf函数的进一步说明：
如果想输出字符"%",则应该在“格式控制”字符串中用连续两个%表示，如:
printf("%f%%", 1.0/3);
输出0.333333%。

对于单精度数，使用%f格式符输出时，仅前7位是有效数字，小数6位．
对于双精度数，使用%lf格式符输出时，前16位是有效数字，小数6位．

**可变宽参数**

对于m.n的格式还可以用如下方法表示（例）

char ch[20];
printf("%*.*s\n",m,n,ch);

前边的*定义的是总的宽度，后边的定义的是输出的个数，分别对应外面的参数m和n
我想这种方法的好处是可以在语句之外对参数m和n赋值，从而控制输出格式。

# 文件读写

参考文献:
[C++文件读写详解（ofstream,ifstream,fstream）](https://www.cnblogs.com/hdk1993/p/5853233.html)

```cpp
#include <fstream>
ofstream         //文件写操作 内存写入存储设备
ifstream         //文件读操作，存储设备读区到内存中
fstream          //读写操作，对打开的文件可进行读写操作
```

## 打开文件
在fstream类中，成员函数open（）实现打开文件的操作，从而将数据流和文件进行关联，通过ofstream,ifstream,fstream对象进行对文件的读写操作
函数：open（）

```c
public member function
void open ( const char * filename,
            ios_base::openmode mode = ios_base::in | ios_base::out );
void open(const wchar_t *_Filename,
        ios_base::openmode mode= ios_base::in | ios_base::out,
        int prot = ios_base::_Openprot)；
```

**参数**： filename   操作文件名
           mode        打开文件的方式
           prot         打开文件的属性                            //基本很少用到，在查看资料时，发现有两种方式
打开文件的方式在ios类(所以流式I/O的基类)中定义，有如下几种方式：

>ios::in    为输入(读)而打开文件
>ios::out    为输出(写)而打开文件
>ios::ate    初始位置：文件尾
>ios::app    所有输出附加在文件末尾
>ios::trunc    如果文件已存在则先删除该文件
>ios::binary    二进制方式


这些方式是能够进行组合使用的，以“或”运算（“|”）的方式：例如

```c
ofstream out;
out.open("Hello.txt", ios::in|ios::out|ios::binary)
```

## 关闭文件
当文件读写操作完成之后，我们必须将文件关闭以使文件重新变为可访问的。
**成员函数close()**，它负责将缓存中的数据排放出来并关闭文件。这个函数一旦被调用，原先的流对象就可以被用来打开其它的文件了，这个文件也就可以重新被其它的进程所访问了。为防止流对象被销毁时还联系着打开的文件，析构函数将会自动调用关闭函数close。
##文本文件的读写
类ofstream, ifstream 和fstream 是分别从ostream, istream 和iostream 中引申而来的。这就是为什么 fstream 的对象可以使用其父类的成员来访问数据。
一般来说，我们将使用这些类与同控制台(console)交互同样的成员函数(cin 和 cout)来进行输入输出。如下面的例题所示，我们使用重载的插入操作符<<：
##文本文件的写操作

### code

```c
  // writing on a text file
#include <fiostream.h>
int main () {
     ofstream out("out.txt");
     if (out.is_open())
    {
         out << "This is a line.\n";
         out << "This is another line.\n";
         out.close();
     }
     return 0;
}
//结果: 在out.txt中写入：
This is a line.
This is another line
```

## 文本文件的读操作
### 注意事项

1. 使用in.getline时注意一行的长度,放在txt文件里看似拐了一行,其实可能根本只有一行

   ### code

```c
#include <iostream>
#include <fstream>
#include <string>
//读取方式: 逐词读取, 词之间用空格区分
//read data from the file, Word By Word
//when used in this manner, we'll get space-delimited bits of text from the file
//but all of the whitespace that separated words (including newlines) was lost.
void ReadDataFromFileWBW()
{
    ifstream fin("data.txt");
    string s;
    while( fin >> s )
    {
        cout << "Read from file: " << s << endl;
    }
}
 
 
//读取方式: 逐行读取, 将行读入字符数组, 行之间用回车换行区分
//If we were interested in preserving whitespace,
//we could read the file in Line-By-Line using the I/O getline() function.
void ReadDataFromFileLBLIntoCharArray()
{
    ifstream fin("data.txt");
    const int LINE_LENGTH = 100;
    char str[LINE_LENGTH];
    while( fin.getline(str,LINE_LENGTH) )
    {
        cout << "Read from file: " << str << endl;
    }
}
 
 
//读取方式: 逐行读取, 将行读入字符串, 行之间用回车换行区分
//If you want to avoid reading into character arrays,
//you can use the C++ string getline() function to read lines into strings
void ReadDataFromFileLBLIntoString()
{
    ifstream fin("data.txt");
    string s;
    while( getline(fin,s) )
    {
        cout << "Read from file: " << s << endl;
    }
}
 
 
//带错误检测的读取方式
//Simply evaluating an I/O object in a boolean context will return false
//if any errors have occurred
void ReadDataWithErrChecking()
{
    string filename = "dataFUNNY.txt";
    ifstream fin( filename.c_str());
    if( !fin )
    {
        cout << "Error opening " << filename << " for input" << endl;
        exit(-1);
    }
}
```

###状态标志符的验证(Verification of state flags)
除了eof()以外，还有一些验证流的状态的成员函数（所有都返回bool型返回值）：

* bad()
  如果在读写过程中出错，返回 true 。例如：当我们要对一个不是打开为写状态的文件进行写入时，或者我们要写入的设备没有剩余空间的时候。
* fail()
  除了与bad() 同样的情况下会返回 true 以外，加上格式错误时也返回true ，例如当想要读入一个整数，而获得了一个字母的时候。
* eof()
  如果读文件到达文件末尾，返回true。
* good()
  这是最通用的：如果调用以上任何一个函数返回true 的话，此函数返回 false 。
  要想重置以上成员函数所检查的状态标志，你可以使用成员函数clear()，没有参数。

***

# STL
## 栈

```c
#include<iostream>
#include<stack>
using namespace std;
int main()
{
stack<int> mys;
mys.push(9);
mys.push(3);
mys.push(2);
cout<<"mys.size: "<<mys.size()<<endl;
while(!mys.empty())
{
  cout<<mys.top()<<endl;
  mys.pop();
}
return 0;
}
 
 
```

***

## 队列
### 头文件：
include < queue >

### API
push(x) 将x压入队列的末端
pop() 弹出队列的第一个元素(队顶元素)，注意此函数并不返回任何值
front() 返回第一个元素(队顶元素)
back() 返回最后被压入的元素(队尾元素)
empty() 当队列为空时，返回true
size() 返回队列的长度

```c++
queue<string> q;
q.push("Hello World!");
q.push("China");
cout<<q.front()<<endl;
```

 

## priority_queue优先队列
### 应用场景

1. 队列中最大的元素总是位于队首
   ###头文件
    #include <queue>
   ###初始化

### 基本操作
empty() 　　   如果队列为空，则返回真
pop()　　　　删除对顶元素，删除第一个元素
push() 　　     加入一个元素
size() 　　　  返回优先队列中拥有的元素个数
top() 　　　　返回优先队列对顶元素，返回优先队列中有最高优先级的元素

### 注意事项

1. 重载运算符
   operator<(const Edge & e)const ,后面这个const不能省略

### 自定义优先级
priority_queue<vector<int>, less<int> > pq1; 　　　 // 使用递增less<int>函数对象排序
priority_queue<deque<int>, greater<int> > pq2; 　　// 使用递减greater<int>函数对象排序


1. 结构体中重载运算符

```c
bool operator<(const Edge & e)const{
  return weight > e.weight;//改为最小堆
}
```

```c
struct node
{
    int x, y;
    friend bool operator < (node a, node b)
    {
        return a.x > b.x;//结构体中，x小的优先级高
    }
};
priority_queue<node> q4;
```

2. 对指针类型(注意重载操作符不能对指针)

```c
struct Compare {
bool operator()(const HufNode *h1, const HufNode * h2) {
  return h1->data > h2->data;//小的优先级高,由于使用的是!cmp1，所以就变成小的排最前
}
};
//初始化
priority_queue<HufNode*,vector<HufNode*>,Compare> q;
for (int i = 0; i < n; i++) {
  int tmp;
  cin >> tmp;
  HufNode* h = new HufNode(tmp);
  q.push(h);
}
```

***

## 堆(heap)
参考文献:
[C++标准库中的堆-heap](https://blog.csdn.net/flyyufenfei/article/details/78175511)
注意:`C++中堆(heap)是在vector的基础上实现的`

### 头文件

```c
#include<algorithm>
```

### API

>make_heap: 根据指定的迭代器区间以及一个可选的比较函数，来创建一个heap. O(N)
>push_heap: 把指定区间的最后一个元素插入到heap中. O(logN)
>pop_heap: 弹出heap顶元素, 将其放置于区间末尾. O(logN)
>sort_heap：堆排序算法，通常通过反复调用pop_heap来实现. N*O(logN)

### 详解
#### make_heap
STL中的通过make_heap创建的堆，默认是大顶堆(max heap)，即每个非叶子节点元素的值均不”小于”(默认使用<作为比较准则)其左右孩子节点。要改变堆的建立准则，可以自己制定一个比较函数，如下第二个版本的make_heap声明:


// 1
template< class RandomIt >
void make_heap( RandomIt first, RandomIt last );


// 2
template< class RandomIt, class Compare >
void make_heap( RandomIt first, RandomIt last, Compare comp );
示例代码：


默认的make_heap

```c
vector<int> vi{6, 1, 2, 5, 3, 4};
printContainer(vi, "vi: ");             // vi: 6 1 2 5 3 4
 
 
make_heap(vi.begin(), vi.end());
printContainer(vi, "vi: ");             // vi: 6 5 4 1 3 2
```

需要注意的是，make_heap需使用随机迭代器来创建heap。


自己指定比较函数的make_heap

```c
vector<int> v2{6, 1, 2, 5, 3, 4};
printContainer(v2, "v2 before make_heap: ");
make_heap(v2.begin(), v2.end(), greater<int>());
printContainer(v2, "v2 after make_heap: ");
```

输出：


v2 before make_heap: 6 1 2 5 3 4
v2 after make_heap: 1 3 2 5 6 4
这里使用了greater<int>()来代替默认的less<int>()来创建int类型的heap。可以按层次遍历的顺序把这个heap画出来，可以看到它跟默认情况刚好相反，会是一个小顶堆。其中,greater从大到小排序,内部时`return (_Left > _Right);` less从小到大,内部`return (_Left < _Right);` `返回值为bool类型`

#### push_heap
// 1
template< class RandomIt >
void push_heap( RandomIt first, RandomIt last );


// 2
template< class RandomIt, class Compare >
void push_heap( RandomIt first, RandomIt last, Compare comp );
与make_heap类似，push_heap也有两个版本，其中有一个版本可以指定堆的比较函数，并且也是以一对迭代器指定的区间来进行操作。


示例代码：


vector<int> v1{6, 1, 2, 5, 3, 4};
make_heap(v1.begin(), v1.end());


v1.push_back(200);
printContainer(v1, "before push_heap: ");        // before push_heap: 6 5 4 1 3 2 200
push_heap(v1.begin(), v1.end());
printContainer(v1, "after push_heap: ");         // after push_heap: 200 5 6 1 3 2 4
先用make_heap来构造一个堆，然后在容器末尾追加元素之后，把新的迭代器区间传给push_heap，这样新尾部元素也被添加到堆中。


注意：使用push_heap(f, l)的话，调用者需要确保[f, l-1)已经是一个堆. push_heap(f, l)仅仅会把*(l-1)插入到[f, l-1)这个区间形成的堆中，时间复杂度是O(logN).


即, STL书中所述：the caller has to ensure, on entry, the elements in the range [begin, end) are a heap(according to the same sorting criterion).


如果一开始不用make_heap处理，直接push_heap会怎样？


vector<int> v2{6, 1, 2, 5, 3, 4};
v2.push_back(200);
printContainer(v2, "v2 before push_heap: ");
push_heap(v2.begin(), v2.end());
printContainer(v2, "v2 after push_heap: ");
输出：


v2 before push_heap: 6 1 2 5 3 4 200
v2 after push_heap: 200 1 6 5 3 4 2
可以看出直接调用push_heap的结果并不是一个heap. 下面要提到的pop_heap也有同样的要求。

#### pop_heap
// 1
template< class RandomIt >
void pop_heap( RandomIt first, RandomIt last );


// 2
template< class RandomIt, class Compare >
void pop_heap( RandomIt first, RandomIt last, Compare comp );


Swaps the value in the position first and the value in the position last-1 and makes the subrange [first, last-1) into a max heap. This has the effect of removing the first (largest) element from the heap defined by the range [first, last).
它的作用是：交换*first和*(last-1)， 然后把[first, last-1)建成一个max heap. 也就是说把堆顶的最大元素交换到区间尾，然后把除了尾部的元素的剩余区间重新调整成堆。


需要注意的是，调用者要保证，在调用pop_heap时[first, last)已经是一个堆(使用相同的排序准则)。

```c
vector<int> v1{6, 1, 2, 5, 3, 4};
make_heap(v1.begin(), v1.end());
printContainer(v1, "after make_heap: ");
 
 
pop_heap(v1.begin(), v1.end());
printContainer(v1, "after pop_heap: ");
auto largest = v1.back();
psln(largest);
v1.pop_back();
printContainer(v1, "delete largest: ");
```

输出：


after make_heap: 6 5 4 1 3 2
after pop_heap: 5 3 4 1 2 6
largest = 6
delete largest: 5 3 4 1 2
####sort_heap
// 1
template< class RandomIt >
void sort_heap( RandomIt first, RandomIt last );


// 2
template< class RandomIt, class Compare >
void sort_heap( RandomIt first, RandomIt last, Compare comp );
sort_heap即经典的堆排序算法，通过每次弹出堆顶直到堆为空，依次被弹出的元素就组成了有序的序列了。STL中的priority_queue即使用heap的这个特性来实现。


使用sort_heap(f, l)处理过的区间因为已经有序，就不再是一个heap了。


vector<int> v1{6, 1, 2, 5, 3, 4};
printContainer(v1, "before sort_heap: ");


make_heap(v1.begin(), v1.end());


sort_heap(v1.begin(), v1.end());
printContainer(v1, "after sort_heap: ");
输出：


before sort_heap: 6 1 2 5 3 4
after sort_heap: 1 2 3 4 5 6
注意：调用者仍需确保区间已经是一个堆。

#### is_heap
// (1)    (since C++11)
template< class RandomIt >
bool is_heap( RandomIt first, RandomIt last );


// (2)    (since C++11)
template< class RandomIt, class Compare >
bool is_heap( RandomIt first, RandomIt last, Compare comp );
示例：


vector<int> v1{6, 1, 2, 5, 3, 4};
psln(is_heap(v1.begin(), v1.end()));


pln("after make_heap");


make_heap(v1.begin(), v1.end());
psln(is_heap(v1.begin(), v1.end()));
输出：


is_heap(v1.begin(), v1.end()) = 0
after make_heap
is_heap(v1.begin(), v1.end()) = 1
is_heap_until
原型:


// (1)    (since C++11)
template< class RandomIt >
RandomIt is_heap_until( RandomIt first, RandomIt last );


// (2)    (since C++11)
template< class RandomIt, class Compare >
RandomIt is_heap_until( RandomIt first, RandomIt last, Compare comp );
示例：


vector<int> v1{6, 1, 2, 5, 3, 4};
auto iter = is_heap_until(v1.begin(), v1.end());
psln(*iter);        // *iter = 5    5 是第一个不满足heap条件的位置。


make_heap(v1.begin(), v1.end());
iter = is_heap_until(v1.begin(), v1.end());
ASSERT_TRUE(iter == v1.end());

***

## map
### 应用场景

1. 牺牲空间获取时间
   ###基础API

```c
#include <map>//要使用map,必须包含此头文件.
map<string,int> M;//定义一个完成从string到int映射的map
M.clear(); //清空一个map
M.find(b);//确定map中是否保存string对象b的映射,若没有函数返回M.end()
M[b] = idx; //若map中不存在string对象b的映射,则定义其映射为b映射为idx
idxb = M[b]; //若map中存在string对象b的映射,则读出该映射
iterator erase（iterator it);//通过一个条目对象删除
 
iterator erase（iterator first，iterator last）//删除一个范围
 
size_type erase(const Key&key);//通过关键字删除
```

### 常见用法
#### 判断是否已经存在对象的映射

```c
if (M.find(a) == M.end())
```

***

#### 遍历

```c
using namespace std;
 
 
int main() {
    map<int, int> _map;
    _map[0] = 1;
    _map[1] = 2;
    _map[10] = 10;
 
 
    map<int, int>::iterator iter;
    iter = _map.begin();
    while(iter != _map.end()) {
        cout << iter->first << " : " << iter->second << endl;
        iter++;
    }
 
 
    // 也可以使用for循环遍历
    /*
    for(iter = _map.begin(); iter != _map.end(); iter++) {
        cout << iter->first << " : " << iter->second << endl;
    }
    */
    return 0;
}
```

#### 排序

```c
bool cmp_by_value(const PAIR& lhs, const PAIR& rhs) {
  return lhs.second < rhs.second;
}
 
struct CmpByValue {
  bool operator()(const PAIR& lhs, const PAIR& rhs) {
    return lhs.second < rhs.second;
  }
};
int main() {
  map<string, int> name_score_map;
  name_score_map["LiMin"] = 90;
  name_score_map["ZiLinMi"] = 79;
  name_score_map["BoB"] = 92;
  name_score_map.insert(make_pair("Bing",99));
  name_score_map.insert(make_pair("Albert",86));
//把map中元素转存到vector中
  vector< pair<string, int> > name_score_vec(name_score_map.begin(), name_score_map.end());
  sort(name_score_vec.begin(), name_score_vec.end(), CmpByValue());
// sort(name_score_vec.begin(), name_score_vec.end(), cmp_by_value);
  for (int i = 0; i != name_score_vec.size(); ++i) {
    cout << name_score_vec[i] << endl;
  }
  return 0;
}
```

# new关键字

```c
int *a = new int[5];
class A {...}   //声明一个类 A
A *obj = new A();  //使用 new 创建对象
delete []a;
delete obj;
```

## 初始化


```
int *pia = new int[10]; // 每个元素都没有初始化
int *pia2 = new int[10] ();  // 每个元素初始化为0
int* c=new char[10] () //初始化为空
 
```

 

***

# 运算符重载
## 注意
不能对指针重载

## 二元运算符

```c
// 重载 + 运算符，用于把两个 Box 对象相加
   Box operator+(const Box& b)
   {
      Box box;
      box.length = this->length + b.length;
      box.breadth = this->breadth + b.breadth;
      box.height = this->height + b.height;
      return box;
   }
class Box
{
/**
    * 改写部分 2018.09.05
    * 重载 + 运算符，用于把两个 Box 对象相加
    * 因为其是全局函数，对应的参数个数为2。
    * 当重载的运算符函数是全局函数时，需要在类中将该函数声明为友员。
    */
   friend Box operator+(const Box& a, const Box& b);
 
 
};
 
 
Box operator+(const Box& a, const Box& b)
{
    Box box;
    box.length = a.length + b.length;
    box.breadth = a.breadth + b.breadth;
    box.height = a.height + b.height;
    // cout << box.length << "--" << box.breadth << "--" << box.height << endl;
    return box;
}
```

## 比较运算符

```c
// 重载小于运算符（ < ）
      bool operator <(const Distance& d)
      {
         if(feet < d.feet)
         {
            return true;
         }
         if(feet == d.feet && inches < d.inches)
         {
            return true;
         }
         return false;
      }
```

## 赋值运算符

```c
void operator=(const Distance &D )
      {
         feet = D.feet;
         inches = D.inches;
      }
```

## 输入输出符重载

```c
class Distance
{
   private:
      int feet;             // 0 到无穷
      int inches;           // 0 到 12
   public:
      // 所需的构造函数
      Distance(){
         feet = 0;
         inches = 0;
      }
      Distance(int f, int i){
         feet = f;
         inches = i;
      }
      friend ostream &operator<<( ostream &output,
                                       const Distance &D )
      {
         output << "F : " << D.feet << " I : " << D.inches;
         return output;     
      }
 
      friend istream &operator>>( istream  &input, Distance &D )
      {
         input >> D.feet >> D.inches;
         return input;     
      }
};
int main()
{
   Distance D1(11, 10), D2(5, 11), D3;
 
   cout << "Enter the value of object : " << endl;
   cin >> D3;
   cout << "First Distance : " << D1 << endl;
   cout << "Second Distance :" << D2 << endl;
   cout << "Third Distance :" << D3 << endl;
 
 
   return 0;
}
```

# 面向对象
## 对象初始化
第一种方式是显式的调用构造函数。（在栈上分配内存
A a = A();
A a = A(1);
另一种方式是隐式的调用构造函数，格式更紧凑。（在栈上分配内存
A a;    // 等价于 A a = A();
A a(1);    // 等价于 A a = A(1);
构造函数还可以与new一起使用。（在堆中动态分配内存。
A a = new A();    // 记得要 delete a;
A a = new A(1);    // 记得要 delete a;
注：
A a;隐式使用默认构造函数时，变量a后面不能加括号，
否则A a();会被编译器认为是函数声明。