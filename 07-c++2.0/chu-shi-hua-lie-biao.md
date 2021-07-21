# 初始化列表

### 简介

c++11为了方便对类统一初始化，新增了初始化列表std::initializer\_list&lt;T&gt;特性

```cpp
std::vector<int> Func123(){
    return {123};
}

class A{
public:
    A(int){ }
private:
    A(const A&){ }
};

void main(int argc, char *argv[])
{
    A a(1); //
    A b = 123; //error 因为拷贝构造函数是私有的
    A c = {123};
    A d{123}; //c++11
    
    int e = {123};
    int f{123}; //c++11
    
    return 0;
}
```

### std::initializer\_list

可以接收任意长度参数的初始化列表

```cpp
struct CustomVec {
   std::vector<int> data;
   CustomVec(std::initializer_list<int> list) {
       for (auto iter = list.begin(); iter != list.end(); ++iter) {
           data.push_back(*iter);
      }
  }
};
```

> **注意**：std::initializer\_list，它可以接收任意长度的初始化列表，但是里面必须是相同类型T，或者都可以转换为T。

#### 始化列表的优点

1. 提供一个普适的类型初始化方式
2. 可以使用初始化列表接收任意长度参数
3. 防止类型窄化，隐式转换导致的精度丢失
   * 从浮点类型到整数类型的转换
   * 从 long double 到 double 或 float 的转换，以及从 double 到 float 的转换，除非源是常量表达式且不发生溢出
   * 从整数类型到浮点类型的转换，除非源是其值能完全存储于目标类型的常量表达式
   * 从整数或无作用域枚举类型到不能表示原类型所有值的整数类型的转换，除非源是其值能完全存储于目标类型的常量表达式

```cpp
int main() {
   int a = 1.2; // ok
   int b = {1.2}; // error

   float c = 1e70; // ok
   float d = {1e70}; // error

   float e = (unsigned long long)-1; // ok
   float f = {(unsigned long long)-1}; // error
   float g = (unsigned long long)1; // ok
   float h = {(unsigned long long)1}; // ok

   const int i = 1000;
   const int j = 2;
   char k = i; // ok
   char l = {i}; // error

   char m = j; // ok
   char m = {j}; // ok，因为是const类型，这里如果去掉const属性，也会报错
}
/*

test.cc:24:17: error: narrowing conversion of ‘1.2e+0’ from ‘double’ to ‘int’ inside { } [-Wnarrowing]
    int b = {1.2};
                ^
test.cc:27:20: error: narrowing conversion of ‘1.0000000000000001e+70’ from ‘double’ to ‘float’ inside { } [-Wnarrowing]
     float d = {1e70};

test.cc:30:38: error: narrowing conversion of ‘18446744073709551615’ from ‘long long unsigned int’ to ‘float’ inside { } [-Wnarrowing]
    float f = {(unsigned long long)-1};
                                     ^
test.cc:36:14: warning: overflow in implicit constant conversion [-Woverflow]
    char k = i;
             ^
test.cc:37:16: error: narrowing conversion of ‘1000’ from ‘int’ to ‘char’ inside { } [-Wnarrowing]
    char l = {i};
*/
```

### Reference

* \[学会C++11列表初始化\]\([https://mp.weixin.qq.com/s/wpV4K0aJS9l3ilk4nuurQA](https://mp.weixin.qq.com/s/wpV4K0aJS9l3ilk4nuurQA)\)

