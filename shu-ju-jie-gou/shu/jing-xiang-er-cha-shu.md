---
description: 简单、树
---

# 镜像二叉树

题目连接：[剑指Offer-镜像二叉树](https://www.nowcoder.com/practice/a9d0ecbacef9410ca97463e4a5c83be7?tpId=13&tqId=11171&rp=1&ru=%2Fta%2Fcoding-interviews&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&tab=answerKey)

### 题目描述

操作给定的二叉树，将其变换为源二叉树的镜像。

```text
比如：    源二叉树 
            8
           /  \
          6   10
         / \  / \
        5  7 9 11
        
        镜像二叉树
            8
           /  \
          10   6
         / \  / \
        11 9 7  5
```

示例1：

* **输入**

```text
{8,6,10,5,7,9,11}
```

* **返回值**

```text
{8,10,6,11,9,7,5}
```

### 解题思路

利用后续遍历的特性，从树的底部开始向上层层交换。

* 后续遍历找到最后一个根节点n
* 交换根节点的左右两个子树
* 回溯到根节点n的上一级根节点n-1
* 交换n-1级节点的左右两个子树
* 重复上述操作直到完成树的镜像操作

### 代码

```text
/**
 * struct TreeNode {
 *	int val;
 *	struct TreeNode *left;
 *	struct TreeNode *right;
 *	TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 * };
 */
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     * 
     * @param pRoot TreeNode类 
     * @return TreeNode类
     */
    TreeNode* Mirror(TreeNode* pRoot) {
        // write code here
       if(!pRoot) return nullptr; //递归终止条件
       
       TreeNode* left = Mirror(pRoot->left); //递归找最后一个根节点
       TreeNode* right = Mirror(pRoot->right);
       
        pRoot->left = right; //左右子树交换
        pRoot->right = left;
        return pRoot;
    }
};
```

