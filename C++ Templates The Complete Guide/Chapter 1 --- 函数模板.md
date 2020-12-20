# Chapter 1 --- 函数模板

## 1.1 声明一个函数模板

以两个值的max函数为例，如下所示:

```c++
#include <iostream>
template<typename T>
T max(T a, T b){
    return b < a?a:b;
}

int main() {
    std::cout << "::max(1, 2) = " << ::max(1,2) << std::endl;         // ::max(1, 2) = 2
    std::cout << "::max(1.5, 2.8) = " << ::max(1.5,2.8) << std::endl; // ::max(1.5, 2.8) = 2.8
    return 0;
}
```

（特别注意：max前面的双冒号代表调用全局函数。）

特别提醒，有时我们会看到下面的写法：

```c++
template<class T>
```

这是老式的，在C++98之前的写法，因为容易与类关键字混淆，C++98之后就使用typename了。但为了兼容旧版本，仍保留了这种写法。 

## 1.2 模板的编译过程（两阶段编译）

- Phase 1 (Definition Phase) : 检查与参数类型无关的语法错误（譬如半括号），未知的符号名，静态断言等。
- Phase 2 (Definition Phase) : 检查与参数有关的语法错误，譬如1.1中的max模板如果直接应用在一个complex对象上就会报错。 

```c++
#include <iostream>
#include <complex>
template<typename T>
T max(T a, T b){
    return b < a?a:b;
}

int main() {
    std::complex c1{1,2};
    std::complex c2{2,4};
    std::cout << "::max(1, 2) = " << ::max(c1,c2) << std::endl;         // Error
    return 0;
}
```

## 1.3 模板函数的参数问题

以上面的max函数为例，这里暗含一个要求，就是max函数的两个参数必须可以为同一类型（实际上更复杂，是可以被推断为同一类型，比如max(int, int const)也可以，但这里不想太详细解释了）。

```c++
template<typename T>
T max(T a, T b){
    return b < a?a:b;
}

int main(){
	std::cout << "::max(1, 2.2) = " << ::max(1, 2.2) << std::endl;         // Error
}
```

如果就是需要比较不同类型参数的大小，可以采取以下方法：

1. 类型转换，使用如下写法：

```c++
::max(static_case<double>(1), 2.2)
```

2. 指定模板参数：

```c++
::max<double>(1, 2.2)
```

3. 定义模板的时候就使用双模板参数。

## 1.4 默认模板参数

有时我们想指定默认的模板参数，像默认函数参数那样写即可。特别注意一下下面的例子

```c++
template<typename T>
void f(T a = ""){
    // ...
}

int main(){
    f(1); // OK
    f(); // Error
}
```

需要改成下面的写法：

```c++
#include <string>
template<typename T = std::string>
void f(T a = ""){
    // ...
}

int main(){
    f(); // OK
}
```



## 1.5 多个模板参数

 如前所述，有时我们想比较的对象一个是int，另一个是double，这时可以这么做：

```c++
template<typename RT, typename T1, typename T2>
RT max(T1 a, T2 b){
    return b < a?a:b;
}

int main(){
	std::cout << "::max(1, 2.2) = " << ::max<double>(1, 2.2) << std::endl;         // ::max(1, 2.2) = 2.2
}
```

但是这么做需要我们显式地指定返回类型（RT），不太方便，我们可以有如下的解决方案。

1. auto关键字

```c++
template<typename T1, typename T2>
auto max(T1 a, T2 b){
    return b < a?a:b;
}

int main(){
	std::cout << "::max(1, 2.2) = " << ::max(1, 2.2) << std::endl;         // ::max(1, 2.2) = 2.2
}
```

但这里的问题是有时不一定能特别清楚到底返回的是什么类型。（书中关于这里有稍多一些的文字，但此时尚未能理解）。

2. Common Type，也就是T1和T2都能转换过去的类型那个

```c++
template<typename T1, typename T2, typename RT = std::common_type_t<T1, T2>>
RT max(T1 a, T2 b){
    return b < a?a:b;
}

int main(){
	std::cout << "::max(1, 2.2) = " << ::max(1, 2.2) << std::endl;         // ::max(1, 2.2) = 2.2
}
```

还有一种类似于Common Type的写法，但此时尚未能理解：

```c++
template<typename T1, typename T2, typename RT = std::decay_t<decltype(ture?T1():T2())>>
RT max(T1 a, T2 b){
    return b < a?a:b;
}

int main(){
	std::cout << "::max(1, 2.2) = " << ::max(1, 2.2) << std::endl;         // ::max(1, 2.2) = 2.2
}
```

  ## 1.6 模板函数的重载

有时我们想对某些特殊的参数做特别的动作，比如两个参数都是int时输入一些信息，那么这是就可以直接写一个带两个int参数的max即可。

```c++
#include <iostream>

int max(int a, int b){
    std::cout << "(specialized max func) ";
    return b < a?a:b;
}

template<typename T>
T max(T a, T b){
    return b < a?a:b;
}
```

但是这里要特别注意的是，有时候因为强制类型转换的问题，导致一些意外情况发生，譬如：

```c++
int main() {
    std::cout << "::max(1, 20) = " << ::max(1,20) << std::endl;
    std::cout << "::max(1.2, 24.0) = " << ::max(1.1,24.0) << std::endl;
    std::cout << "::max('a', 'b') = " << ::max('a', 'b') << std::endl;
    std::cout << "::max<double>(1, 20) = " << ::max<double>(1, 20) << std::endl;
    std::cout << "::max('a', 5) = " << ::max('a', 5) << std::endl;
    return 0;
}
```

输出为：

```shell
::max(1, 20) = (specialized max func) 20
::max(1.2, 24.0) = 24
::max('a', 'b') = b
::max<double>(1, 20) = 20
::max('a', 5) = (specialized max func) 97
```

注意最后一行，'a' 与5是不同类型，所以调用的是第一个函数。

## 1.7 传值与传引用

事实上，这一部分的讨论是非常复杂的，特别是与重载这个话题连在一起更为复杂，Part 1中基本只讨论pass by value的情形，pass by reference在书的后面会再度详细解释。