# 使用unordered_map

unordered_map与map的使用基本差别不大，但是map要求key必须是有序的，能够比较大小，有的时候可能不太方便使用。这时便可以考虑使用unordered_map， 它不要求能比较大小，只要求能够计算出hash值，以及能判断相等操作即可，比如我们有如下的struct

```c++
struct coord{
    int x;
    int y;
};
```

如果我们希望把这样一个struct作为key，就需要做两步，第一是重载==操作符：

```c++
bool operator==(const coord &l, const coord &r){
    return l.x == r.x && l.y == r.y;
}
```

第二是写一个hash函数，建议使用如下的模板

```c++
namespace std{
    template<>
    struct hash<coord>{
        using argument_type = coord;
        using result_type = size_t;
        result_type operator()(const argument_type &c)const{
            /* compute hash value in here */
            return static_cast<result_type>(c.x) + static_cast<result_type>(c.y);
            /* compute hash value end */
        }
    };
}
```

注意上面的代码中，仅有两行注释括起来的部分是自定义的，其余部分都是模板，不建议改动。

然后我们就可以把这个struct作为key来使用unordered_map了。

```c++
int main() {
    std::unordered_map<coord, int> M{{{0,0},0},
                                     {{0,1},1},
                                     {{1,0},1},
                                     {{1,1},2}};
    for(auto &[K,V]:M){
        std::cout << K.x << " " << K.y << " " << V << std::endl;
    }
    return 0;
}
```

```shell
1 1 2
1 0 1
0 1 1
0 0 0
```

