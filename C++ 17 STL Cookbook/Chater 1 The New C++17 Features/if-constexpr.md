# if-constexpr

我们在写模板类的时候，经常需要考虑不同模板参数类型可能有不同的效果。

例如，如果我们想实现一个addable的类，其作用就是相加：

```c++
template<typename T>
class addable{
    T val;
public:
    addable(T v):val{v}{}

    template<typename U>
    T add(U x)const {
        return x+val;
    }
};
```

然后在main函数中调用这个模板类：

```c++
int main(){
    addable t{10};
    auto r = t.add(15);
    std::cout << r << std::endl; // 25
}
```

但是很显然，假如T是一个int，而U是一个vector，这种写法自然会报错。但我们很可能希望使U是一个vector，作用是让val加上vector的总和。这时我们便可以使用if constexpr语法（当然，也可以使用enable_if语法，只是本节的重点在if constexpr）。

```c++
template<typename T>
class addable{
    T val;
public:
    addable(T v):val{v}{}

    template<typename U>
    T add(U x) {
        if constexpr (std::is_same_v<U, std::vector<T>>){ // 这一句的意思是说，如果U是一个vector<T>的话。
            return val + std::accumulate(x.begin(), x.end(), 0);
        }else{
            return val + x; // 注意，不可以把这一句提到else 外面
        }
    }
};
```

在main函数中调用这个模板类：

```c++
int main(){
    addable t{10};
    std::vector<int> nums{1,2,3,4,5};
    auto r1 = t.add(10);
    std::cout << r1 << std::endl; // 20
    auto r2 = t.add(nums);
    std::cout << r2 << std::endl; // 25
}
```

当然，除此之外也有enable_if的写法

```c++
template<typename T>
class addable{
    T val;
public:
    addable(T v):val{v}{}

    template<typename U>
    std::enable_if_t<std::is_same_v<std::vector<T>, U>, T>
    add(U x) {
        return val + std::accumulate(x.begin(), x.end(), 0);
    }

    template<typename U>
    std::enable_if_t<!std::is_same_v<std::vector<T>, U>, T>
    add(U x) {
        return val + x;
    }
};
```

效果是相同的。