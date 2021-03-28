---
description: 面试高频指数：★★☆☆☆
---

# 线程安全单例

### 单例模式的特点

* 构造函数和析构函数为**private**类型，目的**禁止**外部构造和析构
* 拷贝构造和赋值构造函数为**private**类型，目的是**禁止**外部拷贝和赋值，确保实例的唯一性
* 类里有个获取实例的**静态函数**，可以全局访问

### 懒汉OR饿汉

* 懒汉: 指系统运行中，实例并不存在，只有当需要使用该实例时，才会去创建并使用实例。（这种方式要考虑线程安全，推荐使用内部静态变量的懒汉单例）
* 饿汉: 指系统一运行，就初始化创建实例，当需要时，直接调用即可。（本身就线程安全，没有多线程的问题）

### 线程安全单例

* **饿汉模式\(本身就是线程安全\)**

```cpp
class Singleton
{
public:
    // 获取单实例
    static Singleton* GetInstance(){
        return g_pSingleton;
    }

    // 释放单实例，进程退出时调用
    static void deleteInstance(){
        if (g_pSingleton)
        {
            delete g_pSingleton;
            g_pSingleton = NULL;
        }
    }

private:
    // 将其构造和析构成为私有的, 禁止外部构造和析构
    Singleton(){}; 
    ~Singleton(){ deleteInstance(); };

    // 将其拷贝构造和赋值构造成为私有函数, 禁止外部拷贝和赋值
    Singleton(const Singleton &) = delete;
    Singleton &operator=(const Singleton &) = delete;

private:
    // 唯一单实例对象指针
    static Singleton *g_pSingleton;
};

// 代码一运行就初始化创建实例 ，本身就线程安全
Singleton* Singleton::g_pSingleton = new (std::nothrow) Singleton;

Singleton* Singleton::GetInstance()
{
    return g_pSingleton;
}

void Singleton::deleteInstance()
{
    if (g_pSingleton)
    {
        delete g_pSingleton;
        g_pSingleton = NULL;
    }
}

```

* **懒汉模式\(双检锁方式\)**

```cpp
#include <iostream>
#include <mutex>

template<typename T>
class DoubleCheckSingletion{
public:
    static T* InstancePtr();  // 获取单实例对象
    static T& Instance() { return *InstancePtr(); } // 获取单实例对象指针
    static void Destory();  //释放单实例，进程退出时调用

protected:
    // 将其构造和析构成为私有的, 禁止外部构造和析构
    DoubleCheckSingletion() = default;
    ~DoubleCheckSingletion() = default;

private:
    static T* volatile ptr_instance_;  //声明为volatile才能起到线程安全作用
    static std::mutex mutex_;

    DoubleCheckSingletion(const DoubleCheckSingletion&) = delete;
    DoubleCheckSingletion& operator=(const DoubleCheckSingletion) = delete;
};

template<typename T>
T* volatile DoubleCheckSingletion<T>::ptr_instance_ = nullptr;

template<typename T>
std::mutex DoubleCheckSingletion<T>::mutex_;

template<typename T>
T* DoubleCheckSingletion<T>::InstancePtr()
{
    if(ptr_instance_ == nullptr){
        //  这里使用了两个 if判断语句的技术称为双检锁；好处是，只有判断指针为空的时候才加锁，
        //  避免每次调用 GetInstance的方法都加锁，锁的开销毕竟还是有点大的。
        std::lock_guard<std::mutex> lock(mutex_);
        if(ptr_instance_ == nullptr){
            ptr_instance_ = new T();
        }   
    }   
    return ptr_instance_;
}

template<typename T>
void DoubleCheckSingletion<T>::Destory()
{
    if(ptr_instance_ != nullptr){
        std::lock_guard<std::mutex> lock(mutex_);
        if(ptr_instance_ != nullptr){
            delete ptr_instance_;
        }   
    }   
    ptr_instance_ = nullptr;
}
```

> 参考: [博客园-线程安全单例](https://www.cnblogs.com/joean/p/4601297.html)

* **内部静态变量的懒汉单例（C++11 线程安全）**

```cpp
#include <iostream>
class MeyersSingletion{
public:
    static MeyersSingletion& Instance(){
        static MeyersSingletion instance(param);
        return instance;
    }   
    static int param;
    int storage;

private:
    MeyersSingletion(int m): storage(m){};
    
    MeyersSingletion() = delete;
    ~MeyersSingletion() = default;
    //禁止拷贝和复制
    MeyersSingletion(const MeyersSingletion&) = delete;
    MeyersSingletion& operator=(const MeyersSingletion&) = delete;
};
```

* **CallOnce\(STL 标准库\)**

```cpp
#include <iostream>
#include <mutex>
#include <memory>

class CallOnceSingleton {
public:
  static CallOnceSingleton& Instance() {
    static std::once_flag s_flag;
    std::call_once(s_flag, [&]() {
      instance_.reset(new CallOnceSingleton);
    }); 

    return *instance_;
  }

  ~CallOnceSingleton() = default;

  void PrintAddress() const {
    std::cout << this << std::endl;
  }

private:
  CallOnceSingleton() = default;

  CallOnceSingleton(const CallOnceSingleton&) = delete;
  CallOnceSingleton& operator=(const CallOnceSingleton&) = delete;

private:
  static std::unique_ptr<CallOnceSingleton> instance_;
};

std::unique_ptr<CallOnceSingleton> CallOnceSingleton::instance_;

```

### 特点与选择

* 懒汉式是以时间换空间，适应于访问量较**小**时；推荐使用**内部静态变量的懒汉单例**。
* 饿汉式是以空间换时间，适应于访问量较**大**时，或者线程比较多的的情况。

> Reference：
>
> 1. [知乎-线程安全单例](https://zhuanlan.zhihu.com/p/142573902)



