---
description: 面试高频指数：★☆☆☆☆
---

# 自旋锁

### 自旋锁的原理

自旋锁又两种状态：

1. 锁定状态：锁定状态又被称为不可用状态，当自旋锁被某个线程持有时候就是锁定状态，在自旋锁被释放之前其他线程不能获得锁的多有权。
2. 可用状态：当自旋锁未被任何线程持有时候的状态就是可用状态。

### 使用C++11原子操作实现自旋锁

```cpp
#include <iostream>
#include <atomic>

class spin_mutex{
public:
    spin_mutex() = default;
    spin_mutex(const spin_mutex&) = delete;
    spin_mutex& operator=(const spin_mutex&) = delete;
    void lock(); //获取自旋锁
    void unlock(); //释放自旋锁
private:
    std::atomic<bool> flag = ATOMIC_VAR_INIT(false);
};

void spin_mutex::lock()
{
    bool expected = false;
    while(!flag.compare_exchange_strong(expected, true)){
        expected = false;
    }
}

void spin_mutex::unlock()
{
    flag.store(false);
}
```

> **bool compare\_exchange\_weak\( T& expected, T desired\):**
>
> 1.  **\*this == expected \[==&gt;\] \*this == desired** 
> 2. **\*this != expected \[==&gt;\] expected == \*this**

## Reference

1. \[c++11实现自旋锁\]\([https://blog.csdn.net/jeffasd/article/details/80661804](https://blog.csdn.net/jeffasd/article/details/80661804)\)
2. \[cppreference-compare\_exchange\_strong\]\([https://zh.cppreference.com/w/cpp/atomic/atomic/compare\_exchange](https://zh.cppreference.com/w/cpp/atomic/atomic/compare_exchange)\)

