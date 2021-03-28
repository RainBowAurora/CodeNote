---
description: 面试高频指数：★★★★★
---

# 虚函数内存布局

### 虚函数概念

1.  **虚函数**：被 `virtual` 关键字修饰的成员函数，就是虚函数。

* 纯虚函数在类中声明时，加上 =0； 含有纯虚函数的类称为抽象类（只要含有纯虚函数这个类就是抽象类），类中只有接口，没有具体的实现方法； 
* 继承纯虚函数的派生类，如果没有完全实现基类纯虚函数，依然是抽象类，不能实例化对象。 

  > * 抽象类对象不能作为函数的参数，不能创建对象，不能作为函数返回类型； 
  > * 可以声明抽象类指针，可以声明抽象类的引用； 
  > * 子类必须继承父类的纯虚函数，并全部实现后，才能创建子类的对象。

#### 虚函数的实现机制

虚函数通过虚函数表来实现。虚函数的地址保存在**虚函数表**中，在类的对象所在的内存空间中，保存了指向虚函数表的指针（称为“虚表指针”），通过虚表指针可以找到类对应的虚函数表。虚函数表解决了基类和派生类的继承问题和类中成员函数的覆盖问题，当用基类的指针来操作一个派生类的时候，这张虚函数表就指明了实际应该调用的函数: 

* 虚函数表存放的内容：类的虚函数的地址。 
* 虚函数表建立的时间：编译阶段，即程序的编译过程中会将虚函数的地址放在虚函数表中。 
* 虚表指针保存的位置：虚表指针存放在对象的内存空间中最前面的位置，这是为了保证正确取到虚函数的偏移量。

{% hint style="info" %}
虚函数表和类绑定，虚表指针和对象绑定。即类的不同的对象的虚函数表是一样的，但是每个对象都有自己的虚表指针，来指向类的虚函数表。
{% endhint %}

### 虚函数的内存布局

* 编译器将虚函数表的指针放在类的实例对象的内存空间中，该对象调用该类的虚函数时，通过指针找到虚函数表，根据虚函数表中存放的虚函数的地址找到对应的虚函数。 
* 如果派生类没有重新定义基类的虚函数 A，则派生类的虚函数表中保存的是基类的虚函数 A 的地址，也就是说基类和派生类的虚函数 A 的地址是一样的。 
* 如果派生类重写了基类的某个虚函数 B，则派生的虚函数表中保存的是重写后的虚函数 B 的地址，也就是说虚函数 B 有两个版本，分别存放在基类和派生类的虚函数表中。 
* 如果派生类重新定义了新的虚函数 C，派生类的虚函数表保存新的虚函数 C 的地址。

> `g++ -fdump-class-hierarchy xxx.cpp //内存布局查看命令`

#### **一般继承（无虚函数覆盖）**

* 虚函数按照其声明顺序放于表中。
* 父类的虚函数在子类的虚函数前面。

**代码示例:**

```cpp
#include <iostream>
#include <stdio.h>

typedef void(*func)();

class Base{
public:
    int base_data;
    virtual void func1() { std::cout << "Base::func1()\n"; };
    virtual void func2() { std::cout << "Base::func2()\n"; };
    virtual void func3() { std::cout << "Base::func3()\n"; };
    virtual ~Base() {};
};

class Derive: public Base{
public:
    int derive_data;
    virtual void func4() { std::cout << "Derive::func4()\n"; };
    virtual void func5() { std::cout << "Derive::func5()\n"; };
    virtual void func6() { std::cout << "Derive::func6()\n"; };
    virtual ~Derive() {};
};


int main(int argc, char *argv[])
{
    Base base;
    std::cout << "&base: " << &base << std::endl;
    std::cout << "&base.base_data: " << &base.base_data << std::endl;
    std::cout << "----------------------------------------" << std::endl;

    Derive derive;
    std::cout << "&derive: " << &derive << std::endl;
    std::cout << "&derive.base_data: " << &derive.base_data << std::endl;
    std::cout << "&derive.derive_data: " << &derive.derive_data << std::endl;
        // &base : base首地址
        // (unsigned long*)&base : base的首地址，vptr的地址
        // (*(unsigned long*)&base) : vptr的内容，即vtable的地址，指向第一个虚函数的slot的地址
        // (unsigned long*)(*(unsigned long*)&base) : vtable的地址，指向第一个虚函数的slot的地址
        // vtbl : 指向虚函数slot的地址
        // *vtbl : 虚函数的地址
    for(int i=0; i<3; i++)
    {
        unsigned long* vtbl = (unsigned long*)(*(unsigned long*)&base) + i;
        func pfunc = (func)*(vtbl);
        pfunc();
        std::cout << "slot address: " << vtbl << std::endl;
        std::cout << "func address: " << *vtbl << std::endl;
    }
    std::cout << "----------------------------------------" << std::endl;

    for(int i=0; i<3; i++)
    {
        unsigned long* vtbl = (unsigned long*)(*(unsigned long*)&derive) + i;
        func pfunc = (func)*(vtbl);
        pfunc();
        std::cout << "slot address: " << vtbl << std::endl;
        std::cout << "func address: " << *vtbl << std::endl;
    }
    std::cout << "----------------------------------------" << std::endl;

    return 0;
}

```

![&#x56FE;1. &#x4E00;&#x822C;&#x7EE7;&#x627F;,&#x65E0;&#x865A;&#x51FD;&#x6570;&#x8986;&#x76D6;](../.gitbook/assets/image%20%2810%29.png)

#### **一般继承（有虚函数覆盖）**

* 覆盖的函数被放到了虚表中原来父类虚函数的位置。
* 没有被覆盖的函数依旧在原来的位置。

代码示例：

```cpp
#include <iostream>
#include <stdio.h>

typedef void(*func)();

class Base{
public:
    int base_data;
    virtual void func1() { std::cout << "Base::func1()\n"; };
    virtual void func2() { std::cout << "Base::func2()\n"; };
    virtual void func3() { std::cout << "Base::func3()\n"; };
    virtual ~Base() {};
};

class Derive: public Base{
public:
    int derive_data;
    virtual void func2() { std::cout << "Derive::func2()\n"; };
    virtual void func1() { std::cout << "Derive::func1()\n"; };
    virtual ~Derive() {};
};
int main(int argc, char *argv[])
{
    Base base;
    std::cout << "&base: " << &base << std::endl;
    std::cout << "&base.base_data: " << &base.base_data << std::endl;
    std::cout << "----------------------------------------" << std::endl;

    Derive derive;
    std::cout << "&derive: " << &derive << std::endl;
    std::cout << "&derive.base_data: " << &derive.base_data << std::endl;
    std::cout << "&derive.derive_data: " << &derive.derive_data << std::endl;
    // &base : base首地址
    // (unsigned long*)&base : base的首地址，vptr的地址
    // (*(unsigned long*)&base) : vptr的内容，即vtable的地址，指向第一个虚函数的slot的地址
    // (unsigned long*)(*(unsigned long*)&base) : vtable的地址，指向第一个虚函数的slot的地址
    // vtbl : 指向虚函数slot的地址
    // *vtbl : 虚函数的地址
    for(int i=0; i<3; i++)
    {
        unsigned long* vtbl = (unsigned long*)(*(unsigned long*)&base) + i;
        func pfunc = (func)*(vtbl);
        pfunc();
        std::cout << "slot address: " << vtbl << std::endl;
        std::cout << "func address: " << *vtbl << std::endl;
    }
    std::cout << "----------------------------------------" << std::endl;
    
    for(int i=0; i<3; i++)
    {
        unsigned long* vtbl = (unsigned long*)(*(unsigned long*)&derive) + i;
        func pfunc = (func)*(vtbl);
        pfunc();
        std::cout << "slot address: " << vtbl << std::endl;
        std::cout << "func address: " << *vtbl << std::endl;
    }
    std::cout << "----------------------------------------" << std::endl;

    return 0;
}

```

内存布局: 

![&#x56FE;2. &#x4E00;&#x822C;&#x7EE7;&#x627F;, &#x6709;&#x865A;&#x51FD;&#x6570;&#x8986;&#x76D6;](../.gitbook/assets/image%20%2812%29.png)

#### 虚拟继承

代码示例:

```cpp
class Base{
public:
    int base_data;
    virtual void func1() { std::cout << "Base::func1()\n"; };
    virtual void func2() { std::cout << "Base::func2()\n"; };
    virtual void func3() { std::cout << "Base::func3()\n"; };
    virtual ~Base() {};
};

class Derive:  virtual public Base{
public:
    int derive_data;
    virtual void func1() { std::cout << "Derive::func1()\n"; };
    virtual void func2() { std::cout << "Derive::func2()\n"; };
    virtual ~Derive() {};
};
```

![](../.gitbook/assets/image%20%2814%29.png)



> Reference: 
>
> 1. [虚继承的Vtable](https://www.dazhuanlan.com/2020/02/11/5e4243b125717/?__cf_chl_jschl_tk__=b732157418841d9a21d1bbcfa35763a18e1ddeed-1616666621-0-ARP7E5jK_-ozYWjdGrkxGs59eKkYZXDh8VRZ4pZx0ydR7EJeBidi_oUe4_dY4JUKJFkvkrELCyktM5pWCueN_enMYelT6xU2G09lw7B6vQcueamtCXlRPc8Ecnd_C5-taQqPn1wHcfdd7PcrnxkvSCnBDBRIyiCQM_wzmuYusNf9dCNu5S2qK2vZDKuqUneB0dUDHPxCfv32RDbEBcV0QIiqiL6dTLsSfthOuB8RJuynA4_5sWpZUmCnTCycfR3peLggI9L2BVqfalEZxxE0n-isnBWZuEzRS6mHbgBi6KT3YJccg4o5XSsg0sQpWgeZQ8GQBwumBhs532YNvgsOftx4YTliUHCem0Skip50JnMS)
> 2. [知乎-C++为什么要弄出虚表这么个东西](https://www.zhihu.com/question/389546003/answer/1194780618)

