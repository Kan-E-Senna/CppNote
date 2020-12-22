# Chapter 4 --- 可变参数模板

如果我们希望实现允许可变参数的函数，就需要使用这一技术。

## 4.1 一个简单例子

```c++
void print(){}

template<typename T, typename... Types>
void print(T firstarg, Types... args){
    std::cout << firstarg << std::endl;
    print(args...);
}

int main() {
    print(1, 1.8, "Hello", "world");
    return 0;
}
```

```shell
1
1.8
Hello
world
```

从上面的例子中注意几点：

- 模板参数使用typename... 来标明后面的参数是一个参数pack。
- 传入参数pack的时候加...标明这是一个参数pack。
- 第一行的空函数必须有，因为下面的函数是递归地调用print，空的print函数作为递归的终点。

## 4.2 sizeof...

sizeof...可以用来指明参数pack中有多少个参数。

```c++
void print(){}

template<typename T, typename... Types>
void print(T firstarg, Types... args){
    std::cout << "sizeof...(args) = " << sizeof...(args) << std::endl;
    std::cout << firstarg << std::endl;
    print(args...);
}

int main() {
    print(1, 1.8, "Hello", "world");
    return 0;
}
```

```shell
sizeof...(args) = 3
1
sizeof...(args) = 2
1.8
sizeof...(args) = 1
Hello
sizeof...(args) = 0
world
```

需要特别注意的是， 我们可能会想到使用以下这种方式来终止递归：

```c++
template<typename T, typename... Types>
void print(T firstarg, Types... args){
    std::cout << firstarg << std::endl;
    if (sizeof...(args) > 0 ) print(args...);
}
```

这种写法是不可以的，因为编译器只要看见print(args...)就会要求单参数或者无参数的print函数存在（因为编译器知道这个是递归），而不管这个函数是不是真的运行。

## 4.3 折叠表达式

我们有如下的技巧来使用参数pack。

```c++
template<typename T>
auto foldSum(T... s) {
    return (... + s);
}
```

下表列出了折叠表达式的四种使用方式：

| Fold Expression         | Evaluation                                 |
| ----------------------- | ------------------------------------------ |
| ( ... op pack)          | ((pack1 op pack2) op pack3 ) ... op packN) |
| (pack op ... )          | (pack1 op (... op (packN-1 op packN))      |
| (init op .... op pack)  | ((init op pack1) op pack2 )  ... op packN) |
| ( pack op ... op init ) | (pack1 op ( ...(packN op init) )           |

   

## 4.4 例子

简要介绍STL中的std::enable_if_t，它是一个变量模板，接收两个模板参数，第一个为bool变量或表达式，第二个为类型，如：

```c++
std::enable_if_t<true, int> x;
```

这样x就是一个int。

我们有时需要参数pack中的参数类型相同，比如在初始化array的时候，这时在STL中就有如下的写法：

```c++
namespace std {
    template<typename T, typename... U>
	array(T, U...) -> array< 
        enable_if_t <
            (is_same_v<T, U> && ... ), T 
        >, 
        (1+sizeof...(U)) 
    >;
}
```

array的两个模板参数，一个是enable_if_t<...>，另一个是1+sizeof...(U)。第二个参数很好理解，是T和U所有参数的总和。重点是第一个参数。

先解释(is_same_v<T, U> && ... )，它实际上可以展开，变成如下的形式：

```c++
is_same_v<T, U1> && is_same_v<T, U2> && is_same_v<T, U3> && ... && is_same_v<T, U4>
```

也就是判断U里面的所有参数是否与T是同一类型，如果是的话为true，如果不是的话就是false。

然后就是 enable_if_t < (is_same_v<T, U> && ... ), T >，第一个参数的值如果是true，那么后面T的类型就生效，array的类型就是array\<T\>, 如果第一个参数的类型是false，那么编译器会报错。因为有了这一行代码，我们才能使用如下的初始化方式：

```c++
std::array myArr{1,2,3,4};
```

