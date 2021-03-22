# 智能指针（三）weak_ptr

shared_ptr的问题在于，如果我们有很多个shared_ptr都指向这个对象，那么我们可能很难及时地销毁对象回收内存，因为引用计数一直不为0，weak_ptr可以帮助我们解决这个问题，它可以被shared_ptr赋值，但是不增加shared_ptr的引用计数，我们可以使用一个share_ptr，然后让多个weak_ptr指向这个对象，这样的话只要这个share_ptr被销毁，内存就被回收了，weak_ptr本身是不会用delete的。

还是使用先前的代码：

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

使用如下的main函数：

```c++
int main() {
    std::weak_ptr<Foo> wp;
    {
        auto p {std::make_shared<Foo>("FooBySharePtr1")};
        wp = p;
        std::cout << "Weak pointer --- "
                  <<"expired = "     << wp.expired()
                  << " use_count = " << wp.use_count()
                  << std::endl;
    }

    std::cout << "Weak pointer --- "
              <<"expired = "     << wp.expired()
              << " use_count = " << wp.use_count()
              << std::endl;
    return 0;
}
```

第5行，我们创建了一个对象，并使用share_ptr指向这个对象。第六行，把这个shared_ptr赋给weak_ptr，再输出weak_ptr的信息。输出如下：

```shell
Constructor, name = FooBySharePtr1
Weak pointer --- expired = 0 use_count = 1
Destructor, name = FooBySharePtr1
Weak pointer --- expired = 1 use_count = 0
```

可以看到第二行的输出，use_count=1，说明的是它只被一个shared_ptr所指向。