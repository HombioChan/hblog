## 定义
二叉树中节点的左子树的所有节点的值都小于节点的值，右子树的所有节点的值都大于（等于）节点的值时，具有这个性质的的二叉树即为二叉查找树，简称BST。
## 算法
首先定义二叉查找树的节点实现
```go
type TreeNode struct {
    Val int
    Left *TreeNode
    Right *TreeNode
}
```
### 遍历
遍历在[树](https://juejin.cn/post/7086517958544982029)这一节已经实现。

> 二叉查找树的中序遍历可以得到一个有序的序列。
### 查找节点
在二叉查找树中查找值等于target的节点，思路和二分查找一样。
```go
/*
递归实现
时间复杂度：O(logn)
空间复杂度：O(logn)
*/
func find(root *TreeNode, target int) *TreeNode {
    if root == nil {
        return nil
    }
    if root.Val == target {
        return root
    } else if root.Val < target {
        return find(root.Right, target)
    } else {
        return find(root.Left, target)
    }
}
/*
非递归实现
时间复杂度：O(logn)
空间复杂度：O(1)
*/
func find(root *TreeNode, target int) *TreeNode {
    node := root
    for node != nil {
        if node.Val = target {
            return node
        } else if root.Val < target {
            node = node.Right
        } else {
            node = node.Left
        }
    }
    return nil
}
```
### 插入节点
前提：二叉树值相等的节点总在右子树。

那么相当于找到最后一个值小于等于target的节点然后插入。
```go
/*
时间复杂度：O(logn)
空间复杂度：O(1)
*/
func insert(root *TreeNode, target int) {
    node := &{target, nil, nil}
    if root == nil {
        root = node
    }
    preNode := findLastLtNode(root, target)
    if preNode.Val <= target {
        tmp := preNode.Right
        preNode.Right = node
        node.Right = tmp
    } else {
        tmp := preNode.Right
        preNode.Left = node
        node.Left = tmp
    }
}

/*
找到最后一个值小于等于target的节点
时间复杂度：O(logn)
空间复杂度：O(1)
*/
func findLastLtNode(root *TreeNode, target int) (*TreeNode) {
    var pre *TreeNode
    cur := root
    for cur != nil {
        pre = cur
        if cur.Val <= target {
            if cur.Right != nil && cur.Right.Val > target {
                return pre
            }
            cur = cur.Right
        } else {
            cur = cur.Left
        }
    }
    return pre
}
```


### 删除节点
前提：二叉树值相等的节点总在右子树。

那么相当于找到第一个值等于target的节点然后删除。
```go
/*
时间复杂度：O(logn)
空间复杂度：O(1)
*/
func insert(root *TreeNode, target int) {
    if root == nil {
        return 
    }
    pre, cur := findFirstEqNode(root, target)
    //没有找到，直接返回
    if cur == nil {
        return 
    }
    if cur.Left != nil && cur.Right != nil {
        //找到右子树的最小节点，将值和当前节点交换
        minCur = cur.Right
        minPre = minCur
        for minCur.Left != nil {
            minPre = minCur
            minCur = minCur.Left
        }
        //下面直接删除这个节点
        cur.Val = minCur.Val
        pre = minPre
        cur = minCur
    }
    var child *TreeNode
    if cur.Left != nil {
        child = cur.Left
    } else if cur.Right != nil {
        child = cur.Right
    } else {
        child = nil
    }
    if pre.Val >= cur.Val {
        //待删除的节点在双亲节点的右子树
        pre.Right = child
    } else {
        //待删除的节点在双亲节点的左子树
        pre.Left = child
    }
}

/*
找到第一个值等于target的节点
时间复杂度：O(logn)
空间复杂度：O(1)
*/
func findFirstEqNode(root *TreeNode, target int) (*TreeNode， *TreeNode) {
    var pre *TreeNode
    cur := root
    for cur != nil {
        if cur.Val == target {
            if pre != nil && pre.Val != target {
                return pre
            }
            pre, cur = cur, cur.Left
        } else if cur.Val < target {
            pre, cur = cur, cur.Right
        } else {
            pre,cur = cur, cur.Left
        }
    }
    return pre, cur
}
```

## 拓展问题
### 如何保持二叉查找树稳定的查找性能？
二叉查找树的时间复杂度和树的高度成反比，当二叉查找树的高度最低时，查询性能最好，为log(n)。
所以，如果要保持稳定的log(n)查询性能，那么在插入和删除节点后必须进行某种操作，保证树的高度最低。

这种操作称之为二叉查找树的平衡操作，本节实现的插入和删除算法并没有涉及到平衡操作，可以称这种二叉查找树为**朴素二叉查找树**，其查询性能是不稳定的。

与之相对的称为**平衡二叉查找树**，每次插入/删除节点都会进行平衡操作，所以查询性能可以稳定在log(n)，这种树的具体实现有很多，常见的有
- 红黑树：近似平衡（应用广泛）
- AVL树：绝对平衡（教材使用）
- B+树：(产用于索引)
- ...
### 既然散列表的查找性能为O(1)，为什么还会有平衡BST？
1. 设计上：散列表设计复杂，需要考虑哈希函数、哈希冲突、扩容缩容问题，而平衡BST只需考虑平衡问题
2. 整体性能稳定性方面：当出现冲突、扩容时，散列表的插入性能会退化，而平衡BST的性能总是可以稳定在O(logn)