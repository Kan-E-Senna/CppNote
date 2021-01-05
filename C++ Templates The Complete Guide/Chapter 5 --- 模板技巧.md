# Chapter 5 --- 模板技巧

## typename关键字

这一部分看得不是太明白，书上写得也不是特别清楚，需要看到第13章才能回过头来再看，这里暂且按下。

## 零初始化

在使用模板时，我们可能希望如果T是一个built-in type的话，它可以初始化为0，我们当然不能这样写：

```c++
template <typename T>
class MyClass{
private:
    T x;
public:
    MyClass():x{0}{} // 错误，x未必就是一个built-in type
  	void foo(T p = 0);// 错误，p未必就是一个built-in type
}
```

改成这样就可以了：

```c++
template <typename T>
class MyClass{
private:
    T x;
public:
    MyClass():x{}{};
    // 如果使用C++11之前的版本需要下面的写法（花括号变小括号）
    // MyClass():x(){}
    void foo(T p = T{});
    // 如果使用C++11之前的版本需要下面的写法（花括号变小括号）
    // void foo(T p = T());
}
```

## Using this->

这一部分太基础，不记录了

## 原始数组与字符串字面量的特殊用法

我们可以把原始数组或者字符串字面量的长度作为模板参数，用法如下：

```c++
#include <iostream>

template<typename T, int N>
void count_arr(T(&a)[N]){
    std::cout << N << std::endl;
}

template<int N>
void count_constchar(const char (&a)[N]){
    std::cout << N << std::endl;
}

int main() {
    int arr[]{1,2,3,4,5};
    count_arr(arr);

    count_constchar("abcdefg");
    return 0;
}
```

特别注意的是，这种办法似乎只适用于原始数组和字符串字面量。

另外，从书中的示例代码来看，编译器可以很准确的判断出数组指针，有限数组，未定限数组，数组引用。下面把示例代码贴出：

```c++
/* basics/arrays.hpp*/
#include <iostream>

template<typename T>
struct MyClass;             // primary template

template<typename T, std::size_t SZ>
struct MyClass<T[SZ]>       // partial specialization for arrays of known bounds
{
  static void print() { std::cout << "print() for T[" << SZ << "]\n"; }
};

template<typename T, std::size_t SZ>
struct MyClass<T(&)[SZ]>    // partial spec. for references to arrays of known bounds
{
  static void print() { std::cout << "print() for T(&)[" << SZ << "]\n"; }
};

template<typename T>
struct MyClass<T[]>         // partial specialization for arrays of unknown bounds
{
  static void print() { std::cout << "print() for T[]\n"; }
};

template<typename T>
struct MyClass<T(&)[]>      // partial spec. for references to arrays of unknown bounds
{
  static void print() { std::cout << "print() for T(&)[]\n"; }
};

template<typename T>
struct MyClass<T*>          // partial specialization for pointers
{
  static void print() { std::cout << "print() for T*\n"; }
};
```

```c++
/* basic/arrays.cpp */
#include "arrays.hpp"

template<typename T1, typename T2, typename T3>
void foo(int a1[7], int a2[],    // pointers by language rules
         int (&a3)[42],          // reference to array of known bound
         int (&x0)[],            // reference to array of unknown bound
         T1 x1,                  // passing by value decays
         T2& x2, T3&& x3)        // passing by reference
{
  MyClass<decltype(a1)>::print();     // uses MyClass<T*>
  MyClass<decltype(a2)>::print();     // uses MyClass<T*>
  MyClass<decltype(a3)>::print();     // uses MyClass<T(&)[SZ]>
  MyClass<decltype(x0)>::print();     // uses MyClass<T(&)[]>
  MyClass<decltype(x1)>::print();     // uses MyClass<T*>
  MyClass<decltype(x2)>::print();     // uses MyClass<T(&)[]>
  MyClass<decltype(x3)>::print();     // uses MyClass<T(&)[]>
}

int main()
{
  int a[42];
  MyClass<decltype(a)>::print();      // uses MyClass<T[SZ]>

  extern int x[];                     // forward declare array
  MyClass<decltype(x)>::print();      // uses MyClass<T[]>

  foo(a, a, a, x, x, x, x);
}

int x[] = {0, 8, 15};                 // define forward-declared array
```

## 对成员函数使用模板

经常使用的场景是，我们可能在与其它对象交互时，考虑other的对象类型可能与自身不相同。譬如自身是Myclass\<int\>，可能需要考虑要交互的对象为Myclass\<double\>，这是需要对用于交互的方法套用模板。这部分内容较简单，不作过多笔记了。

（不过这一部分可能有坑存在，书上这一块写得很简略，需要往后看到更多细节。）

## 变量模板

我们可以这样来使用模板：

```c++
template <typename T>
constexpr T pi = T{3.1415926535897};

auto pi1{ pi<float> };
auto pi2{ pi<double> };
auto pi3{ pi<long double> };
```

可以得知，std::is_same_v, std:is_const_v等等都是这种变量模板。关于变量模板的更多实例需要看到书的后面。

## 对模板参数使用模板

我们有的时候有这样的需求，就是我们的模板参数也需要模板参数。譬如说，我们想实现一个自己的stack，除了需要一个typename T用来标明元素类型以外，我们还需要一个typename Container用来标明容器类型，因为stack可以用数组或者链表来实现。但是Container自己也需要接受模板参数。直接使用下面的写法会报错：

```c++
template<typename T, typename Container>
class Stack{
private:
    Container<T> cont; // ERROR
    //...
};
```

正确的写法是对Container再使用template，如下：

```c++
template<typename T, template<typename Ele> class Container>
class Stack{
private:
    Container<T> cont; // OK
    //...
};
```

注意一点，这里我们用的是class，在C++17之前的版本都必须用class才是正确的，c++17之后可以把class换成typename。

