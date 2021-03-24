# Formatting output

有时我们希望我们的输出符合某种规范，这时需要对iostream进行调整。

## 头文件

```c++
#include <iostream>
#include <iomanip>
```

### 例一：

```c++
int main() {
    std::cout << std::setfill('_');  
    std::cout << std::left << std::setw(8) << 1234 << std::endl;
    std::cout << std::right << std::setw(8) << 1234 << std::endl;
    return 0;
}
```

std::setfill设置填充字符，std::left和std::right设置内容的位置，std::setw设置输出内容的宽度，不满宽度的部分使用填充字符进行填充。

```
234____
____1234
```

### 例二：

```c++
int main() {
    std::cout << std::setfill('_');
    std::cout << std::internal << std::setw(8) << std::hex << std::showbase << 0x1234 << std::endl;
    return 0;
}
```

std::internal设置填充位置在中间，这一项需要联合std::showbase才能看出，不使用std::showbase的样式与std::right效果相同。std::hex指示以十六进制位输出。（std::oct八进制，std::dec十进制，没有二进制的format方法，只能转换成bitset）

```
0x__1234
```

### 例三：

```c++
int main() {
    std::cout << std::setfill('_');
    std::cout << std::hex << std::showbase << std::setw(8) << std::uppercase << 1234 << std::endl;
    std::cout << std::uppercase << "abcd" << std::endl;
    return 0;
}
```

std::uppercase要求把输出中的0x大写为0X。注意，这个不能用在输出字符串上。

```
___0X4D2
abcd
```

### 例四：

```c++
int main() {
    std::cout << 12.0 << std::endl;
    std::cout << std::showpoint << 12.0 << std::endl;
    return 0;
}
```

std::showpoint要求输出的数字显示小数点以及小数点后的0

```
12
12.0000
```

### 例五:

```c++
int main() {
    std::cout << std::scientific << 1230000000.456 << std::endl;
    std::cout << std::fixed << 1230000000.456 << std::endl;
    return 0;
}
```

std::scientific显示科学计数法，std::fixed显示原生数字。

```
1.230000e+09
1230000000.456000
```

### 例六：

```c++
int main() {
    std::cout << std::fixed << std::setprecision(8) << 0.000001 << std::endl;
    std::cout << std::fixed << std::setprecision(2) << 0.000001 << std::endl;
    return 0;
}
```

std::setprecision设置精度

```
0.00000100
0.00
```



还有更多的format用法，可以查看文档。