---
description: 面试高频指数：★★★★☆
---

# static 的作用

### static的作用

static 定义静态变量，静态函数: 

* **保持变量内容持久**：static 作用于局部变量，改变了局部变量的生存周期，使得该变量存在于定义后直到程序运行结束的这段时间。

```text
#include <iostream>
using namespace std;

int fun(){
    static int var = 1; // var 只在第一次进入这个函数的时初始化
    var += 1;
    return var;
}
  
int main()
{
    for(int i = 0; i < 10; ++i)
    	cout << fun() << " "; // 2 3 4 5 6 7 8 9 10 11
    return 0;
}
```

* **隐藏**：static 作用于全局变量和函数，改变了全局变量和函数的作用域，使得全局变量和函数只能在定义它的文件中使用，在源文件中不具有全局可见性。（注：普通全局变和函数具有全局可见性，即其他的源文件也可以使用。）
* **共享数据**: static 作用于类的成员变量和类的成员函数，使得类变量或者类成员函数和类有关，也就是说可以不定义类的对象就可以通过类访问这些静态成员。注意：类的静态成员函数中只能访问静态成员变量或者静态成员函数，不能将静态成员函数定义成虚函数。

  ```text
  #include<iostream>
  using namespace std;

  class A
  {
  private:
      int var;
      static int s_var; // 静态成员变量
  public:
      void show()
      {
          cout << s_var++ << endl;
      }
      static void s_show()
      {
          cout << s_var << endl;
  		// cout << var << endl; // error: invalid use of member 'A::a' in static member function. 静态成员函数不能调用非静态成员变量。无法使用 this.var
          // show();  // error: cannot call member function 'void A::show()' without object. 静态成员函数不能调用非静态成员函数。无法使用 this.show()
      }
  };
  int A::s_var = 1;  // 静态成员变量在类外进行初始化赋值，默认初始化为 0

  int main()
  {
    
      // cout << A::sa << endl;    // error: 'int A::sa' is private within this context
      A ex;
      ex.show();
      A::s_show();
  }
  ```

### static的使用事项

 1. **static 静态成员变量：**

* 静态成员变量是在类内进行声明，在类外进行定义和初始化，在类外进行定义和初始化的时候不要出现 static 关键字和private、public、protected 访问规则。
*  静态成员变量相当于类域中的全局变量，被类的所有对象所共享，包括派生类的对象。 
* 静态成员变量可以作为成员函数的参数，而普通成员变量不可以。

```text
class A
{
public:
    static int s_var;
    int var;
    void fun1(int i = s_var); // 正确，静态成员变量可以作为成员函数的参数
    void fun2(int i = var);   //  error: invalid use of non-static data member 'A::var'
};

int A::s_var = 10086; //类外初始化
```

* 静态数据成员的类型可以是所属类的类型，而普通数据成员的类型只能是该类类型的指针或引用。

```text
class A
{
public:
    static A s_var; // 正确，静态数据成员
    A var;          // error: field 'var' has incomplete type 'A'
    A *p;           // 正确，指针
    A &var1;        // 正确，引用
};
```

 2. **static 静态成员函数：**

* 静态成员函数不能调用非静态成员变量或者非静态成员函数，因为静态成员函数没有 this 指针。静态成员函数做为类作用域的全局函数。 
* 静态成员函数不能声明成虚函数（`virtual`）、`const`函数和 `volatile`函数。

### 普通全局变量和静态全局变量的区别

**相同点：**

* 存储方式：普通全局变量和 `static` 全局变量都是静态存储方式。

**不同点：**

* 作用域：普通全局变量的作用域是整个源程序，当一个源程序由多个源文件组成时，普通全局变量在各个源文件中都是有效的；静态全局变量则限制了其作用域，即只在定义该变量的源文件内有效，在同一源程序的其它源文件中不能使用它。由于静态全局变量的作用域限于一个源文件内，只能为该源文件内的函数公用，因此可以避免在其他源文件中引起错误。
* 初始化：静态全局变量只初始化一次，防止在其他文件中使用。

  [  
  ](https://leetcode-cn.com/leetbook/read/cpp-interview-highlights/ef93n7/)

