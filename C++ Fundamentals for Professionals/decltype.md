# decltype

我们经常使用auto来进行自动类型推导，但是auto的缺点是，我们可能没法控制推导出的类型。譬如说我们希望希望推导出const或者引用类型，或者希望推导出的类型与另一个我们已经掌握的变量一致（这在模板编程中非常常见）。这时我们就不能用auto，可以使用decltype。

## 语法

- decltype(variable)
- decltype(expression)

## decltype(variable)

如果decltype里面是一个变量，那么类型就与这个变量相关。

```c++
#include <iostream>
#include <vector>

int func(int, int){ return 0; }

int main(){

  decltype(5) i = 5;                            // int
    
  int& intRef = i;	                             // int&
  decltype(intRef) intRefD = intRef;            // int&

  int* intPoint = &i;                           // int*
  decltype(intPoint) intPointD = intPoint;      // int*

  const int constInt = i;                       // const int
  decltype(constInt) constIntD = constInt;      // const int

  static int  staticInt = 10;                   // static int
  decltype(staticInt) staticIntD = staticInt;   // static int

  const std::vector<int> myVec;
  decltype(myVec) vecD = myVec;                 // const std::vector<int>

  auto myFunc = func;                           // (int)(*)(int, int)
  decltype(myFunc) myFuncD = myFunc;            // (int)(*)(int, int)

  // define a function pointer
  int (*myAdd1)(int, int) = [](int a, int b){ return a + b; };

  // use type inference of the C++11 compiler
  decltype(myAdd1) myAdd2 = [](int a, int b){ return a + b; };
}
```

## decltype(expression)

如果decltype里面是一个表达式，那么类型与表达式的值类型有关，如果表达式结果是一个左值，那么类型就推导为引用类型，如果是右值，则推导为右值的值类型。

```c++
#include <iostream>

int main() {
  int i = 1998; // Rvalue
  decltype(i) i2 = 2011; // Same as int i2 = 2011
  
  decltype((i)) iRef = i2; // (i) is an lvalue, reference returned
  std::cout << "iRef: " << iRef << std::endl;
  std::cout << "i2: " << i2 << std::endl;

  std::cout << std::endl;
  
  iRef = 2012;
  std::cout << "iRef: " << iRef << std::endl;
  std::cout << "i2: " << i2 << std::endl; // iRef is a reference after all
}
```

注意第7行的代码中(i) 加了括号，他是一个左值，所以返回引用类型。