# 线程安全的对象声明周期管理

编写线程安全的类不是难事，用同步原语（synchronization primitives）保护内 部状态即可。但是对象的生与死不能由对象自身拥有的 mutex（互斥器）来保护。如 何避免对象析构时可能存在的 race condition（竞态条件）是 C++ 多线程编程面临的 基本问题，可以借助 STL 库中的 shared\_ptr 和 weak\_ptr 完美解决。

### 当析构函数遇到多线程

当一个对象能被多个线程同时看到时，那么对象的销毁时机就 会变得模糊不清，可能出现多种竞态条件\(race condition\):

* 在即将析构一个对象时，从何而知此刻是否有别的线程正在执行该对象的成员函数?
* 如何保证在执行成员函数期间，对象不会在另一个线程被析构？
* 在调用某个对象的成员函数之前，如何得知这个对象还活着？它的析构函数会不会碰巧执行到一半？

#### 线程安全的定义

⭐依据 \[JCP\]，一个线程安全的 class 应当满足以下三个条件：

1. 多个线程同时访问时，其表现出正确的行为。
2. 无论操作系统如何调度这些线程， 无论这些线程的执行顺序如何交织 （interleaving）。
3. 调用端代码无须额外的同步或其他协调动作。

> 依据这个定义，C++ 标准库里的大多数 class 都不是线程安全的，包括 std:: string、std::vector、std::map 等，因为这些 class 通常需要在外部加锁才能供多个 线程同时访问。

#### 线程安全的Conter例子

如同下面的例子一样，尽管这个 Counter 本身毫无疑问是线程安全的，但如果 Counter 是动态创建的并 通过指针来访问，前面提到的对象销毁的 race condition 仍然存在。

```cpp
#include <iostream>
#include <thread>
#include <mutex>

class Conter{
public:
    Conter(): value_(value){ }
    int64_t value() const;
    int64_t getAndInstance();
    
private:
    int value_;
    mutable std::mutex mutex_; 
};


int64_t value() const
{
    std::lock_guard<std::mutex> lock(mutex_); //lock的作用域刚好和临界区重合
    return value_;
}

int64_t getAndInstance()
{
    std::lock_guard<std::mutex> lock(mutex_);
    int64_t ret = value_++;
    return ret;
}

//这里只是举例子，真实的情况下用原子操作std::automic更加合理
```

> 注意到其 mutex _成员是 **mutable** 的，意味着 const 成员函数如 Counter::value\(\) 也能直接使 用 non-const 的 mutex_。

### 线程安全对象的创建

#### 构造对象

如何保证对象在的构造时是线程安全，唯一的要求就是在构造期间不要暴漏this指针：

* 不要在构造函数中注册任何回调函数;
* 也不要在构造函数中把this指针传给跨线程对象;
* 即便在构造函数中的最后一行也不可以。

> 这样做的目的是在构造函数执行期间的对象还没有完成初始化，如果这时候把this暴露给其他对象（其自身创建的子对象除外）,那么别的线程有可能会使用这个半成品对象，造成不可预知的后果。

#### 销毁对象

相比于构造对象销毁对象做到线程安全更加困难，因为析构函数有可能会先将mutex成员变量销毁（成员函数用来保护临界区的互斥量必须有效）。

```text
Foo~Foo(){
    std::lock_guard<std::mutex> lock(mutex_);
    //free internal state
}

void Foo::update(){
    std::lock_guard<std::mutex> lock(mutex_);
    //make use of internal state
}
```

如果此时同时存在两个线程A、B能够看到Foo对象x，A线程正在销毁x， B线程正准备调用x-&gt;update\(\)，造成了race condition。

⭐解决方法，使用stl提供的智能指针来管理对象：

* std::shared\_ptr：是强引用其**控制**对象的生命周期，内部有一个引用计数器，当引用计数为0时候才释放对象调用析构函数。
* std::weak\_ptr: 是弱引用其**不控制**对象的生命周期，但是其可以知道当前引用对象是否还活着，如果对象还活着则可以进行提升操作获得有效的std::sharedptr，如果对象已经死亡提升操作会返回一个空的std::shared\_ptr。

> 使用智能指针的优势：一旦某对象不再被引用，系统刻不容缓，立刻回收内存。这 通常发生在关键任务完成后的清理（clean up）时期，不会影响关键任务的实时性， 同时，内存里所有的对象都是有用的，绝对没有垃圾空占内存。 --《垃圾收集机制批判》孟岩

### 系统的避免各种指针错误

C++里可能出现的内存问题和解决方法大致如下：

1. 缓冲区溢出（buffer overrun）： 用stl自带的容器库如std::vector&lt;DataType&gt;、std::string、std::list&lt;DataType&gt;等来管理缓冲区，自动记录缓冲区大小，并通过成员函数而不是裸指针来修改缓冲区。
2. 空悬指针/野指针：使用std::sharedptr、std::weak\_ptr等智能指针来管理对象。
3. 重复释放（double delete）：使用std::sharedptr智能指针来管理对象。
4. 内存泄漏（memory leak）：使用std::sharedptr、std::weak\_ptr等智能指针来管理对象。
5. 不配对的 new\[\]/delete：把 new\[\] 统统替换为 std::vector（现代的c++程序一般不会出现delete语句）。
6. 内存碎片（memory fragmentation）

> 空悬指针：指向已经销毁的对象或者已经收回的地址。
>
> 野指针：指的是未经初始化的指针，其指向的地址或对象不可确定。

### 总结

1. 原始指针暴露给多个线程往往会造成 race condition 或额外的簿记负担。
2. 如无特殊要求统一用 shared\_ptr/scoped\_ptr 来管理对象的生命期。
3. shared\_ptr 是值语意，当心意外延长对象的生命期。例如  容器可能拷贝shared\_ptr。
4. weak\_ptr 是 shared\_ptr 的好搭档，可以用来解决循环应用导致的内存泄漏问题。

### Reference

* 《Linux多线程服务端编程：使用muduo%20C++网络库》-- 陈硕

