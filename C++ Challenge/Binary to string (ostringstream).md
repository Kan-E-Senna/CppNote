# Binary to string (ostringstream)

> input: 0xBA
>
> output: "BA"

> input: 15
>
> output: "0F"

可以利用ostringstream来实现这一功能

```c++
#include <iostream>
#include <sstream>
#include <iomanip>

int main() {
    std::ostringstream oss;
    oss.setf(std::ios_base::uppercase);  // 设置format，字母全为大写
    oss << std::hex << std::setw(4) << std::setfill('0') << 15; 
    // std::hex设置十六进制输出，setw设置宽度，setfill设置空缺位置补0
    std::cout << oss.str() << std::endl;
    // 最后利用.str()得到字符串。
    return 0;
}
```

