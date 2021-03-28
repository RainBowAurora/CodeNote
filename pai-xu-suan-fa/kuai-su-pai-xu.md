---
description: 时间复杂度O(nlogn)
---

# 快速排序

![&#x5FEB;&#x901F;&#x6392;&#x5E8F;](../.gitbook/assets/1611901551szcjha-kuai-su-pai-xu-1.gif)



快速排序算法由 C. A. R. Hoare 在 1960 年提出。它的时间复杂度也是 **`O(nlogn)`**，但它在时间复杂度为 **`O(nlogn)`**级的几种排序算法中，大多数情况下效率更高，所以快速排序的应用非常广泛。再加上快速排序所采用的分治思想非常实用。

快速排序算法的基本思想是：

* 从数组中取出一个数，称之为基数（pivot） 遍历数组，将比基数大的数字放到它的右边，比基数小的数字放到它的左边。
* 遍历完成后，数组被分成了左右两个区域 将左右两个区域视为两个数组，重复前两个步骤，直到排序完成。

```text
template<typename Iterator,typename CompareType=std::less<typename std::iterator_traits<Iterator>::value_type>>
Iterator partition(const Iterator begin, const Iterator end, const Iterator partition_iter, CompareType comapre=CompareType())
{
    auto size = std::distance(begin, end);
    if(size < 1) return end;

    assert(std::distance(begin,partition_iter)>=0 &&std::distance(partition_iter,end)>0);

    std::swap(*partition_iter, *(end - 1));
    auto small_next = begin;

    for(auto iter = begin; iter != end; iter++){
        if(*iter < *(end - 1)){
            std::swap(*iter, *(small_next++));
        }
    }
    
    std::swap(*small_next, *(end - 1)); //交换partition元素到它的位置
    return small_next;
}

template<typename Iterator,typename CompareType=std::less<typename std::iterator_traits<Iterator>::value_type>>
void QuickSort(const Iterator begin, const Iterator end, CompareType comapre=CompareType())
{
    auto size = std::distance(begin, end);
    if(size <=1) return;

    auto partition_iter = partition(begin, end, end-1);
    QuickSort(begin, partition_iter, comapre);
    QuickSort(partition_iter+1, end, comapre);
}
```

