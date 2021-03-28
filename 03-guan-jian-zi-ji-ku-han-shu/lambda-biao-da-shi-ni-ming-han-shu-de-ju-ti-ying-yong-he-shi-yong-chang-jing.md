---
description: 面试高频指数：★★★☆☆
---

# lambda 表达式（匿名函数）的具体应用和使用场景

 `lambda` 表达式的定义形式如下：

```cpp
[capture list] (parameter list) -> reurn type
{
   function body
}
```

> **capture list**：捕获列表，指 **lambda** 表达式所在函数中定义的局部变量的列表，通常为空，但如果函数体中用到了 **lambda** 表达式所在函数的局部变量，必须捕获该变量，即将此变量写在捕获列表中。捕获方式分为：引用捕获方式 \[&\]、值捕获方式 \[=\]。 `return type`、`parameter list`、`function body`：分别表示返回值类型、参数列表、函数体，和普通函数一样。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main()
{
    vector<int> arr = {3, 4, 76, 12, 54, 90, 34};
    sort(arr.begin(), arr.end(), [](int a, int b) { return a > b; }); // 降序排序
    for (auto a : arr)
    {
        cout << a << " ";
    }
    return 0;
}
/*
运行结果：90 76 54 34 12 4 3
*/
```



