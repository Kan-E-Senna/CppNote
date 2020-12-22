# auto关键字

## 使用场景

auto用于让编译器来推断变量类型，多用于以下的场景：

- 声明变量避免冗余

```c++
auto s = std::string{"text"};
auto vect = std::vector<int> {1,2,3};
for(auto i = vect.begin();i!=vect.end();i++) {...}
```

- 命名匿名函数

```c++
auto upper = [](char const seq) {return toupper(seq);}
```

- 让编译器决定函数模板的返回值

```c++
template<typename T1, typename T2>
auto max(T1 a, T2 b){
    return a > b? a : b;
}
```

- 泛型编程（C++14之后可以用）

```c++
auto addfun = [](auto const a, auto const b) {return a+b;};
```

## 注意事项

1. 如果使用auto推断变量类型，不能作用于一个未初始化的变量。

```c++
auto i; // ERROR
```

2. auto只能推断具体的类型，不能判断附加属性（是否是const, volatile, 引用），这一点很重要，看下面的例子：

```c++
class foo{
private:
    int x_;
public:
    foo(int x):x_{x}{};
    int& get() {return x_;};
};


int main() {
    foo example1{15};
    auto x1 = example1.get();  // x1 is int, not int&  | Try to change "auto" to "decltype(auto)" 
    x1 = 100;
    std::cout << "x1 = " << example1.get() << std::endl;

    foo example2{25};
    int& x2 = example2.get();
    x2 = 100;
    std::cout << "x2 = " << example2.get() << std::endl;
    return 0;
}
```

最后的结果为：

```shell
x1 = 15
x2 = 100
```

这说明在第12行的auto并没有把x1的类型推断为int&，而是推断成了int。

要解决这个问题，把12行的auto改为decltype(auto)即可。

3. auto初始化变量时，不能推断由多个单词组成的类型名。

```c++
auto N = long long {42}; // ERROR
```

要解决这一问题，使用using进行重命名即可。

```c++
using ll = long long;
auto N = ll{42};
```