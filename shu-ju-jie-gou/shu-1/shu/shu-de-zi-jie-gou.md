---
description: 二叉树、树
---

# 树的子结构

题目连接：[剑指Offer-树的子结构](https://www.nowcoder.com/practice/6e196c44c7004d15b1610b9afca8bd88?tpId=13&tqId=11170&rp=1&ru=%2Fta%2Fcoding-interviews&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&tab=answerKey)

### 题目描述

输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

示例1：

**输入**

```text
{8,8,#,9,#,2,#,5},{8,9,#,2}
```

**返回值**

```text
true
```



### 解题思路

判断一个树是否是另一个树的子结构:

* 两个树A B的根节点是否相同
* 如果不相同，判断A的左孩子是否与B相同，接着判断A的右孩子是否与B相同
* 按照上面两个步邹依次寻找下去

1. 如果上述情况都不满足则说明树B不是树A的子结构
2. 如果树B从头到最后一个都跟A相同则说明B是A的子结构
3. 如果查询到A的末尾也没有找到跟B完全吻合的情况则说明B不是A的子结构



### 代码

```cpp
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    bool HasSubtree(TreeNode* pRoot1, TreeNode* pRoot2) {
        if(pRoot1 == NULL|| pRoot2 == NULL ) return false;
        //树AB的根节点是否相同||A的左孩子是否跟B相同||A的右孩子是否跟B相同
        return (isSubtree(pRoot1, pRoot2)||
               HasSubtree(pRoot1->left, pRoot2)||
               HasSubtree(pRoot1->right, pRoot2));
    }
    
    //树的根节点是否相同
    bool isSubtree(TreeNode* pRootA, TreeNode* pRootB){
        if(pRootB == NULL) return true;    //树B全部跟树A重合
        if(pRootA == NULL) return false;    //树A遍历完毕也找不到跟树B完全吻合的情况
        
        if(pRootA->val == pRootB->val){ //如果当前跟节点相同则判断其两个孩子节点是否相同
        return isSubtree(pRootA->left, pRootB->left)&&
               isSubtree(pRootA->right,pRootB->right);
        }else{
            return false;
        }
    }
};
```

