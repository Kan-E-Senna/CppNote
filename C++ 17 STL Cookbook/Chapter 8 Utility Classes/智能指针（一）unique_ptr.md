# 智能指针（一）unique_ptr

在C++的内存管理中，一个很容易引起问题的麻烦在于，我们有可能有多个指针指向同一块区域。从而造成问题，比如free两次。看下面的代码：

```c++
class Foo{
private:
    std::string name;
public:
    Foo(std::string n):name{std::move(n)}{
        std::cout << "Constructor, name = " << name << std::endl;
    };
    ~Foo(){
        std::cout << "Destructor, name = " << name << std::endl;
    }
    const std::string& getname(){
        return name;
    }
};
```

我们上面有一个类，仅仅是在构造和析构的时候打印。假如我们在main函数中做了如下的事：

```c++
int main() {
    Foo *p1 = new Foo{std::string{"abcd"}};
    Foo *p2 = p1;
    delete p1;
    delete p2;
	std::cout << "All ok?" << std::endl;
    return 0;
}
```

我们在这里让p1和p2都指向同一块内存，然后进行delete。会产生如下的输出：

```shell
Constructor, name = abcd
processing, name = abcd
Destructor, name = abcd
Destructor, name =
Process finished with exit code -1073741819 (0xC0000005)
```

可以看到这里的程序发生了错误退出，并没有打印出最后的All ok。解决这个问题的最好的办法就是不要出现这种两个指针同时指向同一块内存的情况。可以使用unique_ptr，这里的unique就是独一，只有一个unqiue_ptr可以指向这块内存。unique_ptr的实现思路，就是在指针赋值的时候，必须使用移动语义，也就是如果赋值给新的指针，那么原指针就必须被销毁。我们写一些测试函数来观察一下：

```c++
void process_item(Foo* p){
    if (p == nullptr) return ;
    std::cout << "processing, name = " << p->getname() << std::endl;
}

void process_item(std::unique_ptr<Foo> p){
    if (p == nullptr) return ;
    std::cout << "processing, name = " << p->getname() << std::endl;
}
```

再写出main函数：

```c++
int main() {
    auto p1 {new Foo{"FooByNormal"}};
    auto p2 {std::make_unique<Foo>("FooBySmart")};

    process_item(p1);
    process_item(std::move(p2));
    process_item(std::make_unique<Foo>("FooBySmart2"));
    return 0;
}
```

首先注意第3行，unique_ptr的创建方式，也可以使用先声明在初始化/赋值的方式进行创建，但一般建议使用第二行的形式。

这里特别注意第7行，之所以要使用move，是因为unique_ptr是不允许直接赋值的，必须使用移动语义。不过这样的话，在运行完函数后，指针就被销毁了。如果不希望在函数运行完保留指针，需要再写引用和右值引用。（当然，也可以使用完美转发）

```c++
// 同时实现引用和右值引用
void process_item(std::unique_ptr<Foo>& p){
    if (p == nullptr) return ;
    std::cout << "processing, name = " << p->getname() << std::endl;
}

void process_item(std::unique_ptr<Foo>&& p){
    if (p == nullptr) return ;
    std::cout << "processing, name = " << p->getname() << std::endl;
}
```

```c++
// 使用完美转发
template<typename T>
void process_item(T&& p){
    if (p == nullptr) return ;
    std::cout << "processing, name = " << std::forward<T>(p)->getname() << std::endl;
}
```

最终运行出的结果：

```shell
Constructor, name = FooByNormal
Constructor, name = FooBySmart
processing, name = FooByNormal
processing, name = FooBySmart
Destructor, name = FooBySmart
Constructor, name = FooBySmart2
processing, name = FooBySmart2
Destructor, name = FooBySmart2
```

可以观察到：

1. FooByNormal没有被销毁
2. FooBySmart在FooBySmart2构建之前就被销毁了。