---
description: 面试高频指数：★★★★☆
---

# new相关

### new和malloc之间的区别

* malloc、free 是库函数，而new、delete 是关键字。 
* new 申请空间时，无需指定分配空间的大小，编译器会根据类型自行计算；malloc 在申请空间时，需要确定所申请空间的大小。 
* new 申请空间时，返回的类型是对象的指针类型，无需强制类型转换，是类型安全的操作符；malloc 申请空间时，返回的是 void\* 类型，需要进行强制类型的转换，转换为对象类型的指针。 
* new 分配失败时，会抛出 bad\_alloc 异常，malloc 分配失败时返回空指针。 
* 对于自定义的类型，new 首先调用 operator new\(\) 函数申请空间（底层通过 malloc 实现），然后调用构造函数进行初始化，最后返回自定义类型的指针；delete 首先调用析构函数，然后调用 operator delete\(\) 释放空间（底层通过 free 实现）。malloc、free 无法进行自定义类型的对象的构造和析构。 
* new 操作符从自由存储区上为对象动态分配内存，而 malloc 函数从堆上动态分配内存。（自由存储区不等于堆\)。

### new和malloc如何判断是否申请到内存

* `new`： 如果成功申请到内存则返回该对象类型指针，如果申请失败则抛出`std::bad_alloc`异常。
* `malloc`：如果成功申请到内存则返回void\* 类型的指针，如果申请失败则返回NULL指针。

### new/delete分配方式如下

* new -~~**size**-~~&gt; operator new\(\) -&gt; mallock\(\) -&gt; constructor fun -&gt; ptr
* delete -&gt; destructor\(\) -&gt; operator delete\(\) -&gt; free\(\)
* new\[count\] -~~**size+4**~~-&gt; operator new\[\] -&gt; mallock\(\) -~~CallCount次~~-&gt; constructor fun -&gt; ptr
* delete\[\] -~~CallCount次~~-&gt; destructor\(\) -&gt; operator delete\(\) -&gt; free\(\) 

> 使用`new[]/delete[]`会多开辟4个字节用来存储开辟的对象个数



