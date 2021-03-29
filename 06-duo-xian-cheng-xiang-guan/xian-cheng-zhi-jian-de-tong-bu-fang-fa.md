---
description: 面试高频指数：★★★★★
---

# 线程之间的同步方法

### 互斥和同步

互斥：一个公共资源同一时刻只能被一个进程或线程使用，多个进程或线程不能同时使用公共资源。

同步：两个或两个以上的进程或线程在运行过程中协同步调，按预定的先后次序运行。

解决方法：互斥锁，条件变量，读写锁，自旋锁，信号量（互斥与同步）

### 线程同步方法

#### 1. 互斥锁（[std::mutex](https://en.cppreference.com/w/cpp/thread/mutex)）C++11

互斥锁是一种简单的加锁的方法来控制对**共享资源**的访问，互斥锁只有两种状态,即**上锁\( lock \)和解锁\( unlock \)**。

> 特点：唯一性，原子性，非繁忙等待

```cpp
#include <mutex>

//互斥量
std::mutex mxutex; //独占互斥量
std::timed_mutex mutex; //带超时的互斥量
std::recursive_mutex mutex; //递归的独占互斥量
std::recursive_timed_mutex mutex; //带有超时的递归互斥量
```

> 需要注意的是尽量不要使用递归\(**std::recursive\_mutex**\)锁好，主要原因如下：
>
> * 需要用到递归锁定的多线程互斥处理往往本身就是可以简化的，允许递归互斥很容易放纵复杂逻辑产生，从而导致一些多线程同步引起的晦涩问题。 
> * 递归锁比起非递归锁，效率会低一些。
> *  递归锁虽然允许同一个线程多次获得同一互斥量，可重复获得的最大次数并未具体说明，一旦超过一定次数，再对lock进行调用就会抛出std::system错误。

#### 乐观锁和悲观锁

* 悲观锁: 总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁（共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程）。
* 乐观锁: 总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现。乐观锁适用于多读的应用类型，这样可以提高吞吐量。

#### 2. 条件变量\([std::condition\_variable](https://zh.cppreference.com/w/cpp/thread/condition_variable)\) C++11

条件变量是用来等待而不是用来上锁的。条件变量用来自动阻塞一个线程，直 到某特殊情况发生为止。适合多个线程等待某个条件的发生，不使用条件变量，那么每个线程就不断尝试互斥锁并检测条件是否发生，浪费系统资源。

```cpp
#include <codition_variable>

std::mutex mut;
std::condition_variable cv;

std::unique_lock<std::mutex> lk(m);
cv.wait(lk, []{return ready;}); //cv.wait需要配合unique_lock一同使用

cv.notify_one(); //通知一个等待的线程
cv.notify_broadcast(); //通知所有等待的线程
```

#### 3. 读写锁\([std::shared\_mutex](https://zh.cppreference.com/w/cpp/thread/shared_mutex)\)C++17

三种状态：**读模式下加锁状态、写模式加锁状态、不加锁状态**

读写锁把对共享资源的访问者划分成读者和写者，读者只对共享资源进行读访问，写者则需要对共享资源进行写操作。C++17开始，标准库提供了shared\_mutex类（在这之前，可以使用boost的shared\_mutex类或系统相关api）。

```cpp
class ThreadSafeCounter {
public:
	ThreadSafeCounter() = default;
 
	// 多个线程/读者能同时读计数器的值。
	unsigned int get() const {
		std::shared_lock<std::shared_mutex> lock(mutex_);
		return value_;
	}
 
	// 只有一个线程/写者能增加/写线程的值。
	void increment() {
		std::unique_lock<std::shared_mutex> lock(mutex_);
		value_++;
	}
 
	// 只有一个线程/写者能重置/写线程的值。
	void reset() {
		std::unique_lock<std::shared_mutex> lock(mutex_);
		value_ = 0;
	}
 
private:
	mutable std::shared_mutex mutex_;
	unsigned int value_ = 0;
};
```

{% file src="../.gitbook/assets/atomic\_rw\_lock.h" caption="Apollo自动驾驶-读写锁" %}

#### 

#### 4. 自旋锁

自旋锁与互斥量功能一样，唯一一点不同的就是互斥量阻塞后休眠让出cpu，而自旋锁阻塞后不会让出cpu，会一直忙等待，直到得到锁。自旋锁在用户态使用的比较少，在内核使用的比较多！自旋锁的使用场景：锁的持有时间比较短，或者说小于2次上下文切换的时间。

```cpp
#include <atomic>

//使用CAS原理实现自旋锁
class spin_mutex {
  std::atomic<bool> flag = ATOMIC_VAR_INIT(false);
public:
  spin_mutex() = default;
  spin_mutex(const spin_mutex&) = delete;
  spin_mutex& operator= (const spin_mutex&) = delete;
  void lock() {
    while(flag.exchange(true, std::memory_order_acquire)){
      std::this_thread::yield();   //Saveing CPU ...   
    }
  }
  void unlock() {
    flag.store(false, std::memory_order_release);
  }
};
```

CAS的缺点:

> 1. **CPU开销过大**：在并发量比较高的情况下，如果许多线程反复尝试去更新一个变量，却又一直更新失败，循环往复，会消耗CPU很多资源。
> 2. **ABA问题**：假设在内存中有一个值为A的变量储存在内存地址V当中，此时有三个线程使用CAS机制更新这个变量的值，每个线程的执行时间都略有偏差，线程1和线程2已经获取当前值，线程3还没有获取当前值。接下来线程1先一步执行成功，把当前值成功从A更新为B，同时线程2因为某种原因被阻塞，没有做更新操作，线程3在线程1更新成功之后获取了当前值B，再之后线程2仍然阻塞，线程3继续执行，成功将当前值更新为A，最后，线程2终于恢复了运行状态，由于线程2之前获取了“当前值A”并且经过了Compare检测，内存地址中的实际值也是A，所以线程2最后把变量A更新成了B，在这个过程中，线程2获取的当前值是一个旧值，尽管和当前值一模一样，但是内存地址中V中的变量已经经历了A-&gt;B-&gt;A的改变。可以**通过引用计数增加版本号解决**。

#### 

#### 5. 信号量

信号量本质上是一个非负的整数计数器，它被用来控制对公共资源的访问。编程时可根据操作信号量值的结果判断是否对公共资源具有访问的权限，当信号量值大于 0 时，则可以访问，否则将阻塞。[PV](https://www.baidu.com/link?url=f8bxCHdk4fWLu4WWY-jrELDX7uzlTXPUSDjRE5KdcOwsLMqRXUhtCCuNRiLgVLMSXeQJxFdlX6rRvVrfrXmT7eOQSUZpeHMBPvE5lQx8O7i&wd=&eqid=f94e1683002cd24b000000026060325a) 原语是对信号量的操作，一次 P 操作使信号量减１，一次 V 操作使信号量加１。

信号量实现原理：

* **信号量是一个持有一个计数器cnt，并且支持 Up 和 Down 操作的同步器。**
* **Up增加cnt变量，并在cnt变量大于0的时候唤醒一个线程，并不会去堵塞线程。**
* **Down操作则是反着来，Down操作减少cnt变量，并在cnt变量小于0的时候堵塞执行down操作的线程** 

```cpp
#include <condition_variable>
#include <mutex>

class Semaphore{
public:
  void Up(){
    std::unique_lock<std::mutex> lock{mtx};
    cnt++;
    cv.notify_one();
  }
  
  void Down(){
    std::unique_lock<std::mutex> lock{mtx};
    cv.wait(lock, [this]{return cnt > 0;});
    cnt--;
}
  
private:
  int cnt;
  std::mutex mtx;
};

```

> 参考： 
>
> 1. [知乎-c++多线程同步](https://zhuanlan.zhihu.com/p/80308146)
> 2. [CSDN-C++11有关线程同步的使用](https://blog.csdn.net/fengxinlinux/article/details/76686829)
> 3. [CSDN-C++11实现自旋锁](https://blog.csdn.net/sharemyfree/article/details/47338001)
> 4. [简书-C++11实现简单的信号量](https://www.jianshu.com/p/b1a57ca0e71d)
> 5. [CSDN-面试必备之乐观锁与悲观锁](https://blog.csdn.net/qq_34337272/article/details/81072874)

