# Chapter 3 --- 非类型模板参数（Nontype Template Parameters）

## 3.1 类模板非类型模板参数

我们有时可能需要在类的外面定义与类有关的参数，譬如设计一个容器类但是限制其最大容量，这时便可以在模板参数中添加非类型模板参数。

```c++
template<typename T, int MaxSize>
class myArray{
private:
    T* data;
    int size;
public:
    myArray(int sz){
	    size = (sz >= MaxSize?MaxSize:sz);
        data = new T[size];
    }
    ~myArray(){
        delete[] data;
    }
};
```

## 3.2 函数模板非类型模板参数

譬如如下的函数：

```c++
template<int Val, typename T>
T addValue(T x){
    return x + Val;
}
```

这种定义函数的方式的优点在于，它分离了多        个参数，我们在使用STL库函数的时候，有时要求我们使用这种函数，譬如：

```c++
std::transform (source.begin(), source.end(), dest.begin, addValue<5, int>);
```

## 3.3 使用限制

不是所有的非类型（Nontype）都是允许的。

- 不允许是浮点数
- 不允许是某一具体的类

```c++
template<double V> // ERROR
double process (double x) {
    return V*x;
}

template<std::string name> // ERROR
class myClass{
    //...
};
```

有时我们需要使用字符串来作为模板参数，这时要注意：

> 不可以直接使用字符串字面量来传模板参数

```c++
template<char const * name>
void Message(){
    std::cout << name << std::endl;
}


int main() {
    Message<"hello">(); // ERROR
    return 0;
}
```

需要定义全局字符串变量，然后作为模板参数：

```c++
template<char const * name>
void Message(){
    std::cout << name << std::endl;
}

extern char const s1[] = "1234";
char const s2[] = "abcd";
static char const s3[] = "qwer";


int main() {
    Message<s1>(); // All version
    Message<s2>(); // Since C++11
    Message<s3>(); // Since C++17
    return 0;
}
```

## 3.4 注意事项

偶尔我们可能会把表达式作为模板参数

```c++
template<int I, bool B>
class C{
    // ...
};
```

需要注意的是，如果希望使用函数或者表达式，则它必须是一个编译期就能确定值的函数或者表达式。

```c++
int square(int x) {
    return x*x;
}

constexpr int cube(int x){
    return x*x*x;
}

template<int I, bool B>
void Message(){
    std::cout << I << " " << B << std::endl;
}

int main() {
    Message<square(5), sizeof(int) == 4>(); // ERROR
    Message<cube(5), sizeof(int) == 4>(); // OK
    return 0;
}
```

另外，使用表达式时，要特别小心”>“，有可能与左边模板的"<"匹配造成编译错误。

```c++
int main() {
    Message<cube(5), sizeof(int) > 3>(); // ERROR
    Message<cube(5), (sizeof(int) > 3)>(); // OK
    return 0;
}
```

