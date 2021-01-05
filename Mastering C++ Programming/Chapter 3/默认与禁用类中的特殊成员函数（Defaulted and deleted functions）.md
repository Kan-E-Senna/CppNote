# 默认与禁用类中的特殊成员函数（Defaulted and deleted functions）

一般而言，C++中的类都有一些特殊成员函数：构造函数，析构函数，复制构造函数，赋值构造函数，移动构造函数，移动赋值构造函数。对于这些函数，一个一个全部显示地写可能会显得非常繁琐，C++提供了“=default”这个用法来显示说明这个函数由编译器自动生成。另外，我们处于设计模式的考量，需要设计出一些，譬如说，只能移动构造，不能复制构造的类。C++提供了“=delete”这个用法来帮助我们禁用某些特殊函数。

## =default

用法如下：

```c++
struct foo {
    int x, y;
    foo() = default;
    foo(const foo &) = default;
    foo& operator=(const foo &) = default;
}
```

## =delete

譬如说我们希望设计一个不可复制构造的函数：

```c++
class Point{
private:
    double x, y;
public:
    Point() = default;

    Point(Point const &) = delete;
    Point& operator=(Point const&) = delete;
};
```

像这样使用=delete禁用复制构造函数和赋值构造函数即可。

另外，=default只能用在类的特殊函数上，而=delete可以用在普通函数上。用在普通函数上时，如果使用了，则编译报错。