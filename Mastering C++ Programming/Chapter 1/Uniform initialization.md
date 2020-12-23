# Uniform initialization

除了常见的赋值初始化，C++11之后建议使用uniform initilization，（中文翻译为标准初始化，或者一致性初始化？暂时不清楚），也就是使用花括号进行初始化。

示例：

```c++
std::string s1{"test"}
// it equuals to
std::string s2 = "test";
```

## 几个常见的初始化例子：

```c++
std::vector<int> v{1,2,3};
std::map<int, std::string> m{{1, "one"}, {2, "two"}};

int arr1[3]{1,2,3};
int* arr2 = new int[3]{1,2,3};

int i{42};
double d{1.2};

// user-defined struct
struct Point{
    int x;
    double y;
}
Point example{x, y};

//user-defined Class
class complex{
    private:
    int re;
    int im;
    public:
    complex():re{0}, im{0}{};
    complex(int r, int ima = 0):re{r}, im{ima}{};
}
complex example2{};
complex example3{5};
complex example4{17, 28};

```

## 花括号初始化与括号初始化的差别

在使用vector的时候，我们可以看到使用花括号和括号是有一定的差别的。

```c++
std::vector<int> vect1(5); // vect1 has 5 elements
std::vector<int> vect2{5}; // vect2 has 1 element and its value is 5;
```

出现这种情况的原因在于vector的构造函数可以接受一个initializer_list作为参数。可以看下面的例子：

```c++
void print(const char* st){
    std::cout << st << std::endl;
}

class foo{
private:
    int x;
public:
    foo():x(0){
        std::cout << "default" << std::endl;
    }
    foo(int a):x{a}{
        std::cout << "one argument" << std::endl;
    }
    foo(std::initializer_list<int> l){
        std::cout << "initializer_list" << std::endl;
    }
};

int main() {
    foo example1(5);
    foo example2{10};
    return 0;
}
```

以下是我们的输出：

```shell
one argument
initializer_list
```

可以看出，括号初始化会调用foo(int)构造函数，而花括号初始化调用的是foo(std::initializer_list\<int\>）。如果把这个构造函数删去，那么也会调用foo(int)。

## 注意事项

花括号初始化的好处在于它的类型检查更为严格。（但不是完全限制类型转换）譬如：

```c++
int x = 1.4; // OK, x = 1
int y{1.4};  // Error
```

