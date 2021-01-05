# Chapter 8 --- 编译期编程

先出一个题目，判断一个数是否是素数，要求必须是编译期求结果。

可以有两种思路：

## 利用constexpr

首先想到的就是用constexpr：

```c++
constexpr bool doIsPrime(unsigned p, unsigned d){
    return d!=2 ? (p%d != 0) && doIsPrime(p, d-1) 
                : (p%2!=0);
}

constexpr bool IsPrime(unsigned p){
    return p<4 ? !(p<2) 
               : doIsPrime(p, p/2);
}
```

## 利用模板

```c++
//递归主体
template<unsigned p, unsigned d>
struct DoIsPrime {
    static constexpr bool value = (p%d != 0) && DoIsPrime<p, d-1>::value;
};

// 递归结束点
template<unsigned p>
struct DoIsPrime<p, 2>{
    static constexpr bool value = (p%2 != 0);
};

// 求值入口
template<unsigned p>
struct IsPrime{
    static constexpr bool value = DoIsPrime<p, p/2>::value;
};

// 特殊情形
template<>
struct IsPrime<0> {static constexpr bool value = false;};
template<>
struct IsPrime<1> {static constexpr bool value = false;};
template<>
struct IsPrime<2> {static constexpr bool value = true;};
template<>
struct IsPrime<3> {static constexpr bool value = true;};
```

注意在value前面需要加static constexpr标明需要编译期求值。

以IsPrime(9)为例，展示其运算过程：

9%4 != 0 && DoIsPrime(9, 3)::value

then expands to

9%4 != 0 && 9%3 !=0 && DoIsPrime(9, 2)::value

then expands to

9%4 != 0 && 9%3 !=0 && 9%2 !=0

结果为false。

## 模板分支

```c++
template<int SZ, bool = isPrime(SZ)>
struct OP{
    ...
};

template<int SZ>
struct OP<SZ, false>{
    ...
};

template<int SZ>
struct OP<SZ, true>{
    ...
};
```

