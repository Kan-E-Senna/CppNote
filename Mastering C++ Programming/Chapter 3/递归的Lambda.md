# 递归的Lambda

要在lambda中使用递归，必须要使lambda具名，并且明确lambda的返回类型，因此不能再用auto来自动推断lambda的类型。

以阶乘函数为例：

```c++
std::function<int(int)> fact = [&fact](int const n){return n>=1?n*fact(n-1):1;};
// 不可以使用auto fact = ... 的写法
```

需要使用std::function<int(int)>，其中第一个int是返回类型，第二个int是参数类型那个，假如设计了一个函数参数有两个，分别是int, double，返回类型是一个string，那么写法就应该是：

```c++
std::function<std::string(int, double)> ...
```

再有就是需要注意引入头文件：

```c++
#include <functional>
```

最后再次提醒，匿名函数最好不要在全局范围内使用。