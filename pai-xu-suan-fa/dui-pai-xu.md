---
description: 时间复杂度O(nlongn)
---

# 堆排序

![&#x56FE;1. &#x6700;&#x5927;&#x5806;](../.gitbook/assets/image%20%2822%29.png)

### 最大堆与最小堆

* 根节点的值 ≥ 子节点的值，这样的堆被称之为最大堆，或大顶堆；
* 根节点的值 ≤ 子节点的值，这样的堆被称之为最小堆，或小顶堆。

### 堆排序过程

* 用数列构建出一个大顶堆，取出堆顶的数字；
* 调整剩余的数字，构建出新的大顶堆，再次取出堆顶的数字；
* 循环往复，完成整个排序

### 构建大顶堆 & 调整堆 

构建大顶堆有两种方式：

1. 从 0 开始，将每个数字依次插入堆中，一边插入，一边调整堆的结构，使其满足大顶堆的要求； 
2. 将整个数列的初始状态视作一棵完全二叉树，自底向上调整树的结构，使其满足大顶堆的要求。

{% hint style="info" %}
将根节点的下标视为 0，则完全二叉树有如下性质：

* 对于完全二叉树中的第 i 个数，它的左子节点下标：left = 2i + 1
*  对于完全二叉树中的第 i 个数，它的右子节点下标：right = left + 1 
* 对于有 n 个元素的完全二叉树（n &gt;2），它的最后一个非叶子结点的下标：n/2 - 1
{% endhint %}

```cpp
template<typename Iterator>
class HeapSort{
public:
    template<typename CompareType=std::less<typename std::iterator_traits<Iterator>::value_type>>
    void operator()(const Iterator begin, const Iterator end, CompareType compare=CompareType()){
        heap_size_ = std::distance(begin, end);
        root_ = begin;
        BuildHeap(compare); //堆构建

        for(int num = heap_size_ - 1; num > 0; num--){
            std::swap(*root_, *(root_+ num)); //将堆顶交换到末尾
            Heapify(0, num, compare);  //维持堆排序
        }
    }
    
    template<typename CompareType=std::less<typename std::iterator_traits<Iterator>::value_type>>
    void BuildHeap(CompareType compare=CompareType()){
        for(int i = ((heap_size_>>1)-1); i >= 0; i--){  // (n/2 -1)
            Heapify(i, heap_size_, compare);
        }
    }

    //维护一个堆
    template<typename CompareType=std::less<typename std::iterator_traits<Iterator>::value_type>>
    void Heapify(int i, size_t size,CompareType compare=CompareType()){
        size_t max_index = i;
        size_t left = i*2+1;    //求左子树
        size_t right = left+1;  //求右子树
        
        if(left < size && compare(*(root_+max_index), *(root_+left))){
            max_index = left;
        }
        if(right < size && compare(*(root_+max_index), *(root_+right))){
            max_index = right;
        }
        if(max_index != i){
            std::swap(*(root_+i), *(root_+max_index));
            Heapify(max_index, size, compare);
        }
    }

private:
    std::size_t heap_size_ = {0};
    Iterator root_;
};
```

