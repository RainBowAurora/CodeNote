---
description: 面试高频指数：★★★☆☆
---

# 对象创建限制在堆或栈

C++ 中的类的对象的建立分为两种：

* 静态建立：由编译器为对象在栈空间上分配内存，直接调用类的构造函数创建对象。例如：A a; 
* 动态建立：使用 new 关键字在堆空间上创建对象，底层首先调用 operator new\(\) 函数，在堆空间上寻找合适的内存并分配；然后，调用类的构造函数创建对象。例如：A \*p = new A\(\);

### 限制对象只能在堆上创建

* 将构造函数的属性设置为`private`，保证子类能够访问析构函数。
* 提供一个`public`静态函数来完成构造，而不是使用new在类外创建。

```cpp
class A
{
protected:
    A() {}
    ~A() {}

public:
    static A *create()
    {
        return new A();
    }
    void destory()
    {
        delete this;
    }
};
```

### 限制对象只能在栈上创建

* 将`operator new()`设置为私有，原因是当对象建立在堆上时，是采用 `new` 的方式进行建立，其底层会调用 `operator new()` 函数，因此只要对该函数加以限制，就能够防止对象建立在堆上。
* 将operator delete\(\) 设置为私有，禁止外部调用delete运算符，理由同上。

```cpp
class A
{
private:
    void *operator new(size_t t) {}    // 注意函数的第一个参数和返回值都是固定的
    void operator delete(void *ptr) {} // 重载了 new 就需要重载 delete
public:
    A() {}
    ~A() {}
};
```





