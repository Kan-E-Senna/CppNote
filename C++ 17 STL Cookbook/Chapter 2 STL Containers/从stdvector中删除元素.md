# 从std::vector中删除元素

头文件：

```c++
#include <iostream>
#include <vector>
#include <algorithm>
```

## std::remove

假设有以下的vector

```c++
std::vector<int> v{1,2,3,4};
```

然后我们希望删去2这个元素，我们就可以使用remove

```c++
std::remove(v.begin(), v.end(), 2);
```

但是，如果你把新的vector输出出来，会发现问题

```c++
for(auto &e:v)std::cout << e << " ";
std::cout << std::endl;
```

```shell
1 3 4 4
```

末尾多了一个4。

这是因为，remove会把后面的元素利用赋值往前挪，但是原先vector的末尾没有谁赋值给它，因此没有动。remove事实上是有返回值的，它的返回值是一个迭代器，用以指代新的结尾。所以，经常使用的方式应该是这样：

```c++
const auto new_end{std::remove(v.begin(), v.end(), 2);}
```

这里的new_end是一个迭代器，它指向的是第二个4（注意end是数组中最后一个元素的下一个）。

这样设计的一个好处是有利于在一定程度上提高效率，但代价就是我们可能没法利用一些标准操作了，例如如果我们使用push_back，那么结果是1 3 4 4 5而不是1 3 4 5。因此我们可能仍然希望进行删除，这样就需要erase操作。

## erase

把新得到的new_end和v.end()作为参数传入erase即可。

```c++
v.erase(new_end, std::end(v));
```

这样再次输出v的值，就可以看到1 3 4了。

## 有关capacity

另一个要注意的点是，v的capacity在remove的过程中是不变的。

```c++
std::vector<int> v{1,2,3,4};
std::cout << v.capacity() << std::endl; // 4
const auto new_end{std::remove(v.begin(), v.end(), 2);}
std::cout << v.capacity() << std::endl; // 4
```

如果希望让v的capacity也变小，就需要使用shirnk_to_fit()

```c++
std::vector<int> v{1,2,3,4};
std::cout << v.capacity() << std::endl; // 4
const auto new_end{std::remove(v.begin(), v.end(), 2);}
std::cout << v.capacity() << std::endl; // 4
v.shrink_to_fit();
std::cout << v.capacity() << std::endl; // 3
```

