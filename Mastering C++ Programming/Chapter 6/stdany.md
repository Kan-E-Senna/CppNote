# std::any

有时我们可能需要在一个容器内放入多种数据类型的值，在以往，我们借助于void*来实现这个目标，但是这种方式是类型不安全的。C++为我们提供了std::any这个单元素容器来为我们实现这个目标。

**注：需要C++17**

## 头文件

```c++
#include <any>
```

为了使用字符串字面量，引入字符串字面量库：

```c++
#include <string>
using namespace std::literals;
```

另外，实测不引入string头文件也可以使用这些字面量。但最好还是引入string头文件。

## 初始化

```c++
std::any var{42};
// 也可以声明空值 std::any var;
```

## 赋值

```c++
var = "42"s;
var = 42.0;
```

这些都不会有问题。

## 值类型

用typeid来确认这些值的类型。

```c++
std::any var{42};
std::cout << (var.type() == typeid(int)) << std::endl;           // 1

var = "42"s;
std::cout << (var.type() == typeid(int)) << std::endl;           // 0
std::cout << (var.type() == typeid(std::string)) << std::endl;   // 1

var = 42.0;
std::cout << (var.type() == typeid(int)) << std::endl;           // 0
std::cout << (var.type() == typeid(std::string)) << std::endl;   // 0
std::cout << (var.type() == typeid(double)) << std::endl;        // 1
```

## 查看值

std::any不能直接使用std::cout进行输出，必须使用std::any_cast转换成普通类型再进行输出。

```c++
std::any var{42};
std::cout << std::any_cast<int>(var) << std::endl; // 42
```

需要特别注意的是，std::any_cast中的值类型必须与var的值类型完全相符，否则就会报错。

```c++
std::any var{42};
std::cout << std::any_cast<double>(var) << std::endl; // 报错
```

## 检查是否为空值

```c++
std::any var;
std::cout << var.has_value() << std::endl; // 0
var = 42;
std::cout << var.has_value() << std::endl; // 1
var.reset();
std::cout << var.has_value() << std::endl; // 0
```

使用has_value即可。另外，.reset()可以把一个any类型直接转换为空值。