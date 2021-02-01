# std中的排序

## sort

```c++
std::vector<int> v{3,1,4,2,5,7,6};

std::sort(v.begin(), v.end(), std::less<>());
// std::less<>() 可以省略。如果希望降序排序，则需要使用std::greater<>()
for(auto x:v) std::cout << x << " "; // 1 2 3 4 5 6 7
std::cout << std::endl;
```

## stable_sort

这个是用来保证，两个具有相同比较值的对象，排序后的位次与排序前的位次一致。

```c++
std::vector<Task> tasks{{10, "Task 1"s},
                        {40, "Task 2"s},
                        {25, "Task 3"s},
                        {10, "Task 4"s},
                        {80, "Task 5"s},
                        {10, "Task 6"s}};

std::stable_sort(tasks.begin(), tasks.end());
for(auto t:tasks){
    std::cout << '{' << t.priority << ',' << t.name << '}' << std::endl;
}
```

```shell
{10,Task 1}
{10,Task 4}
{10,Task 6}
{25,Task 3}
{40,Task 2}
{80,Task 5}
```

## partial_sort

用于对序列的一部分进行排序。

```c++
std::vector<int> v{3,1,4,2,5,7,6};

std::partial_sort(v.begin(), v.begin()+4, v.end()); 
for(auto x:v) std::cout << x << " "; // 1 2 3 4 5 7 6
std::cout << std::endl;
```

## nth_element

这一函数用来把原序列变成这样一个序列：0-N-1所有的元素都比第N个元素小，N+1-末尾所有的元素都比第N个元素大。

```c++
std::vector<int> v{3,1,4,2,5,7,6};

std::nth_element(v.begin(), v.begin()+3, v.end());
for(auto x:v) std::cout << x << " "; // 2 1 3 4 5 7 6
std::cout << std::endl;
```

这里指定N是3, 得到的序列，第三个是4，前三个元素2 1 3都比4小，后三个元素5 7 6都比4大。

## is_sorted

is_sorted用来判定序列是否被排序。

```c++
std::vector<int> v{3,1,4,2,5,7,6};
std::cout << std::is_sorted(v.cbegin(), v.cend(), std::greater<>()) << std::endl; // 0

std::sort(v.begin(), v.end(), std::greater<>());
std::cout << std::is_sorted(v.cbegin(), v.cend(), std::greater<>()) << std::endl; // 1
```

