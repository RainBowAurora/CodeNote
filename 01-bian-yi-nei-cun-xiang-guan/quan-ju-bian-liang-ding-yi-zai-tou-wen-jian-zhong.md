---
description: 面试高频指数：★☆☆☆☆
---

# 全局变量定义在头文件中

 如果在头文件中定义全局变量，当该头文件被多个文件 `include` 时，该头文件中的全局变量就会被定义多次，导致重复定义，因此不能再头文件中定义全局变量。

```cpp
#ifndef __INCLUDE_TEST_H__
#define __INCLUDE_TEST_H__

int a = 10; //错误可能引发多重定义

int b; //没有问题这里只是声明变量

extern int c; //没有问题这里extern也可以看作是声明, 表明变量定义在其他源文件当中

#endif /*__INCLUDE_TEST_H__*/
```

> `#ifndef`只防止某.c重复include同一头文件 不同.c去include同一头文件是可以的；如果这个头文件里定义了全局变量，每个include该头文件的.c都会生成各自的同名全局变量，导致重复定义

