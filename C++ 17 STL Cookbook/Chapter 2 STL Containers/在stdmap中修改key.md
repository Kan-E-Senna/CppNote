# 在std::map中修改key

一般而言map中的key是const类型的，一般不能修改。硬要修改的话，策略是将这个键值对节点先删除，然后再插入一个新的键值对节点。C++17中提供了这样的接口，就是extract。

```c++
#include <iostream>
#include <map>
#include <string>

using namespace std::string_literals;
int main() {

    std::map<int, std::string> M{{1, "abc"s}, {2, "bcd"s}, {3, "efg"s}};
    return 0;
}

```

使用extract：

```c++
auto a = M.extract(1);
```

a的类型是一个node_handle，可以使用.key()来获取键，在这一步操作完成后，原先的map就不再含有以1为键的键值对了。此时就可以修改a这个node_handle来修改键。

```c++
a.key() = 7;
```

然后可以把a重新插回map之中，注意node_handle是一个move only的类型，只能使用move操作进行插入：

```c++
M.insert(std::move(a));
```

输出M的值可以观察效果：

```c++
for(auto &[K, V]:M)std::cout << K << " " << V << std::endl;
std::cout << std::endl;
```

```shell
2 bcd
3 efg
7 abc
```

