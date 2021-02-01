# std::bitset

用有时我们可能希望直接操控数据的二进制形式，这时我们可以使用std::bitset。

## 头文件

```c++
#include <bitset>
```

## 构造函数

从一个整数中直接构造（特别需要注意的是，bitset的大小必须在编译时期确定）：

```c++
std::bitset<8> bs{42}; // 00101010
```

从一个char const*中构造：

```c++
std::bitset<8> bs{"010101" };
```

从一个string中构造：

```c++
using namespace std::literals;
std::bitset<8> bs{"010101"s };
```

还可以从一个字符串的子串中构造，但是要求这个子串必须仅有两种字符类型：

```c++
using namespace std::literals;
std::bitset<8> bs{"ooooxxxx"s, 0, 10, 'o', 'x' };
```

0代表字符串的起始位置，10代表字符串的终止位置（这里10超过的字符串的长度，那么实际终止位置就在字符串的结尾处。很多时候我们更喜欢用std::string::npos来代替字符串终止位置。）'o'为代表0的字符，'x'为代表1的字符。

## API

count() 返回1的个数：

any() 返回是否有1，有1就返回1，反之返回0.

all()返回是否全为1，是则返回1，反之返回0.

none() 返回是否全为0，是则返回1，反之返回0.

test(0) 返回第0位，也就是最低有效位是否为0，可以使用其它参数。效果与bs[0]等价。

```c++
std::bitset<8> bs{42};
std::cout << bs.to_string() << std::endl;

std::cout << "bs.count(): " << bs.count() << std::endl;
std::cout << "bs.any(): " << bs.any() << std::endl;
std::cout << "bs.all(): " << bs.all() << std::endl;
std::cout << "bs.none(): " << bs.none() << std::endl;
std::cout << "bs.test(0): " << bs.test(0) << std::endl;
```

```shell
00101010
bs.count(): 3
bs.any(): 1
bs.all(): 0
bs.none(): 0
bs.test(0): 0
```

另外，可以将这种字节序列作位运算，这里不多介绍。

再有，可以将这种字节序列作转换，譬如：

```c++
bs.to_ulong();
bs.to_ullong();
bs.to_string();
```

但似乎只有这三种转换方式。

其它API需要查阅文档。

