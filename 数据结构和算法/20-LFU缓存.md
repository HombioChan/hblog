## 序言
在 [LRU缓存](17-LRU缓存.md) 已经实现了一个高性能的LRU缓存，本节来实现一个高性能的LFU缓存。

## 实现

### 0.定义接口
```java
public interface ICache {
    void set(int key, int value);
    int get(int key);
}
```

### 1. 链表
为每个缓存数据维护一个计数器，每次访问计数器+1，计数器越大的越靠近链表头部，计数器相同的缓存按LRU策略排序，当缓存满了之后，直接删除链表尾部的数据。

时间复杂度为
1. set: O(n)
2. get: O(n)

```java
public class SimpleLFUCache implements ICache {
    private final LinkedList<Pair> linkedList;
    private final int capacity;

    public SimpleLFUCache(int capacity) {
        this.capacity = capacity;
        this.linkedList = new LinkedList<>();
    }

    @Override
    public void set(int key, int value) {
        Pair target = find(key);
        if (target == null) {
            if (linkedList.size() == capacity) {
                linkedList.removeLast();
            }
            target = new Pair(key, value);
        } else {
            target.value = value;
            target.count += 1;
        }
        move(target);
    }

    private Pair find(int key) {
        for (Pair pair : linkedList) {
            if (pair.key == key) {
                return pair;
            }
        }
        return null;
    }

    private void move(Pair target) {
        for (int i = 0; i < linkedList.size(); i++) {
            if (linkedList.get(i).count <= target.count) {
                linkedList.remove(target);
                linkedList.add(i, target);
                return;
            }
        }
        linkedList.offerLast(target);
    }

    @Override
    public int get(int key) {
        Pair target = find(key);
        if (target == null) {
            return -1;
        }
        move(target);
        return target.value;
    }

    private static class Pair {
        int key;
        int value;
        int count;

        public Pair(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }
}
```
### 2.链表 + 哈希表
使用链表实现的LFU缓存，查找元素的时间复杂度为O(n)，查找完之后需要移动到第一个大于等于的元素前面，时间复杂度也为O(n)，通过引入两个哈希表，可以将这两个操作的时间复杂优化到O(1)。

时间复杂度分析
- set: O(1)
- get: O(1)

```java
public class FastLFUCache implements ICache{
    //优化查询操作
    private final Map<Integer, Pair> keyPairMap = new HashMap<>();
    //次数相同的元素使用一个LFU策略的链表，优化移动操作
    private final Map<Integer, LinkedList<Pair>> countLinkedListMap = new HashMap<>();
    private int capacity;

    public MoreFastLFUCache(int capacity) {
        this.capacity = capacity;
    }

    @Override
    public void set(int key, int value) {
        Pair target = keyPairMap.get(key);
        if (target == null) {
            if (keyPairMap.size() == capacity) {
                removeLFUOne();
            }
            target = new Pair(key, value);
            keyPairMap.put(key, target);
        } else {
            countLinkedListMap.computeIfAbsent(target.count, k -> new LinkedList<>()).remove(target);
            target.value = value;
            target.count += 1;
        }
        countLinkedListMap.computeIfAbsent(target.count, k -> new LinkedList<>()).offerFirst(target);
    }

    private void removeLFUOne() {
        //由于key值是自然数，根据哈希表实现原理，这里可以保证按照按照次数从低到高遍历
        for (Map.Entry<Integer, LinkedList<Pair>> entry : countLinkedListMap.entrySet()) {
            if (!entry.getValue().isEmpty()) {
                //移除掉次数使用最低，且最少使用到的元素
                Pair pair = entry.getValue().removeLast();
                keyPairMap.remove(pair.key);
                break;
            }
        }
    }

    @Override
    public int get(int key) {
        Pair target = keyPairMap.get(key);
        if (target == null) {
            return -1;
        }
        //次数新增之前，从旧的次数链表中移除，这里会遍历链表，可以继续优化
        countLinkedListMap.computeIfAbsent(target.count, k -> new LinkedList<>()).remove(target);
        target.count += 1;
        //加入新的次数链表，根据LFU策略，插入链表头部
        countLinkedListMap.computeIfAbsent(target.count, k -> new LinkedList<>()).offerFirst(target);
        return target.value;
    }

    private static class Pair {
        int key;
        int value;
        int count;

        public Pair(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

}

```
