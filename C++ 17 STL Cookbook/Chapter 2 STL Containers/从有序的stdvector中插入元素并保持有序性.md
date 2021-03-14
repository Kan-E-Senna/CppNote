# 从有序的std::vector中插入元素并保持有序性

头文件：

```c++
#include <iostream>
#include <vector>
#include <algorithm>
```

对于下面的有序数组

```c++
std::vector<int> v{1,2,3,4,6,7};
```

如果我们希望在4与6之间插入一个5，那么需要找到一个位置，在这个位置之前的元素都比5小（在这个例子中是6的位置）。我们可以使用lower_bound来找到这一位置。

```c++
auto iter = std::lower_bound(v.begin(), v.end(), 5);
```

然后使用v.insert执行插入操作即可

```c++
v.insert(iter, 5);
for(auto &e:v) std::cout << e << " ";
std::cout << std::endl;
```

最后输出：

```shell
1 2 3 4 5 6 7
```

