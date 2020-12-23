# 获取数字属性（numeric_limits）

虽然名字里面有一个"limit"，但是不完全就是用来查询最大值的，它可以用来获取一些数值的属性。

## 示例

```c++
#include <iostream>
#include <limits>

template<typename T>
void print_type_properties(){
    std::cout
    << "min = "         << std::numeric_limits<T>::min()        << "\n"
    << "max = "         << std::numeric_limits<T>::max()        << "\n"
    << "bits = "        << std::numeric_limits<T>::digits       << "\n"
    << "decdigits = "   << std::numeric_limits<T>::digits10     << "\n"
    << "is integer = "  << std::numeric_limits<T>::is_integer   << "\n"
    << "is signed = "   << std::numeric_limits<T>::is_signed    << "\n"
    << "is exact = "    << std::numeric_limits<T>::is_exact     << "\n"
    << "has infinity = "<< std::numeric_limits<T>::has_infinity << "\n"
    << std::endl;
}


int main() {
    std::cout << "unsigned short:" << std::endl;
    print_type_properties<unsigned short>();
    std::cout << "int:" << std::endl;
    print_type_properties<int>();
    std::cout << "long:" << std::endl;
    print_type_properties<long>();
    std::cout << "double:" << std::endl;
    print_type_properties<double>();
    return 0;
}
```

输出：

```shell
unsigned short:
min = 0
max = 65535
bits = 16
decdigits = 4
is integer = 1
is signed = 0
is exact = 1
has infinity = 0

int:
min = -2147483648
max = 2147483647
bits = 31
decdigits = 9
is integer = 1
is signed = 1
is exact = 1
has infinity = 0

long:
min = -2147483648
max = 2147483647
bits = 31
decdigits = 9
is integer = 1
is signed = 1
is exact = 1
has infinity = 0

double:
min = 2.22507e-308
max = 1.79769e+308
bits = 53
decdigits = 15
is integer = 0
is signed = 1
is exact = 0
has infinity = 1
```

