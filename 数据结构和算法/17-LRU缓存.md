## 定义
一般说的缓存位于内存，用于提高程序访问数据的效率。缓存涉及的操作有
- put：数据放入缓存
- get：从缓存获取数据 

缓存的空间是有限的，当缓存空间被用满之后，需要按照某种策略替换数据，避免溢出。

常见的缓存替换策略有
- Least-Recently-Used(LRU)：替换最近最少使用的
- Least-Frequently-Used(LFU)：替换最近访问次数最少的
- FIFO
- Random
- ...

我们将使用LRU替换策略的缓存称为LRU缓存。

## LRU缓存实现

### 双向链表
使用链表来存储数据，新加入或者最近被访问的数据移动到头部，渐渐的尾部即为最近最少访问的数据，缓存满了之后，移除掉尾部的最后一个数据。
```go
type Pair stuct {
    Key int
    Value int
}

type Node struct {
    Data *Pair
    Next *Node
    Pre *Node
}

type LRUCache struct {
    Head *Node
    Tail *Node
    Capacity int
    Length int
}

func Constructor(capacity int) LRUCache {
    dummyTail := &Node{0, nil, nil}
    dummyHead := &Node{0, nil, nil}
    dummyHead.Next = dummyTail
    dummyTail.Pre = dummyHead
    return &LRUCache{dummyHead, dummyTail, capacity, 0}
}

func (this *LRUCache) Get(key int) int {
    p := this.Head.Next
    for p != this.Tail {
        if p.Data.Key == key {
            this.removeNode(p)
            this.moveToHead(p)
            return p.Data.Value
        }
        p = p.Next
    }
    return -1
}

func (this *LRUCache) removeNode(node *Node) {
    node.Pre.Next = node.Next
    node.Next.Pre = node.Pre
    node.Pre = nil
    node.Next = nil
}

func (this *LRUCache) moveToHead(node *Node) {
    node.Next = this.Head.Next
    node.Pre = this.Head
    node.Pre.Next = node
    node.Next.Pre = node
}

func (this *LRUCache) Put(key, value int) {
    p := this.Head.Next
    for p != this.Tail {
        if p.Data.Key == key {
            //存在即更新，同时移动到头部
            p.Data.Value = value;
            this.removeNode(p)
            this.moveToHead(p)
        }
        p = p.Next
    }
    if p == this.Tail {
        data := &Data{key, value}
        node := &Node{data, nil}
        this.moveToHead(node)
        this.Length += 1
        if this.Length > this.Capacity {
            //缓存已满，删除尾部数据
            this.removeNode(this.Tail.Pre)
            this.Length -= 1
        }
    }
}

```
时间复杂度分析
- 插入：O(n)
- 查询：O(n)


### [双向链表 + 哈希表](https://leetcode-cn.com/problems/lru-cache/)

使用哈希表来存储缓存数据的和链表的映射关系，可以将时间复杂度优化到O(1)
```go
type Node struct {
    Key int
    Value int
    Next *Node
    Pre *Node
}

type LRUCache struct {
    Head *Node
    Tail *Node
    Capacity int
    Length int
    HM map[int]*Node
}

func Constructor(Capacity int) LRUCache {
    dummyHead := &Node{0, 0, nil, nil}
    dummyTail := &Node{0, 0, nil, nil}
    dummyHead.Next = dummyTail
    dummyTail.Pre = dummyHead
    return LRUCache{dummyHead, dummyTail, Capacity, 0, map[int]*Node{}}
}

func (this *LRUCache) Get(key int) int {
    if node, has := this.HM[key]; has {
        this.removeNode(node)
        this.moveToHead(node)
        return node.Value
    }
    return -1
}

func (this *LRUCache) removeNode(node *Node) {
    node.Pre.Next = node.Next
    node.Next.Pre = node.Pre
    node.Next = nil
    node.Pre = nil
}

func (this *LRUCache) moveToHead(node *Node) {
    node.Next = this.Head.Next
    node.Pre = this.Head
    node.Next.Pre = node
    node.Pre.Next = node
}

func (this *LRUCache) Put(key int, value int)  {
    if node, has := this.HM[key]; has {
        node.Value = value
        this.removeNode(node)
        this.moveToHead(node)
    } else {
        node := &Node{key, value, nil, nil}
        this.HM[key] = node
        this.moveToHead(node)
        this.Length += 1
        if (this.Length > this.Capacity) {
            toRemoveNode := this.Tail.Pre
            delete(this.HM, toRemoveNode.Key)
            this.removeNode(toRemoveNode)
            this.Length -= 1
        }
    }
}

```
