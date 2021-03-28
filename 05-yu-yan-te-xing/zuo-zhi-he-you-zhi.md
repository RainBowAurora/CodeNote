---
description: 面试高频指数：★★★★☆
---

# 左值和右值

左值：指表达式结束后依然存在的持久对象。

右值：表达式结束就不再存在的临时对象。

> 左值和右值的区别：左值持久，右值短暂

右值引用和左值引用的区别：

* 左值引用不能绑定到要转换的表达式、字面常量或返回右值的表达式。右值引用恰好相反，可以绑定到这类表达式，但不能绑定到一个左值上。 
* 右值引用必须绑定到右值的引用，通过 && 获得。右值引用只能绑定到一个将要销毁的对象上，因此可以自由地移动其资源。

> `std::move` 可以将一个左值强制转化为右值，继而可以通过右值引用使用该值，以用于移动语义

```cpp
#include <iostream>
using namespace std;

void fun1(int& tmp) 
{ 
  cout << "fun1(int& tmp):" << tmp << endl; 
} 

void fun2(int&& tmp) 
{ 
  cout << "fun2(int&& tmp)" << tmp << endl; 
} 

int main() 
{ 
  int var = 11; 
  fun1(12); // error: cannot bind non-const lvalue reference of type 'int&' to an rvalue of type 'int'
  fun1(var);
  fun2(1); 
}
```

### move的实现

函数原型:

```text
template <typename T>
typename remove_reference<T>::type&& move(T&& t)
{
	return static_cast<typename remove_reference<T>::type &&>(t);
}
```

引用折叠原理：

* 右值传递给上述函数的形参 T&& 依然是右值，即 T&& && 相当于 T&&。 
* 左值传递给上述函数的形参 T&& 依然是左值，即 T&& & 相当于 T&。 

> 小结：通过引用折叠原理可以知道，move\(\) 函数的形参既可以是左值也可以是右值。

`remove_reference`具体实现：

```text
//原始的，最通用的版本
template <typename T> struct remove_reference{
    typedef T type;  //定义 T 的类型别名为 type
};
 
//部分版本特例化，将用于左值引用和右值引用
template <class T> struct remove_reference<T&> //左值引用
{ typedef T type; }
 
template <class T> struct remove_reference<T&&> //右值引用
{ typedef T type; }   
  
//举例如下,下列定义的a、b、c三个变量都是int类型
int i;
remove_refrence<decltype(42)>::type a;             //使用原版本
remove_refrence<decltype(i)>::type  b;             //左值引用特例版本
remove_refrence<decltype(std::move(i))>::type  b;  //右值引用特例版本
```

> std::move\(\) 实现原理：
>
> 1. 利用引用折叠原理将右值经过 T&& 传递类型保持不变还是右值，而左值经过 T&& 变为普通的左值引用，以保证模板可以传递任意实参，且保持类型不变； 
> 2. 然后通过 `remove_refrence` 移除引用，得到具体的类型 T；
> 3.  最后通过 `static_cast<>` 进行强制类型转换，返回 T&& 右值引用。

