---
description: 面试高频指数：★★★★★
---

# c++11智能指针

智能指针是为了解决动态内存分配时带来的内存泄漏以及多次释放同一块内存空间而提出的。C++11 中封装在了 `<memory>` 头文件中。

### 智能指针的分类

C++11 中智能指针包括以下三种：

* **共享指针（shared\_ptr）**：资源可以被多个指针共享，使用计数机制表明资源被几个指针共享。通过 `use_count`\(\) 查看资源的所有者的个数，可以通过 `unique_ptr`、`weak_ptr` 来构造，调用 `release()`释放资源的所有权，计数减一，当计数减为 0 时，会自动释放内存空间，从而避免了内存泄漏。 
* **独占指针（unique\_ptr**）：独享所有权的智能指针，资源只能被一个指针占有，该指针不能拷贝构造和赋值。但可以进行移动构造和移动赋值构造（调用 `move()` 函数），即一个 `unique_ptr`对象赋值给另一个 `unique_ptr` 对象，可以通过该方法进行赋值。 
* **弱指针（weak\_ptr**）：指向 `share_ptr` 指向的对象，能够解决由shared\_ptr带来的循环引用问题。

### 智能指针的循环引用问题

![](../.gitbook/assets/image%20%283%29.png)

**循环引用**：在如下例子中定义了两个类 Parent、Child，在两个类中分别定义另一个类的对象的共享指针，由于在程序结束后，两个指针相互指向对方的内存空间，导致内存无法释放。

```cpp
#include <iostream>
#include <memory>

class Parent;
class Child;

class Parent{
public:
    void SetChild(std::shared_ptr<Chilld> child){
        this->child_ptr_ = child;
    }
private:
    std::shared_ptr<Child> child_ptr_;
};

class Child{
public:
    void SetParent(std::shared_ptr<Parent> parent){
        this->parent_ptr = parent;
    }
private:
    std::shared_ptr<Parent> parent_ptr_;
};


int main(int argc, char *argv[])
{
    std::weak_ptr<Parent> weak_parent;
    std::weak_ptr<Child> weak_child;
    {
        std::shared_ptr<Parent> shared_parent(new Parent); 
        std::shared_ptr<Child> shared_chilld(new Child);
        
        shared_parent.SetChild(shared_chilld);
        shared_chilld.SetParent(shared_parent);
        
        weak_parent = shared_parent;
        weak_child = shared_chilld;
        
        std::cout <<  "shared_parent.use_count = " << \
                     shared_parent.use_count() << std::endl; // 2
                        
        std::cout <<  "shared_chilld.use_count = " << \
                     shared_chilld.use_count() << std::endl; // 2
    }
    
        std::cout <<  "weak_parent.use_count = " << \
                     weak_parent.use_count() << std::endl; // 1    
                                  
        std::cout <<  "weak_child.use_count = " << \
                     weak_child.use_count() << std::endl; // 1
                     
    return 0;
}

```

### 循环引用的解决方法

打破循环引用: 使用C++11中的智能指针`weak_ptr`:

* `weak_ptr`对被 `shared_ptr`管理的对象存在 非拥有性（弱）引用，在访问所引用的对象前必须先转化为 `shared_ptr`； 
* `weak_ptr` 来打断 `shared_ptr`所管理对象的循环引用问题，若这种环被孤立（没有指向环中的外部共享指针），`shared_ptr`引用计数无法抵达 0，内存被泄露；令环中的指针之一为弱指针可以避免该情况； 
* `weak_ptr` 用来表达临时所有权的概念，当某个对象只有存在时才需要被访问，而且随时可能被他人删除，可以用 `weak_ptr` 跟踪该对象；需要获得所有权时将其转化为 `shared_ptr`，此时如果原来的 `shared_ptr` 被销毁，则该对象的生命期被延长至这个临时的 `shared_ptr` 同样被销毁。

```cpp
#include <iostream>
#include <memory>

class Parent;
class Child;

class Parent{
public:
    void SetChild(std::shared_ptr<Chilld> child){
        this->child_ptr_ = child;
    }
private:
    std::shared_ptr<Child> child_ptr_;
};

class Child{
public:
    void SetParent(std::shared_ptr<Parent> parent){
        this->parent_ptr = parent;
    }
private:
    std::weak_ptr<Parent> parent_ptr_;
};


int main(int argc, char *argv[])
{
    std::weak_ptr<Parent> weak_parent;
    std::weak_ptr<Child> weak_child;
    
    {
        std::shared_ptr<Parent> shared_parent(new Parent); 
        std::shared_ptr<Child> shared_chilld(new Child);
        
        shared_parent.SetChild(shared_chilld);
        shared_chilld.SetParent(shared_parent);
        
        weak_parent = shared_parent;
        weak_child = shared_chilld;
        
        std::cout <<  "shared_parent.use_count = " << \
                     shared_parent.use_count() << std::endl; // 1
                        
        std::cout <<  "shared_chilld.use_count = " << \
                     shared_chilld.use_count() << std::endl; // 2
    }
    
        std::cout <<  "weak_parent.use_count = " << \
                     weak_parent.use_count() << std::endl; // 0    
                                  
        std::cout <<  "weak_child.use_count = " << \
                     weak_child.use_count() << std::endl; // 0
                     
    return 0;
}

```

