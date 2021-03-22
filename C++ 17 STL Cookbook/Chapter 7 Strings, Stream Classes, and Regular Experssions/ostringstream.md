# ostringstream

ostringstream中的o，指的是output，它可以像std::cout那样输出对象，只不过它输出的地点不是标准输出，而是字符串缓冲区。

```c++
#include <iostream>
#include <string>
#include <sstream>

using namespace std::string_literals;

int main() {
    std::ostringstream os;
    std::string st{"1234"};
    os << st << " " << "abc" << " " << 759 << " " << 123.56;
    std::cout << os.str() << std::endl;
    os.str(""s); // 注意，利用.str("")来清空缓冲区
    os << "END";
    std::cout << os.str() << std::endl;
    return 0;
}
```

输出：

```shell
1234 abc 759 123.56
END
```

