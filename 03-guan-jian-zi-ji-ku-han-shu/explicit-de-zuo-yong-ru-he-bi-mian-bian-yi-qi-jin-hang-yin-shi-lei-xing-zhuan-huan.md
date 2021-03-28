---
description: 面试高频指数：★★★☆☆
---

# explicit 的作用（如何避免编译器进行隐式类型转换）

## 隐式声明

`explicit`: 用来声明类构造函数是显示调用的，而非隐式调用，可以阻止调用构造函数时进行隐式转换。只可用于修饰单参构造函数，因为无参构造函数和多参构造函数本身就是显示调用的，再加上 explicit 关键字也没有什么意义。

```text
#include <iostream>
#include <cstring>
using namespace std;

class A
{
public:
    int var;
    A(int tmp)
    {
        var = tmp;
    }
};
int main()
{
    A ex = 10; // 发生了隐式转换
    return 0;
}
```

> 上述代码中，`A ex = 10;` 在编译时，进行了隐式转换，将 `10` 转换成 `A` 类型的对象，然后将该对象赋值给 `ex`

### 如何避免隐式声明:

```text
#include <iostream>
#include <cstring>
using namespace std;

class A
{
public:
    int var;
    explicit A(int tmp)
    {
        var = tmp;
        cout << var << endl;
    }
};
int main()
{
    A ex(100);
    A ex1 = 10; // error: conversion from 'int' to non-scalar type 'A' requested
    return 0;
}
```

