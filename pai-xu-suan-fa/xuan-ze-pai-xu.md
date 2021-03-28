---
description: '时间复杂度O(n^2), 不稳定'
---

# 选择排序

![&#x9009;&#x62E9;&#x6392;&#x5E8F;](../.gitbook/assets/1611226680vyvgvl-xuan-ze-pai-xu-1.gif)

选择排序的思想是：双重循环遍历数组，每经过一轮比较，找到最小元素的下标，将其交换至首位。

> 选择排序就好比第一个数字站在擂台上，大吼一声：“还有谁比我小？”。剩余数字来挨个打擂，如果出现比第一个数字小的数，则新的擂主产生。每轮打擂结束都会找出一个最小的数，将其交换至首位。经过 n-1 轮打擂，所有的数字就按照从小到大排序完成了。

```cpp
template<typename Iterator>
void SelectionSort(const Iterator& begin, const Iterator& end)
{
    //迭代器指向对象的值类型 
    using value_type = typename std::iterator_traits<Iterator>::value_type; 

    auto size=std::distance(begin, end);
    if(size <= 1) return;

    for(auto current = begin; current != end; current++){
        auto min_iter = current;
        for(auto iter = current + 1; iter != end; iter++){
            if(*iter < *min_iter) min_iter = iter;
        }
        value_type  temp_value = *min_iter;
        *min_iter = *current;
        *current = temp_value;
    }
}
```

