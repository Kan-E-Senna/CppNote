# 智能指针（二）shared_ptr

内存泄漏的第二个问题是可能出现一片内存没有指针指向的情况，假如我们有如下的类（与智能指针（一）相同）：

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

然后我们有如下的main函数：

```c++
int main() {
    Foo *p1 = new Foo{"FooByNormal1"};
    Foo *p2 = new Foo{"FooByNormal2"};
    p2 = p1;
    delete p1;
    new Foo{"FooByNormal3"};
    return 0;
}
```

这个时候FooByNormal2和FooByNormal3所代表的内存空间是没有指针指向的，这会导致内存泄漏问题。解决这一问题的方法除了使用uniqu_ptr外（有时我们可能确实希望有多个指针指向同一片内存空间），还可以使用shared_ptr。share_ptr的思路是引用计数，当一片内存没有被指向的时候，就回收内存。

```c++
int main() {
    std::shared_ptr<Foo>{new Foo{"FooBySharePtr1"}};

    auto p1{ std::make_shared<Foo>("FooBySharePtr2")};
    std::cout << "use_count = " << p1.use_count() << std::endl;
    std::shared_ptr<Foo> p2 = p1;
    std::cout << "use_count = " << p1.use_count() << std::endl;
    p1 = nullptr;
    p2 = nullptr;
    std::cout << "All done" << std::endl;

    return 0;
}
```

注意第2行，使用std::shared_ptr构造了一个指针后，会因为没有指针指向它而直接被回收。

第4行，使用make_shared构造一个智能指针。

第5行和第7行，使用use_count来返回引用计数。

第8,9行，引用计数为0，引发FooBySharePtr2的析构。

最终结果：

```shell
Constructor, name = FooBySharePtr1
Destructor, name = FooBySharePtr1
Constructor, name = FooBySharePtr2
use_count = 1
use_count = 2
Destructor, name = FooBySharePtr2
All done
```



