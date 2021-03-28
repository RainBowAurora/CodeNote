---
description: 面试高频指数：★★★★☆
---

# 强制类型转换

### static\_cast

用于数据的强制类型转换，强制将一种数据类型转换为另一种数据类型。

* 用于基本数据类型的转换。 
* 用于类层次之间的基类和派生类之间 指针或者引用 的转换（不要求必须包含虚函数，但必须是有相互联系的类），进行上行转换（派生类的指针或引用转换成基类表示）是安全的；进行下行转换（基类的指针或引用转换成派生类表示）由于没有动态类型检查，所以是不安全的，最好用 `dynamic_cast` 进行下行转换。 
* 可以将空指针转化成目标类型的空指针。 
* 可以将任何类型的表达式转化成 `void` 类型。 

### const\_cast

强制去掉常量属性，不能用于去掉变量的常量性，只能用于去除指针或引用的常量性，将常量指针转化为非常量指针或者将常量引用转化为非常量引用（注意：表达式的类型和要转化的类型是相同的）。

### reinterpret\_cast

改变指针或引用的类型、将指针或引用转换为一个足够长度的整型、将整型转化为指针或引用类型。

### dynamic\_cast

* 其他三种都是编译时完成的，动态类型转换是在程序运行时处理的，运行时会进行类型检查。
*  只能用于带有虚函数的基类或派生类的指针或者引用对象的转换，转换成功返回指向类型的指针或引用，转换失败返回 `NULL`；不能用于基本数据类型的转换。 
* 在向上进行转换时，即派生类类的指针转换成基类类的指针和 `static_cast` 效果是一样的，（注意：这里只是改变了指针的类型，指针指向的对象的类型并未发生改变）。

```cpp
#include <iostream>
#include <cstring>

using namespace std;

class Base
{
};

class Derive : public Base
{
};

int main()
{
    Base *p1 = new Derive();
    Derive *p2 = new Derive();

    //向上类型转换
    p1 = dynamic_cast<Base *>(p2);
    if (p1 == NULL)
    {
        cout << "NULL" << endl;
    }
    else
    {
        cout << "NOT NULL" << endl; //输出
    }

    return 0;
}
```

* 在下行转换时，基类的指针类型转化为派生类类的指针类型，只有当要转换的指针指向的对象类型和转化以后的对象类型相同时，才会转化成功。

```cpp
#include <iostream>
#include <cstring>

using namespace std;

class Base
{
public:
    virtual void fun()
    {
        cout << "Base::fun()" << endl;
    }
};

class Derive : public Base
{
public:
    virtual void fun()
    {
        cout << "Derive::fun()" << endl;
    }
};

int main()
{
    Base *p1 = new Derive();
    Base *p2 = new Base();
    Derive *p3 = new Derive();

    //转换成功
    p3 = dynamic_cast<Derive *>(p1);
    if (p3 == NULL)
    {
        cout << "NULL" << endl;
    }
    else
    {
        cout << "NOT NULL" << endl; // 输出
    }

    //转换失败
    p3 = dynamic_cast<Derive *>(p2);
    if (p3 == NULL)
    {
        cout << "NULL" << endl; // 输出
    }
    else
    {
        cout << "NOT NULL" << endl;
    }

    return 0;
}
```

