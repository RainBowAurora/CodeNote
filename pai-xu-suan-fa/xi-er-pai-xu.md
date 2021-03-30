---
description: 时间复杂度介于O(n)和O(n^2)之间
---

# 希尔排序

![](../.gitbook/assets/1611900726aynixn-xi-er-pai-xu-.gif)

希尔排序本质上是对插入排序的一种优化，它利用了插入排序的简单，又克服了插入排序每次只交换相邻两个元素的缺点。它的基本思想是：

* 将待排序数组按照一定的间隔分为多个子数组，每组分别进行插入排序。这里按照间隔分组指的不是取连续的一段数组，而是每跳跃一定间隔取一个值组成一组。
* 逐渐缩小间隔进行下一轮排序。
* 最后一轮时，取间隔为 1，也就相当于直接使用插入排序。但这时经过前面的“宏观调控”，数组已经基本有序了，所以此时的插入排序只需进行少量交换便可完成。

```cpp
template<typename Iterator,typename CompareType=std::less<typename std::iterator_traits<Iterator>::value_type>>
void ShellSort(const Iterator begin, const Iterator end, CompareType compare=CompareType())
{
    int size = std::distance(begin, end);

    for (int gap = size / 2; gap > 0; gap /= 2) { //间隔序列，在希尔排序中我们称之为增量序列
        for (int i = gap; i < size; i++) { // 从 gap 开始，按照顺序将每个元素依次向前插入自己所在的组
            int pre_index = i - gap;    // 该组前一个数字的索引
            auto key = *(begin+i);

            while(pre_index >= 0 && compare(key, *(begin+pre_index))){
                *(begin+pre_index+gap) = *(begin+pre_index); //向后挪位置
                pre_index -= gap;
            }
                *(begin+pre_index+gap) = key; //找到了自己的位置，坐下
        }
    }
}
```

