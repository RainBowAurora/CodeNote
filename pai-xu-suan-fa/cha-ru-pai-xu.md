---
description: 时间复杂度O(n^2)
---

# 插入排序

![&#x63D2;&#x5165;&#x6392;&#x5E8F;](../.gitbook/assets/1611807142imtwwr-cha-ru-pai-xu-1.gif)

插入排序的思想非常简单，生活中有一个非常常见的场景：在打扑克牌时，我们一边抓牌一边给扑克牌排序，每次摸一张牌，就将它插入手上已有的牌中合适的位置，逐渐完成整个排序。

```cpp
template<typename Iterator,typename CompareType=std::less<typename std::iterator_traits<Iterator>::value_type>>
void InsertSort(const Iterator begin, const Iterator end, CompareType compare = CompareType())
{
    //typedef typename std::iterator_traits<Iterator>::value_type value_type;// 迭代器指向对象的值类型
    auto size=std::distance(begin, end);
    if(size <= 1) return;

    for(auto current = begin; current != end; current++){
        auto small_iter = current;
        
        //找到要插入的位置
        while(small_iter != begin && compare(*current, *(small_iter - 1))){ 
            small_iter--;  
        }

        //插入后方的元素右移1
        auto key = *current;
        for(auto iter = current; iter != small_iter; iter--){
            *iter = *(iter - 1);
        }

        //插入元素
        *small_iter = key;
    }
}
```

> 在比较的过程中，我们将大于当前数字的元素不断向后移动。这个过程就像是已经有一些数字坐成了一排，这时一个新的数字要加入，所以这一排数字不断地向后腾出位置，当新的数字找到自己合适的位置后，就可以直接坐下了。重复此过程，直到排序结束。

