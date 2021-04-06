# 根据前序遍历和中续遍历还原二叉树

题目链接：[牛客-重建二叉树](https://www.nowcoder.com/practice/8a19cbe657394eeaac2f6ea9b0f6fcf6?tpId=117&tqId=37775&rp=1&ru=%2Fta%2Fjob-code-high&qru=%2Fta%2Fjob-code-high%2Fquestion-ranking&tab=answerKey)

### 题目描述

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

示例1：

**输入**

```text
[1,2,3,4,5,6,7],[3,2,4,1,6,5,7]
```

**返回值**

```text
{1,2,5,3,4,6,7}
```

### 解题思路

1. 根据树的特性可知，前序遍历的第一个节点一定是根节点
2. 根据根节点是1可将中序遍历划分为\[3 2 4\]\(左子树\) 、\[6 5 7\]\(右子树\)
3. 根据前序遍历的第二个节点是2可将划分为\[3\]\(左孩子\) 、\[4\]（右孩子）
4. 重复\[1-2\]步骤直到重建完成整个树

### 代码

```cpp
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

/*
          1
         / \
        2   5
       / \
      3   4
      
前序不遍历: [1 2 3 4 5]
中顺序遍历: [3 2 4 1 5]

解题思路：
    1. 根据树的特性可知，前序遍历的第一个节点一定是根节点
    2. 根据根节点可将中顺序遍历分为[3 2 4](左子树)，以及[5](右子树)
    3. 在根据前序遍历的第2个根节点的值4，重新分配左右子树 [3](左子树), [2]（右子树）
    4. 利用递归方法重复 1-2步骤直到复原整个树
*/
class Solution {
public:
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        if(pre.empty() || vin.empty()) return nullptr;
        
        std::vector<int> left_pre, left_vin, right_pre, right_vin;
        auto pose_iter = find(vin.begin(), vin.end(), pre[0]); //找到根节点的位置
        size_t dist = distance(vin.begin(), pose_iter); //得到索引
        
        //根据索引求左子树
        for(int i = 0; i < dist; i++){ 
            left_pre.push_back(pre[i+1]);
            left_vin.push_back(vin[i]);
        }
         //根据索引求右子树
        for(int i = dist+1; i < vin.size(); i++){
            right_pre.push_back(pre[i]);
            right_vin.push_back(vin[i]);
        }
        
        TreeNode* node = new TreeNode(pre[0]); //写入根节点
        node->left = reConstructBinaryTree(left_pre, left_vin); //递归重建左子树 
        node->right = reConstructBinaryTree(right_pre, right_vin); //递归重建右子树
        
        return node;
    }
};
```

