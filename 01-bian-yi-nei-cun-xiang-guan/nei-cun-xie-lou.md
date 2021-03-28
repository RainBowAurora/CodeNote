---
description: 面试高频指数：★★★★☆
---

# 内存泄漏

### 什么时内存泄漏 ?

**内存泄漏**：由于疏忽或错误导致的程序未能释放已经不再使用的内存。

* 并非指内存从物理上消失，而是指程序在运行过程中，由于疏忽或错误而失去了对该内存的控制，从而造成了内存的浪费。
* 常指堆内存泄漏，因为堆是动态分配的，而且是用户来控制的，如果使用不当，会产生内存泄漏。
* 使用 `malloc`、`calloc`、`realloc`、`new` 等分配内存时，使用完后要调用相应的 free 或 delete 释放内存，否则这块内存就会造成内存泄漏。
* 指针重新赋值， 开始时，指针 `p` 和 `p1` 分别指向一块内存空间，但指针 `p` 被重新赋值，导致 `p` 初始时指向的那块内存空间无法找到，从而发生了内存泄漏。

```cpp
char *p = (char *)malloc(10);
char *p1 = (char *)malloc(10);
p = np; //重新赋值导致对之前的内存失去控制
```

### 怎么防止内存泄漏?

1. 使用**RAII**\(资源获取即初始化\)，将指针封装到类内部，构造函数负责创建，析构函数负责释放。

```cpp
#include <iostream>
#include <cstring>

using namespace std;

class A
{
private:
    char *p;
    unsigned int p_size;

public:
    A(unsigned int n = 1) // 构造函数中分配内存空间
    {
        p = new char[n];
        p_size = n;
    };
    ~A() // 析构函数中释放内存空间
    {
        if (p != NULL)
        {
            delete[] p; // 删除字符数组
            p = NULL;   // 防止出现野指针
        }
    };
    char *GetPointer()
    {
        return p;
    };
};
void fun()
{
    A ex(100);
    char *p = ex.GetPointer();
    strcpy(p, "Test");
    cout << p << endl;
}
int main()
{
    fun();
    return 0;
}
```

> 但是这样处理会出现一些问题，比如类进行复制的时候会导致两个指针指向同一处，在调用析构函数时候会导致同一款释放两次。
>
> ```text
> void fun1()
> {
>     A ex(100);
>     A ex1 = ex; //通过拷贝构造函数进行复制
>     char *p = ex.GetPointer();
>     strcpy(p, "Test");
>     cout << p << endl;
> }
> ```

使用引用计数可避免释放同一内存两次的问题:

* 调用构造函数引用计数初始化为1
* 调用拷贝构造函数引用计数+1
* 调用赋值函数左边对象的引用计数+1，右边对象引用计数-1（如果引用计数为0释放）
* 调用析构函数引用计数-1（如果引用计数为0释放）

```cpp
#include <iostream>
#include <memory>
#include <cassert>

template<typename T>
class SmartPtr{
public:
    SmartPtr(T* ptr  = nullptr): ptr_(ptr){
        if(ptr != nullptr){
            count_ = new size_t(1);
        }else{
            count_ = new size_t(0);
        }
    }

    ~SmartPtr(){
        if(this->ptr_){
            (*this->count_)--;   //析构函数引用计数-1
            if(*this->count_ == 0){
                delete count_; 
                delete ptr_;
            }
        }
    }

    SmartPtr(const SmartPtr& other){
        if(this != &other){
            this->ptr_ = other.ptr_;
            this->count_ = other.count_;
            (*this->count_)++; //拷贝构造函数引用计数+1
        }
    }

    SmartPtr& operator=(const SmartPtr& other){
        if(this == &other) return *this; 
        if(this->ptr_){ // 将当前的 ptr 指向的原来的空间的计数 -1
            (*this->count_)--;
            if(*this->count_ == 0){
                delete this->count_;
                delete this->ptr_;
            }
        }
        this->count_ = other.count_;
        this->ptr_ = other.ptr_;
        (*this->count_)++; //指向的引用计数+1
        return *this;
    }

    T& operator*(){
        // assert(this->ptr_ == nullptr);
        return *(this->ptr_);
    }

    T* operator->(){
        assert(this->ptr_ == nullptr);
        return this->ptr_;
    }

    size_t UseCount(){
        return *this->count_;
    }

private:
    T* ptr_;
    size_t *count_;
};
```

2. 使用C++11中的智能指针

```cpp
#include <iostream>
#include <memory>

int main(int argc, char *argv[])
{
    std::shared_ptr<int> p1(new int(1));
    
    std::shared_ptr<int> p2(p1);
    
    std::shared_ptr<int> p3 = std::make_shared<int>(int(10086));
    
    return 0;
}
```

### 内存泄漏检查工具

1. \[valgrind\]\([http://elinux.org/Memory\_Debuggers\#valgrind](http://elinux.org/Memory_Debuggers#valgrind)\): 一个强大开源的程序检测工具
2. \[mtrace\]\([http://elinux.org/Memory\_Debuggers\#mtrace](http://elinux.org/Memory_Debuggers#mtrace)\): GNU扩展, 用来跟踪malloc, mtrace为内存分配函数（malloc, realloc, memalign, free）安装hook函数
3. \[dmalloc\]\([https://blog.csdn.net/gatieme/article/details/51959654](https://blog.csdn.net/gatieme/article/details/51959654)\): 用于检查C/C++内存泄露\(leak\)的工具，即检查是否存在直到程序运行结束还没有释放的内存,以一个运行库的方式发布

