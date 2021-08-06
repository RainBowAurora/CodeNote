# 堆

### 堆的定义与分类

堆是一种特殊的二叉树，满足以下条件的二叉树可被称之为堆：

1. 完全二叉树
2. 每一个根节点的值大于等于或者小于等于其孩子节点的值

堆具有以下特点：

1. 可以在O\(logN\)的时间复杂度内向堆中插入元素
2. 可以在O\(logN\)的时间复杂度内删除堆中元素
3. 可以在O\(1\)的时间复杂度内取得堆中最大值或者最小值\(即堆顶值\)

堆的分类：

* **大顶堆**：堆中每一个节点的值都大于等于其孩子节点的值。所以大顶堆的特性是堆顶元素是堆中最大值。
* **小顶堆**：堆中每一个节点的值都小于等于其孩子节点的值。所以小顶堆的特性是堆顶元素是堆中最小值。

![&#x56FE;1&#x5C0F;&#x9876;&#x5806;&#x548C;&#x5927;&#x9876;&#x5806;](../../../.gitbook/assets/image%20%2826%29.png)
