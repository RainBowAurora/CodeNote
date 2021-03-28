---
description: 面试高频指数：★★★☆☆
---

# sizeof 和 strlen 的区别

*  `strlen` 是头文件 `<cstring>`中的函数，`sizeof` 是 C++ 中的运算符。
*  `strlen` 测量的是字符串的实际长度（其源代码如下），以 `\0` 结束。而 `sizeof` 测量的是字符数组的分配大小。

```cpp
#include <iostream>
#include <cstring>

size_t strlen(const char *str) {
    size_t length = 0;
    while (*str++)
        ++length;
    return length;
}

int main()
{
    char arr[10] = "hello";
    std::cout << strlen(arr) << std::endl; // 5
    std::cout << sizeof(arr) << std::endl; // 20
    return 0;
}
```

* 若字符数组 arr 作为函数的形参，sizeof\(arr\) 中 arr 被当作字符指针来处理，strlen\(arr\) 中 arr 依然是字符数组，从下述程序的运行结果中就可以看出。

  ```text
  #include <iostream>
  #include <cstring>

  using namespace std;

  void size_of(char arr[])
  {
      cout << sizeof(arr) << endl; // warning: 'sizeof' on array function parameter 'arr' will return size of 'char*' .
      cout << strlen(arr) << endl; 
  }

  int main()
  {
      char arr[20] = "hello";
      size_of(arr); 
      return 0;
  }
  /*
  输出结果：
  8
  5
  */
  ```

*  `strlen` 本身是库函数，因此在程序运行过程中，计算长度；而 `sizeof` 在编译时，计算长度；
*  `sizeof` 的参数可以是类型，也可以是变量；`strlen` 的参数必须是 `char*` 类型的变量。

