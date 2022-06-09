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
基本原理：使用链表来存储数据，新加入或者最近被访问的数据移动到头部，渐渐的尾部即为最近最少访问的数据，缓存满了之后，移除掉尾部的最后一个数据。

接下来通过Java逐步实现一个高性能的LRU缓存。

### 1. 定义接口
```java
public interface ICache {
    void set(int key, int value);
    int get(int key);
}
```

### 2.简单链表实现
通过链表实现，时间复杂度如下
- set: O(n)
- get: O(n)

```java

public class SimpleLRUCache implements ICache{
    private final LinkedList<Pair> linkedList;
    private final int capacity;

    public SimpleLRUCache(int capacity) {
        this.linkedList = new LinkedList<>();
        this.capacity = capacity;
    }

    @Override
    public void set(int key, int value) {
        Pair target = find(key);
        if (Objects.isNull(target)) {
            target = new Pair(key, value);
            if (capacity == linkedList.size()) {
                linkedList.removeLast();
            }
        } else {
            target.value = value;
            linkedList.remove(target);
        }
        linkedList.offerFirst(target);
    }

    @Override
    public int get(int key) {
        Pair target = find(key);
        if (Objects.isNull(target)) {
            return -1;
        }
        linkedList.remove(target);
        linkedList.offerFirst(target);
        return target.value;
    }
    
    private Pair find(int key) {
        for (Pair pair : linkedList) {
            if (pair.key == key) {
                return pair;
            }
        }
        return null;
    }

    private static class Pair {
        int key;
        int value;

        public Pair(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }
}
```


### 3.[链表+哈希表实现](https://leetcode-cn.com/problems/lru-cache/)
使用哈希表来存储缓存数据的和链表的映射关系，可以将时间复杂度优化到O(1)

```java

public class FastLRUCache implements ICache{

    private final LinkedList<Pair> linkedList = new LinkedList<>();
    private final Map<Integer, Pair> map = new HashMap<>();
    private final int capacity;

    public FastLRUCache(int capacity) {
        this.capacity = capacity;
    }

    @Override
    public void set(int key, int value) {
        Pair pair = map.get(key);
        if (Objects.isNull(pair)) {
            if (capacity == linkedList.size()) {
                Pair toRemovePair = linkedList.removeLast();
                map.remove(toRemovePair.key);
            }
            pair = new Pair(key, value);
            map.put(key, pair);
        } else {
            pair.value = value;
            linkedList.remove(pair);
        }
        linkedList.offerFirst(pair);
    }

    @Override
    public int get(int key) {
        Pair pair = map.get(key);
        if (Objects.isNull(pair)) {
            return -1;
        }
        linkedList.remove(pair);
        linkedList.offerFirst(pair);
        return pair.value;
    }

    private static class Pair {
        int key;
        int value;

        public Pair(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }
}
```
### 进一步优化
其实，Java中有一个类 `LinkedHashMap` ，内部已经实现了以上逻辑，可以理解为，`LinkedHashMap`就是一个自动扩容的LRU缓存，我们只需稍加改造，就能快速实现一个容量受制的LRU缓存。

```java
public class BeautifulLRUCache implements ICache {
    private final Map<Integer, Integer> lruMap;
    private final int capacity;

    public BeautifulLRUCache(int capacity) {
        lruMap = new LinkedHashMap<>(2*capacity, 0.75f, true);
        this.capacity = capacity;
    }

    @Override
    public void set(int key, int value) {
        if (lruMap.size() == capacity) {
            Integer next = lruMap.keySet().iterator().next();
            lruMap.remove(next);
        }
        lruMap.put(key, value);
    }

    @Override
    public int get(int key) {
        return lruMap.getOrDefault(key, -1);
    }
}
```
