# std::invoke

假设我们有一个函数：

```c++
int add(int const a, int const b) {
    return a+b;
}
```

通常的调用方式：

```c++
add(1, 2);
```

我们还有另一种调用方式：

```c++
std::invoke(&add, 1,2);
```

第一个参数是一个函数指针（注意是函数指针）。后面是参数列表。

当然，如果不希望添加那个&符号，也可以显式地声明一个函数指针：

```c++
int (*fadd)(int const a, int const b) = &add;
std::invoke(fadd, 1,2);
```

这种调用方式的好处在于，有时我们可能会不确定在运行过程中到底运行哪个函数，譬如，我们写了一个计算器，有加减乘除四种运算，但是事先无法确定具体哪一种运算。于是我们可以这样：

```c++
int add(int const, int const);
int dif(int const, int const);
int mul(int const, int const);
int div(int const, int const);

switch (signal){
    case "add" : int (*p)(int const, int const) = &add; break;
    case "dif" : int (*p)(int const, int const) = &dif; break;
    case "mul" : int (*p)(int const, int const) = &mul; break;
    case "div" : int (*p)(int const, int const) = &div; break;
}

std::invoke(p, a, b);
```

实际上，std::invoke的第一个参数是任何callable的东西，所以更多的用法还有待挖掘。