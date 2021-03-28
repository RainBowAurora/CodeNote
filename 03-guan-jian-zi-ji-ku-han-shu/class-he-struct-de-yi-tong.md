---
description: 面试高频指数：★★☆☆☆
---

# class 和 struct 的异同

* struct 和 class 都可以自定义数据类型，也支持继承操作。 
* struct 中默认的访问级别是 public，默认的继承级别也是 public；class 中默认的访问级别是 private，默认的继承级别也是 private。 
* 当 class 继承 struct 或者 struct 继承 class 时，默认的继承级别取决于 class 或 struct 本身， class（private 继承），struct（public 继承），即取决于派生类的默认继承级别。

```text
struct A{}；
class B : A{}; // private 继承 
struct C : B{}； // public 继承
```

*  `class` 可以用于定义模板参数，`struct` 不能用于定义模板参数。

```text
#include<iostream>

using namespace std;

class A{
public:
    void funA(){
        cout << "class A" << endl;
    }
};

struct B: A{ // 由于 B 是 struct，A 的默认继承级别为 public
public:
    void funB(){
        cout << "class B" << endl;
    }
};

class C: B{ // 由于 C 是 class，B 的默认继承级别为 private，所以无法访问基类 B 中的 funB函数

};

int main(){
    A ex1;
    ex1.funA(); // class A

    B ex2;
    ex2.funA(); // class A
    ex2.funB(); // class B

    C ex3;
    ex3.funB(); // error: 'B' is not an accessible base of 'C'.
    return 0;
}
```

