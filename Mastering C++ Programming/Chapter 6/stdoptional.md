# std::optional

有的时候，我们可能希望根据值是否存在来进行相应的动作，在以往，我们通过指定一个不可能的值来设立flag，或者通过使用指针，通过空指针来达到目标，C++17后提供了std::optional来达到这个目标。

## 头文件

```c++
#include <optional>
```

注：需要c++17

##  声明

```c++
std::optional<int> var{42};
```

## 输出值

某种程度上可以把var看作是一种指针。也可以看作是对象从而用.value()来获取值。

```c++
std::cout << *var << std::endl; // 42
std::cout << var.value() << std::endl; //  42
```

```c++
struct foo {
    int a;
    double b;
};
std::optional<foo> var2{foo{24, 28.6}};
std::cout << var2->a << " " << var2->b << std::endl; // 24 28.6
// 注意这里是用->来取值
```

## 检测是否是空值

使用has_value即可

```c++
std::optional<int> var;
std::cout << var.has_value() << std::endl; // 0
```

