# 二叉树

### 目录

* [镜像二叉树](jing-xiang-er-cha-shu.md)
* [树的子结构](shu-de-zi-jie-gou.md)
* [根据前序遍历和中序遍历还原二叉树](gen-ju-qian-xu-bian-li-he-zhong-xu-bian-li-hai-yuan-er-cha-shu.md)

### 树的写法

```cpp
struct TreeNode{
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x): val(x), left(nullptr), right(nullptr){}
};
```

### 树的遍历方法

* 前序遍历：根节点-&gt;左子树-&gt;右子树
* 中序遍历：左子树-&gt;根节点-&gt;右子树
* 后续遍历：左子树-&gt;右子树-&gt;根节点

```cpp
void TreeOrder(TreeNode* node)
{
    Visit(node->val); //前序遍历
    PreOrder(node->left);
    Visit(node->val); //中序遍历
    PreOrder(node->right);
    Visit(node->val); //后续遍历
}
```

