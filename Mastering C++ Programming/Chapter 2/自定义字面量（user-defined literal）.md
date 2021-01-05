# 自定义字面量（user-defined literal）

我们可能希望扩展我们的数字，譬如让我们的数字带单位。要做到这种事情我们可以定义一个类，譬如定义一个weight类，让kilogram, gram去继承这个类。但一般的使用办法可能会显得非常不够简洁。C++允许我们定义一种特殊的字面量自定义的方法，也可以理解为一种特殊的构造函数来得到这个类。下面是例子：

```c++
constexpr int operator""_KB(unsigned long long const size) {
    return static_cast<int>(size * 1024);
}

int main() {

    auto test{4_KB};
    std::cout << test << std::endl;
    return 0;
}
```

我们使用 operator ""_suffix() 这个函数来定义了任意一种以unsigned long long为前缀，\_suffix为后缀的字面量。这里双引号的里面放置的是参数，\_suffix是自定义的后缀，这个后缀未必就是\_KB，任意的字符串都可以。另外，不加下划线的后缀也是存在的，譬如复数库中的$i$，但是编译器只允许标准库这么使用。

**另外，这种函数强烈建议考虑constexpr。**

## 能接受的参数

需要注意的是，这种函数所能接受的参数是有限的，下面列出了它所能接受的参数：

```C++
T operator ""_suffix(unsigned long long int);
T operator ""_suffix(long double);
T operator ""_suffix(char);
T operator ""_suffix(wchar_t);
T operator ""_suffix(char16_t);
T operator ""_suffix(char32_t);
T operator ""_suffix(char const *, std::size_t);
T operator ""_suffix(wchar_t const *, std::size_t);
T operator ""_suffix(char16_t const *, std::size_t);
T operator ""_suffix(char32_t const *, std::size_t);
```

如果想要整数，参数必须是unsigned long long int，如果想要浮点数，参数必须是long double。然后可以在函数内部使用static_cast\<\>转换。

额外解释一下最后四个函数，后面的size_t不必显式地给出，而是由编译器进行推断：

```c++
#include <iostream>
#include <string>

std::string operator""_TEST(char const * s, std::size_t len){
    std::cout << len << std::endl;
    return std::string{s};
}

int main() {
    "abcd"_TEST;
    return 0;
}
```

会得到输出： 4。

## 补充：

### 利用字面量直接初始化string的方法

```c++
#include <iostream>
#include <string>

using namespace std::literals;
int main() {
    auto x{"abcd"s};
    std::cout << std::is_same_v<decltype(y), std::string> << std::endl;
    return 0;
}
```

像第6行一样，后缀加s。

