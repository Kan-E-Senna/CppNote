# chrono库

有时我们需要记录时间，此时便可以使用chrono库。

```c++
#include <chrono>
```

另外，chrono提供了字面量，这使得我们可以直接使用类似“30s”这样的字面量来表示30秒。c++20还提供了y(year)，d(day)这样的字面量，不过写作笔记时，我自己的编译器还未支持c++20，故不多介绍。

特别提醒：这里提到的时间是指时间片（duration）：

## 直接声明时间：

```c++
std::chrono::hours        half_day{12};
std::chrono::minutes      half_hour{30};
std::chrono::seconds      half_minute{30};
std::chrono::milliseconds half_second{500};
std::chrono::microseconds half_mill{500};
std::chrono::nanoseconds  half_micro{500};
```

这样的写法可能略显麻烦，所以我们经常使用字面量来声明时间片：

## 利用字面量

注意，需要首先引入chrono字面量：

```c++
using namespace std::chrono_literals;
```

然后就可以使用了：

```c++
auto half_day = 12h;
auto half_hour = 30min;
auto half_minute = 30s;
auto half_second = 500ms;
auto half_mill = 500us;
auto half_micor = 500ns;
```

## 打印输出

特别需要提醒的是，不能直接使用std::cout << half_day << std::endl，这样来直接输出，必须使用.count()来获取其值。

```c++
std::cout << half_day.count() << 'h' << std::endl;
// 12h
```

## 转换

譬如我们希望知道120分钟是几小时，我们就需要进行转换，使用std::chrono::duration_cast函数即可。

```c++
auto total_sec = 120min;
auto total_hours = std::chrono::duration_cast<std::chrono::hours>(total_sec);
std::cout << total_hours.count() << 'h' << std::endl;
// 2h
```

要注意的是时间片数据都是整数，因此在转换的时候就会遇到精度问题，譬如：

```c++
auto total_sec = 119min;
auto total_hours = std::chrono::duration_cast<std::chrono::hours>(total_sec);
std::cout << total_hours.count() << 'h' << std::endl;
// 1h
```

这是因为duration_cast是直接截断式地转换，如果希望有其它转换方式，我们还有其它函数：floor 向下取整，ceil向上取整，round四舍五入：

### floor

```c++
auto total_sec = 119min;
auto total_hours = std::chrono::floor<std::chrono::hours>(total_sec);
std::cout << total_hours.count() << 'h' << std::endl;
// 1h
```

### ceil

```c++
auto total_sec = 119min;
auto total_hours = std::chrono::ceil<std::chrono::hours>(total_sec);
std::cout << total_hours.count() << 'h' << std::endl;
// 2h
```

### round

```c++
auto total_sec = 119min;
auto total_hours = std::chrono::round<std::chrono::hours>(total_sec);
std::cout << total_hours.count() << 'h' << std::endl;
// 2h
```

## 运算

时间片可以进行运算：

```c++
auto total_sec = 1h+120min;
auto total_hours = std::chrono::duration_cast<std::chrono::hours>(total_sec);
std::cout << total_hours.count() << 'h' << std::endl;
// 3h
```

