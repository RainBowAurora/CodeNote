---
description: 面试高频指数：★★☆☆☆
---

# 菱形继承

代码示例:

```cpp
#include <iostream>
#include <stdio.h>

typedef void(*func)();

class Base{
public:
    int base_data;
    virtual void func0() { std::cout << "Base::func0()\n"; };
    virtual void func1() { std::cout << "Base::func1()\n"; };
    virtual void func2() { std::cout << "Base::func2()\n"; };
    virtual void func3() { std::cout << "Base::func3()\n"; };
    virtual ~Base() {};
};

class Derive1:  virtual public Base{
public:
    int derive1_data;
    virtual void func1() { std::cout << "Derive::func1()\n"; };
    virtual ~Derive1() {};
};

class Derive2: virtual public Base{
public:
    int derive2_data;
    virtual void func2() { std::cout << "Derive1::func2()\n"; };
    virtual ~Derive2() {};
};

class Entity: public Derive1, public Derive2{
public:
    virtual void func3() { std::cout << "Entity::func3()\n"; };
    virtual ~Entity() {};
};
```

内存布局:

![&#x56FE;5. Entity&#x7684;&#x865A;&#x8868;](../.gitbook/assets/image%20%2811%29.png)

