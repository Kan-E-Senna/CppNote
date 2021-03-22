# String_view

有的时候我们不希望进行字符串的拷贝，只希望读取字符串，此时可以使用string_view，它不真的拥有一个字符串，而是有一个指向这个字符串的指针，以及明确能读取的范围大小的值。

```c++
#include <string_view>
#include <iostream>
#include <cctype>
int main() {
    std::string st{"This Is A String"};
    std::string st2{};
    std::string_view sv{st};

    std::cout << "String: " << st << std::endl;
    std::cout << "String view: " << sv << std::endl;
    std::transform(std::begin(st), std::end(st), st.begin(), ::toupper); 
    // toupper这个函数在本次测试中不能加std前缀，似乎是需要c++20
    std::cout << "After transform" << std::endl;
    std::cout << "String: " << st << std::endl;
    std::cout << "String view: " << sv << std::endl;
    return 0;
}
```

输出：

```shell
String: This Is A String
String view: This Is A String
After transform
String: THIS IS A STRING
String view: THIS IS A STRING
```

