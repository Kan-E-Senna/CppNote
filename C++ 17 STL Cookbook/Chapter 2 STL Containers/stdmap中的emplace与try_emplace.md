# std::map中的emplace与try_emplace

我们经常有需要在std::map中插入元素的需求。例如下面的例子：

```c++
#include <map>
#include <iostream>

class Test{
public:
    int value;
    Test(int n):value{n}{
        std::cout << "Construct: sequence number =  " << n << std::endl;
    }
};

int main() {
    std::map<int, Test> M;
    for(int i=1;i<=5;i++){
        M.emplace(i, i*i);
    }
    return 0;
}
```

如果我们希望插入一个6与Test{6}的组合，常有的做法是：

```c++
M[6] = Test{6};
```

但是这种做法并不好，首先我们没法知道原先的M[6]是否已经有值，其次我们这里插入的是一个对象，如果我们的要求是如果已经存在key，则使用原来的key-value对，如果不存在才新建一个对象，而这里是无论原先有没有key都会创建一个对象，这会带来效率上的下降。通常的做法是使用try_emplace。

```c++
auto [iter, success] = M.try_emplace(6, 36);
auto [iter, success] = M.try_emplace(2, 7);
```

try_emplace的第一个参数是key，后面是参数列表，用来构造value。如果key在map中已经存在，那么就不会使用参数列表构造对象，否则会使用参数列表构造对象。try_emplace的返回值有两个，第一个是一个迭代器，第二个是判断是否插入成功的flag，如果之前key在map中不存在，那么迭代器指向新建的对象，否则指向map中已经存在的键为key的对象。

```c++
auto [iter1, success1] = M.try_emplace(6, 36);
std::cout << std::boolalpha << "success = "  << success1 << std::endl;
std::cout << iter1->first << " " << iter1->second.value << std::endl;

auto [iter2, success2] = M.try_emplace(2, 7);
std::cout << std::boolalpha << (iter2 == M.end()) << std::endl;
std::cout << std::boolalpha << "success = "  << success2 << std::endl;
std::cout << iter2->first << " " << iter2->second.value << std::endl;
```

```shell
success = true
6 36
false
success = false
2 4
```

## emplace

emplace的使用与try_emplace一致，区别在于，emplace无论能否插入成功（emplace也是在key已经存在的时候不进行插入的），都会初始化对象，而try_emplace不会。

```c++
auto [iter1, success1] = M.emplace(6, 36);
std::cout << std::boolalpha << "success = "  << success1 << std::endl;
std::cout << iter1->first << " " << iter1->second.value << std::endl;

auto [iter2, success2] = M.emplace(2, 7);
std::cout << std::boolalpha << (iter2 == M.end()) << std::endl;
std::cout << std::boolalpha << "success = "  << success2 << std::endl;
std::cout << iter2->first << " " << iter2->second.value << std::endl;
```

```shell
Construct: sequence number =  36
success = true
6 36
Construct: sequence number =  7
false
success = false
2 4
```

注意上面的第7行，emplace没有修改原map的值。如果需要修改，应该利用迭代器进行修改。

## 建议

尽量使用try_emplace，如有需要，利用flag和迭代器进行修改原map。