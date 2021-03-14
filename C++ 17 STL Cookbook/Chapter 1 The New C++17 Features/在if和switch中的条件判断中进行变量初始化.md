# 在if和switch中的条件判断中进行变量初始化

比如说，我们现在有个数组，然后我们希望查看这个数组中是否有某个元素，如果有，就进行X操作，一般来说我们会写出如下的代码：

```c++
std::vector<int> nums{1,2,3,4,5,6,7,0};
auto iter=std::find(nums.cbegin(), nums.cend(), 0);
if(iter!= nums.cend()){
    // do something
}
```

但是这种写法存在一个问题，iter这个变量在if结束之后仍然存在，而我们很可能不希望它继续存在，于是C++17允许我们把iter的声明放到if的条件语句里面，变成这样：

```c++
std::vector<int> nums{1,2,3,4,5,6,7,0};

if(auto iter=std::find(nums.cbegin(), nums.cend(), 0);iter!= nums.cend()){
    // do something
}
```

这样就很好地限制了iter的生命周期。

另外，switch也可以这样。

这里记载一个非常有趣的switch的语法：

```c++
int c;
switch (std::cin >> c;c){
    case 1 ... 9:std::cout << "Between 1 and 9" << std::endl;break;
    case 10 ... 19:std::cout << "Between 10 and 19" << std::endl;break;
    case 20 ... 29:std::cout << "Between 20 and 29" << std::endl;break;
    default:std::cout << "Nope!" << std::endl;break;
};
```

这里的 ‘...’ 符号很值得注意。