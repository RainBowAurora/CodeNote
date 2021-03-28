---
description: 面试高频指数：★★★★★
---

# 什么是多态？多态如何实现？

 **多态**： 指同一个实体同时具有多种形式。它是面向对象程序设计\(OOP\)的一个重要特征。如果一个语言只支持类而不支持_多态_,只能说明它是基于对象的,而不是面向对象的。c++中的多态分为两种**编译时多态**、**运行时多态**。

* **编译时多态（静态多态）**: 编译期多态，正如其名，就是在编译期确定的一种多态性。这个在C++中主要体现在函数模板。

```cpp
#include <iostream>

template<typename T>
T Add(const T& data1, const T& data2){
    return data1 + data2;
}

int main(int argc, char *argv[])
{
    std::cout << Add(1, 2) << std::endl; // 3
    
    std::cout << Add(3.14, 3.14) << std::endl; // 6.28
    
    return 0;
}
```

* **运行时多态（动态多态）**: 运行期多态主要是指在程序运行的时候，动态绑定所调用的函数，动态地找到了调用函数的入口地址，从而确定到底调用哪个函数。在C++中，运行期多态主要通过虚函数来实现。

```cpp
include <iostream>

class A{
public:
    virtual void Func() {std::cout << "A::Func()\n";}
    virtual ~A(){};
};

class B: public A{
    void Func() override {std::cout << "B::Func()\n";}
};

class C: public A{
    void Func() override {std::cout << "C::Func()\n";}
};

int main(int argc, char *argv[])
{
    A* ptr1 = new B();
    
    ptr1->Func(); // B::Func()
    
    delete ptr1;
    
    ptr1 = new C();
    
    ptr1->Func(); //C::Func()
    
    delete ptr1;
    
    return 0;
}
```

>

