# auto和decltype类型自动推导

auto和decltype是c++11提出的新特性，作用是可以在**编译**时期就推导出变量或者表达式的类型，精简代码同时增加了更加易用性。

### auto

auto可以将类型推导的工作从开发者交给编译器

```cpp
auto a = 10; // 10是int所以 推导出的a的类型是int

auto b = 2.0; // 推导出b的类型是double

int i = 10;
auto c = i; // 因为i的类型是int 所以推导出c的类型是int
```

#### auto 推导规则

* 变量类型推导

```cpp
int i = 10;
auto a = i, &b = i, *c = &i; // i的类型是int, b是i引用, c是指针指向i的地址, 推导出a的类型是int
auto d = 0, f = 1.0; //[错误用法]d类型是int, f类型是double, 对编译器造成了二义性，导致无法推导
auto e; //[错误用法]引用在定义时必须初始化
```

* 类成员变量

```cpp
class A{
    auto a = 1; //[错误用法] auto不能用做非静态成员变量
    static auto b = 1; //[错误用法]这里与auto无关，正常情况下静态变量也不能直接赋值
    static const c = 1; //ok
};
```

* 函数

```cpp
void func1(auto value){} //[错误用法] auto不能用作函数参数

void func2(){
    int a[10] = {};
    auto b = a; //ok
    auto c[10] = a;//[错误用法]auto可以定义指针但不能定义数组
    std::vector<int> d;
    std::vector<auto> e= d;//[错误用法]auto不能推导模板参数
}
```

* cv\(`const`、`volatile`\)情况下的推导

```cpp
auto *d = &i; // d是 int*
auto &e = i; // e是 int&
auto f = e; // f是 int忽略了引用

const auto g = i; //d是const int
auto h = g; //h是int

const auto &m = h; //f是const int&
auto &n = m; //n是 const int&
```

> auto的限制
>
> * auto的使用必须马上初始化，否则无法推导出类型
> * auto在定义一行定义多个变量，各个变量的推导不能产生二义性，否则不能通过编译
> * auto不能用作函数参数
> * auto不能作为类的非静态成员变量
> * auto可以定义指针但不能定义数组
> * auto无法推导出模板参数
> * 在不声明为引用或指针时，auto会忽略等号右边的引用类型和cv属性
> * 在声明为引用和指针时，auto会保留等号右边的应用类型和cv属性

### decltype

上面介绍的auto 用于变量的推导，decltype可以用来推导表达式

```cpp
int func() { return 0; }
decltype(func()) i; //因为函数func()的返回值是int 所以推导出i的类型是int

int x = 0;
decltype(x) y; //y 的类型是int
decltype(x+y) z; //z的类型是int
```

> **注意**: decltype不会跟auto一样忽略引用和cv属性，他会保留表达式的引用和cv属性
>
> ```cpp
> const int &i = 1;
> decltype(i) b = 2; // b是 const int&
> ```

#### decltype的推导规则

对于decltype\(exp\)有:

* exp是表达式，decltype\(exp\)和exp的类型相同
* exp时函数调用，decltype\(exp\)和exp所代表的函数的返回值类型相同
* 其他情况，如果exp是左值，decltype\(exp\)是exp类型的左值引用

```cpp
int a = 0, b = 0;
decltype(a+b) c = 0;// c是int, 因为(a+b)返回的是一个右值
decltype(a+=b) d = c; //d是int&, 因为(a+=b)返回的是一个左值
```

### auto和decltype配合使用

* 常见的案例是推导函数返回值的类型

```cpp
template<typename T, typename U>
return_value add(T t, U u) { // t和v类型不确定，无法推导出return_value类型
    return t + u;
}
```

* 上面的代码由于T和U类型不能确定，所以不能知道返回值的类型，可以使用decltype推导

```cpp
template<typename T, typename U>
decltype(t + u) add(T t, U u) { // t和u尚未定义
    return t + u;
}
```

* 但是这样又引出另一个问题，在decltype\(t+u\)进行推导的时候，t与u还未定义，所以有了下的最终解决方案

```cpp
template<typename T, typename U>
auto add(T t, U u) -> decltype(t + u) 
    return t + u;
}
```

### Reference

1. \[一文吃透C++11中auto和decltype知识点\]\([https://mp.weixin.qq.com/s/3BQ2JlVQsE0sm6eDNa5AdA](https://mp.weixin.qq.com/s/3BQ2JlVQsE0sm6eDNa5AdA)\)
2. \[深入应用C++11代码优化与工程级应用\]\([https://blog.csdn.net/y1196645376/article/details/51441503](https://blog.csdn.net/y1196645376/article/details/51441503)\)

