# Chapter 6 --- 移动语义与enable_if<>

## 有关右值引用

我们有的时候希望避免使用值传递，因此我们需要写一个传引用的函数，如下：

```c++
int add(int& a, int& b){
    return a+b;
}
```

注意，当你写出这个函数的时候，如下的值传递函数就不可以被使用了，因为编译器会不知道你到底想要用哪个函数：

```c++
int add(int a, int b){  // You cannot use this when you already have a pass-by-reference function
    return a+b;
}
```

但是这样会带来一个问题，万一我们想要做如下的事情怎么办？

```c++
add(1, 2); // ERROR

int a = 1, b = 2, c = 3, d = 4;
add(a + c, b * d); // ERROR
```

有两种办法，一种就是把上面pass-by-reference的函数给改成pass-by-value。但是我们的add函数的参数是个很大的对象的话就很麻烦了。我们仍然希望pass-by-reference。为什么前面的例子会报错，那是因为字面量，以及这种加减乘除或者其它的操作得到的值都是右值（rvalue），所以我们需要右值引用（rvalue reference）。添加下面的函数就好了：

```c++
int add(int&& a, int&& b){
    return a+b;
}
```

这样前面的例子就不会报错了。

# 完美转发（Perfect Forwarding）

从上面的例子可以看出，如果我们想避开pass-by-value的写法，我们就得多写一个右值引用，事实上，我们还需要再加一个const的引用，这样就是三个函数，假设我们有以下的三个函数：

```c++
void print(int& x){
    std::cout << "L refercence" << std::endl;
}

void print(int const& x){
    std::cout << "L const refercence" << std::endl;
}

void print(int&& x){
    std::cout << "R refercence" << std::endl;
}
```

（注意右值引用没有const的，因为大多数情况下右值都是即将被消灭掉的，所有没有const这一说。）

假设我们在编写模板函数的时候不巧需要调用print函数，而且我们的函数参数跟print函数的函数参数有关联，这时就稍微有些麻烦了，我们仍然需要三个函数：

```c++
void f(int& x){
    print(x);
}

void f(int const& x){
    print(x);
}

void f(int&& x){
    print(std::move(x));
}
```

特别注意一下第10行，我们使用了std::move，如果不使用这一语义的话，写成如下的形式的话：

```c++
void f(int&& x){
    print(x);
}
f(10);
```

实际上的过程是，先把10赋给一个名叫x的变量，然后在运行print(x)。是传左值。因为这里暗含一个赋值操作，因此实际上会影响性能，特别是如果我们是对一个对象进行操作的情况下。

这样接着写三个函数的话实在是太繁琐，能否使用模板方法让编译器自动判断我们的参数是什么类型？答案是可以，不过与普通类型的判断稍有不同。需要用到完美转发：

```c++
template<typename T>
void f(T&& x){
    print(std::forward<T>(x));
}
```

这样就可以了。

## enable_if<>

我们有时希望限制模板所适用的类型。譬如希望sizeof(T) > 4，我们可以这么写：

```c++
template<typename T>
std::enable_if_t<(sizeof(T)>4), T>
foo(){}
```

注意foo的返回类型是std::enable_if_t<(sizeof(T)>4), T>

如果我们希望我们之前的add函数，只能适用于int, int const, int&, int&&， 我们可以与std::is_convertible_t联用，写成如下的形式：

```c++
template<typename T>
std::enable_if_t<std::is_convertible_v<T, int>, T>
add(T&& a, T&& b){
    return (std::forward<T>(a) + std::forward<T>(b));
}
```

需要特别指出的是，这一节所叙述的内容经常在面向对象的过程中使用，可以提升性能。有关面向对象的例子需要再度查阅书籍。