---
description: 面试高频指数：★★★★★
---

# 重载、重写、隐藏

* **重载**（overload）：即c++允许同一个类中同名不同参数类型、数量\)的函数同时存在。

```cpp
#include <iostream>

void Func(int a){ std::cout << "FUNC1\n"; }

void Func(double b) { std::cout << "FUNC2\n"; }


int main(int argc, char *argv[])
{
    Func(1); // FUNC1
    Func(1.0); // FUNC2
    return 0;
}
```

* **重写**（override）：子类改写父类声明的虚方法\(virtual\)。

```cpp
#include <iostream>

class A{
public:
    virutal void Func() { std::cout << "A::Func\n"; }
    virtual ~A(){};
};

class B: public A{
public:
    void Func() override { std::cout << "B::Func\n"; }
    ~B(){};
};

int main(int argc, char *argv[])
{
    A* ptr = new B();
    
    ptr->Func(); // B::Func
    
    return 0;
}
```

* **隐藏**：是指派生类的函数屏蔽了与其同名的基类函数，主要只要同名函数，不管参数列表是否相同，基类函数都会被隐藏。

```cpp
#include <iostream>

class A{
public:
    void Func(int a, double b) { std::cout << "A::Func\n"; }
    virtual ~A(){};
};

class B: public A{
public:
    void Func(int a) { std::cout << "B::Func\n"; }
    ~B(){};
};

int main(int argc, char *argv[])
{
    B ex;
    
    ex.Func(1); 
    //ex.Func(1, 2.0); //error: candidate expects 1 argument, 2 provided
    
    return 0;
}
```

### 重写和重载的区别

* 范围区别：对于类中函数的重载或者重写而言，重载发生在同一个类的内部，重写发生在不同的类之间（子类和父类之间）。
* 参数区别：重载的函数需要与原函数有相同的函数名、不同的参数列表，不关注函数的返回值类型；重写的函数的函数名、参数列表和返回值类型都需要和原函数相同，父类中被重写的函数需要有 `virtual`修饰。
* `virtual` 关键字：重写的函数基类中必须有 `virtual`关键字的修饰，重载的函数可以有 `virtual` 关键字的修饰也可以没有。

