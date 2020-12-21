 ## Chapter 2 --- 类模板

类模板的使用方法基本与函数模板类似，这里重点强调类模板的参数类型问题。

假设我们定义了如下的类：

```c++
template<typename T>
class myClass{
private:
    T data;
public:
    myClass(){};
    myClass(T t):data{t}{};
    T get(){ return data; }
};
```



## 2.1 类模板的参数

通常，我们需要指定模板参数。

```c++
myClass<int> example1;
myClass<double> example2;
myClass<std::string> example2;
```

不过，在我们的类中，存在单参数的构造函数，这意味着我们可以不给模板参数直接初始化，像这样：

```c++
myClass example4{25};
```

这样用的话，参数的类型就是由编译器来推断。但是这样的话会有一个问题，就是编译器推断出的类型可能不是你想要的类型，譬如：

```c++
int main(){
    myClass example5{"abcd"};
    std::cout << "is std::string? : " << std::is_same_v<decltype(example5.get()), std::string> << std::endl;
    std::cout << "is char const*? : " << std::is_same_v<decltype(example5.get()), std::string> << std::endl;
}
```

最终会得到：

```shell
is std::string? : 0
is char const*? : 1
```

不过我们可能更希望参数类型为string。这种情况下，可以显示地指定类型推导方式：

```c++
myClass(char const*) -> myClass<std::string>;
```

这样再次运行如下的代码：

```c++
int main(){
    myClass example5{"abcd"};
    std::cout << "is std::string? : " << std::is_same_v<decltype(example5.get()), std::string> << std::endl;
    std::cout << "is char const*? : " << std::is_same_v<decltype(example5.get()), std::string> << std::endl;
}
```

就可以得到：

```c++
is std::string? : 1
is char const*? : 0
```

要注意的是，指定推导方式只能由这个类的编写者来决定，在类的命名空间下指定。不可以由使用者来决定。