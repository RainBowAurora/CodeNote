---
description: 面试高频指数：★☆☆☆☆
---

# 可变参数模板

### 可变参数模板介绍

可变参数模板：接受可变数目参数的模板函数或模板类。将可变数目的参数被称为参数包，包括模板参数包和函数参数包。

* 模板参数包：表示零个或多个模板参数；
*  函数参数包：表示零个或多个函数参数。 

> 用省略号来指出一个模板参数或函数参数表示一个包，在模板参数列表中，`class...` 或 `typename...` 指出接下来的参数表示零个或多个类型的列表；一个类型名后面跟一个省略号表示零个或多个给定类型的非类型参数的列表。当需要知道包中有多少元素时，可以使用 `sizeof...` 运算符。
>
> ```cpp
> template <typename T, typename... Args> // Args 是模板参数包
> void foo(const T &t, const Args&... rest); // 可变参数模板，rest 是函数参数包
> ```

```cpp
#include <iostream>

using namespace std;

template <typename T>
void print_fun(const T &t)
{
    cout << t << endl; // 最后一个元素
}

template <typename T, typename... Args>
void print_fun(const T &t, const Args &...args)
{
    cout << t << " ";
    print_fun(args...);
}

int main()
{
    print_fun("Hello", "wolrd", "!");
    return 0;
}
/*运行结果：
Hello wolrd !
*/
```

### 可变参数模板的应用

#### 可变模板参数的展开 <a id="2-&#x53EF;&#x53D8;&#x6A21;&#x677F;&#x53C2;&#x6570;&#x7684;&#x5C55;&#x5F00;"></a>

* 递归函数的方式展开参数包

```cpp
inline void PrintArgs(){ std::cout << std::endl; } //递归终结函数

template<typename T, typename ...Args>
inline void PrintArgs(T first, Args... rest)
{
    std::cout  << first << " ";
    PrintArgs(rest...);
}

PrintArgs("hello", 10086, 3.14);
```

> 使用lambda表达式，从而少些一个地址终止函数
>
> ```text
> template<typename F, typename ...Args>
> inline void PrintArgs(const F &f, Args &&...args)
> {
>     std::initializer_list<int>{(f(std::forward<Args>(args)), 0)...};
> }
>
> //需要c++14 支持 lambda的参数为auto
> //PrintArgs([](auto data){std::cout << data << std::endl;}, "hello", 10086, 3.14);
> ```

* 模板特化和递归的方式展开参数

```cpp
template<typename First, typename... Rest>
struct Sum
{
  enum { value = Sum<First>::value + Sum<Rest...>::value };
};

template<typename Last>
struct Sum<Last>
{
  enum{ value = sizeof(Last) };
};

//Sum<int, double, lang>::value;
```

#### 可变参数模板消除重复代码 <a id="3-&#x53EF;&#x53D8;&#x53C2;&#x6570;&#x6A21;&#x677F;&#x6D88;&#x9664;&#x91CD;&#x590D;&#x4EE3;&#x7801;"></a>

```cpp
struct Base{
    Base() = default;
    virtual ~Base() {};
};

struct DerivedDouble : public Base{
    DerivedDouble (double b) { std::cout << "DerivedDouble\n"; }
};

struct DerivedString : public Base{
    DerivedString (std::string data, int other) { std::cout << "DerivedString\n";}
};

template<typename T,typename...  Args>
T* Instance(Args&&... args)
{
    return new T(std::forward<Args>(args)...);
}

/*  调用方法
    Base* a = Instance<DerivedDouble>(1.2);
    Base* c = Instance<DerivedString>("hello", 3);
*/
```

#### 可变参数模板实现Tupe

```cpp
//泛化版本
template<typename ...T>
class Tuple;

//偏特化版本
template<typename HEAD, typename ...TLIST>
class Tuple<HEAD, TLIST...> : public Tuple<TLIST...>
{
public:
    Tuple(HEAD head, TLIST... args) : Tuple<TLIST...>(args...), value(head){}

    HEAD value;
};

//结束条件，特化版本
template<>
class Tuple<>{};

template<int N, typename ...T>
struct Tupleat;

//类模板偏特化
template<int N, typename T, typename ...TLIST>
struct Tupleat<N, Tuple<T, TLIST...> >
{
    static_assert(sizeof...(TLIST) >= N, "wrong index");
    typedef typename Tupleat<N - 1, Tuple<TLIST...> >::value_type value_type;
    typedef typename Tupleat<N - 1, Tuple<TLIST...> >::tuple_type tuple_type;
};

//类模板偏特化
template<typename T, typename ...TLIST>
struct Tupleat<0, Tuple<T, TLIST...> >
{
    typedef T value_type;
    typedef Tuple<T, TLIST...> tuple_type;
};

template<int N, typename HEAD, typename ...TLIST>
typename Tupleat<N, Tuple<HEAD, TLIST...> >::value_type
Get(Tuple<HEAD, TLIST...> tuple)
{
    typedef typename Tupleat<N, Tuple<HEAD, TLIST...> >::value_type VALUE;
    typedef typename Tupleat<N, Tuple<HEAD, TLIST...> >::tuple_type TUPLE;
    VALUE ret = ((TUPLE) tuple).value;
    return ret;
}

/*
        Tuple<int, double, const char *> test(12, 13.12, "hello");
        auto ntest = Get<0>(test);
        auto dtest = Get<1>(test);
        auto csztest = Get<2>(test);
*/
```

**Reference**:

1. \[C++11 模板元编程\]\([https://www.jianshu.com/p/6a9210e3dc7d](https://www.jianshu.com/p/6a9210e3dc7d)\)
2. \[可变参数详解\]\([https://blog.csdn.net/zt\_xcyk/article/details/73912606?utm\_medium=distribute.pc\_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-9.control&dist\_request\_id=&depth\_1-utm\_source=distribute.pc\_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-9.control](https://blog.csdn.net/zt_xcyk/article/details/73912606?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-9.control&dist_request_id=&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-9.control)\)

