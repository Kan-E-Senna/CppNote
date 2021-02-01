# std中的查找

要使用标准库中提供的查找功能，需要首先引入algorithm头文件。

```c++
#include <algorithm>
```

## find

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,7,0};

auto  iter = std::find(v.cbegin(), v.cend(), 3);
std::cout << *iter << std::endl; // 3
std::cout << std::distance(v.cbegin(), iter) << std::endl; // 4
```

注意find的参数和返回都是迭代器。如果我们希望返回索引，可以利用std::distance。另外，这里使用了cbegin()和cend()。c代表const，也就是说明我们不希望这个迭代器所指向的值被修改。

## find_if

如果我们希望查找的值是符合某种条件的，就可以使用find_if。

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,7,0};

auto  iter = std::find_if(v.cbegin(), v.cend(), [](int const p){ return p%2 == 0;});
std::cout << *iter << std::endl; // 2
std::cout << std::distance(v.cbegin(), iter) << std::endl; // 2
```

## find_if_not

与find_if对应，查找的是不满足给定条件的。

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,7,0};

auto  iter = std::find_if_not(v.cbegin(), v.cend(), [](int const p){ return p%2 == 0;});
std::cout << *iter << std::endl; // 1
std::cout << std::distance(v.cbegin(), iter) << std::endl; // 0
```

## find_first_of

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,7,0};
std::vector<int> w{16,7, 0};

auto  iter = std::find_first_of(v.cbegin(), v.cend(), w.cbegin(), w.cend());

std::cout << *iter << std::endl; // 7
std::cout << std::distance(v.cbegin(), iter) << std::endl; // 9
```

这个函数的意思是，它会首先查找16在v中的位置，它会发现v中没有16，于是会接着查找7，然后返回，不再接着查找0。

## search

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,7,4,5,6};
std::vector<int> w{4,5,6};

auto  iter = std::search(v.cbegin(), v.cend(), w.cbegin(), w.cend());
if (iter != v.cend()) std::cout << std::distance(v.cbegin(), iter) << std::endl; // 10
```

find_end用于查找子序列（这里是w）第一次出现在原序列v中的位置。更常见的用法是用于子字符串的查找。

## find_end

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,7,4,5,6};
std::vector<int> w{4,5,6};

auto  iter = std::find_end(v.cbegin(), v.cend(), w.cbegin(), w.cend());
if (iter != v.cend()) std::cout << std::distance(v.cbegin(), iter) << std::endl; // 10
```

find_end用于查找子序列（这里是w）最后一次在原序列v中的位置。更常见的用法是用于子字符串的查找。

## search_n

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,7,4,5,6};

auto  iter = std::search_n(v.cbegin(), v.cend(),2, 3);

std::cout << *iter << std::endl; // 3
std::cout << std::distance(v.cbegin(), iter) << std::endl; // 4
```

search_n用于查找连续的n个相同值，这里是要查找是否有连续的2个3.

## adjacent_find

adjacent_find 用于查找是否存在连续的两个值满足给定条件。

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,8,4,5,6};

auto  iter = std::adjacent_find(v.cbegin(), v.cend(), 
                                [](int const a, int const b){return (a%2 == 0 && b%2 == 0);});

std::cout << *iter << std::endl; // 2
std::cout << std::distance(v.cbegin(), iter) << std::endl; // 2
```

最后的pred也可以不写，默认为判定两个值相等。

## binary_search

就是二分查找，但是需要注意的是，第一，必须是排序好的序列。第二，这个函数只返回bool，也就是说只能判定是否存在这样一个值，不能得出它的位置信息。

```c++
std::vector<int> v{1,1,2,2,3,3,4,5,6,8,};

auto success = std::binary_search(v.cbegin(), v.cend(), 4);

std::cout << success << std::endl; // 1
```

## lower_bound

lower_bound用来从不小于给定值的对象中，寻找最小值，例如：

```c++
std::vector<int> v{1,2,3,4,5,6,7};
std::vector<int> w{4,5,6};

auto  iter = std::lower_bound(v.cbegin(), v.cend(), 4);

std::cout << *iter << std::endl; // 4 
std::cout << std::distance(v.cbegin(), iter) << std::endl; // 3
```

这里不小于4大的数有{4,5,6,7}，这里面最小值为4，所以返回4。

## upper_bound

upper_bound用来从严格大于给定值的对象中，寻找最小值，例如：

```c++
std::vector<int> v{1,2,3,4,5,6,7};
std::vector<int> w{4,5,6};

auto  iter = std::upper_bound(v.cbegin(), v.cend(), 4);

std::cout << *iter << std::endl; // 5
std::cout << std::distance(v.cbegin(), iter) << std::endl; // 4
```

这里大于4大的数有{5,6,7}，这里面最小值为5，所以返回5。

**需要注意lower_bound和upper_bound事实上意思是相近的，很容易造成误解。**

