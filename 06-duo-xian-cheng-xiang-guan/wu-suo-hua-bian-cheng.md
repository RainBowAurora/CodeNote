---
description: 面试高频指数：★★★☆☆
---

# 无锁化编程（CAS）

### 原子操作

原子操作：顾名思义就是不可分割的操作，该操作只存在未开始和已完成两种状态，不存在中间状态；

原子类型：原子库中定义的数据类型，对这些类型的所有操作都是原子的，包括通过原子类模板std::atomic&lt; T &gt;实例化的数据类型，也都是支持原子操作的。

| 原子类型 | 特化版本 |
| :--- | :--- |
| **atomic\_bool**\(C++11\) | std::atomic&lt;bool&gt; |
| **atomic\_char**\(C++11\) | std::atomic&lt;char&gt; |
| **atomic\_int**\(C++11\) | std::atomic&lt;int&gt; |
| **atomic\_long**\(C++11\) | std::atomic&lt;long&gt; |
| ... | ... |

> 具体支持原子化操作的变量可以自行查阅：[cppreference](https://zh.cppreference.com/w/cpp/atomic/atomic)

#### 如何进行无锁化编程

在原子操作出现之前，对共享数据的读写可能得到不确定的结果，所以多线程并发编程时要对使用锁机制对共享数据的访问过程进行保护。但锁的申请释放增加了访问共享资源的消耗，且可能引起线程阻塞、锁竞争、死锁、优先级反转、难以调试等问题。

**CAS\(Compare And Swap\)原子操作，它是无锁编程中最常用的操作。**

```cpp
#include <atomic>
template<typename T>
struct node
{
    T data;
    node* next;
    node(const T& data) : data(data), next(nullptr) {}
};
 
template<typename T>
class stack
{
    std::atomic<node<T>*> head;
 public:
    void push(const T& data)
    {
      node<T>* new_node = new node<T>(data);
 
      // put the current value of head into new_node->next
      new_node->next = head.load(std::memory_order_relaxed);
 
      // now make new_node the new head, but if the head
      // is no longer what's stored in new_node->next
      // (some other thread must have inserted a node just now)
      // then put that new head into new_node->next and try again
      while(!head.compare_exchange_weak(new_node->next, new_node,
                                        std::memory_order_release,
                                        std::memory_order_relaxed))
          ; // the body of the loop is empty
 
// Note: the above use is not thread-safe in at least 
// GCC prior to 4.8.3 (bug 60272), clang prior to 2014-05-05 (bug 18899)
// MSVC prior to 2014-03-17 (bug 819819). The following is a workaround:
//      node<T>* old_head = head.load(std::memory_order_relaxed);
//      do {
//          new_node->next = old_head;
//       } while(!head.compare_exchange_weak(old_head, new_node,
//                                           std::memory_order_release,
//                                           std::memory_order_relaxed));
    }
};
int main()
{
    stack<int> s;
    s.push(1);
    s.push(2);
    s.push(3);
}
```

**memory\_order内存顺序:**

| 参数 | 说明 |
| :--- | :--- |
| memory\_order\_consume | 本线程所有后续有关本操作的必须在本操作完成后执行 |
| memory\_order\_acquire | 本线程所有后续的读操作必须在本条操作完成才能执行 |
| memory\_order\_release | 本线程所有之前的写操作完成后才执行本操作 |
| memory\_order\_relaxed | 不对执行顺序做任何保证 |
| memory\_order\_acq\_rel | 同时包含acquire和release |
| memory\_order\_seq\_cst | 全部顺序执行 |



Reference：

> 1. [知乎-linux下C++多线程并发之原子操作与无锁编程](https://zhuanlan.zhihu.com/p/149464798)
> 2. [CppReference-atomic](https://en.cppreference.com/w/cpp/atomic/atomic/compare_exchange)

