## 序言
Trie树，也叫“字典树”。顾名思义，它是一个树形结构，是一种专门处理字符串匹配的数据结构，用来解决在一组字符串集合中快速查找某个字符串的问题。

### 实现
假设字符集是26个小写英文字符。

```java

public class LowercaseWordTireTree {

    private TrieNode root;

    public LowercaseWordTireTree() {
        root = new TrieNode('/');
    }

    public void add(String word) {
        char[] chars = word.toCharArray();
        TrieNode p = root;
        for (char aChar : chars) {
            int index = aChar - 'a';
            if (p.children[index] == null) {
                p.children[index] = new TrieNode(aChar);
            }
            p = p.children[index];
        }
        p.endingChar = true;
    }

    public boolean contains(String world) {
        char[] chars = world.toCharArray();
        TrieNode p = root;
        for (char aChar : chars) {
            int index = aChar - 'a';
            if (p.children[index] == null) {
                return false;
            }
            p = p.children[index];
        }
        return p.endingChar;
    }

    static class TrieNode {
        char data;
        TrieNode[] children;
        boolean endingChar;

        public TrieNode(char data) {
            this.data = data;
            this.children = new TrieNode[26];
        }
    }

}
```
#### 时间复杂度分析

- 构建：O(n), n表示所有字符串的长度
- 查找：O(k), k表示要查找的字符串的长度

### 优点
查询速度快

### 缺点
1. 字符集不能太大，否则很耗空间
2. 要求前缀重合要尽可能多，否则很耗空间
3. 指针对缓存不友好，性能上会打折扣

> 综合这几点，针对在一组字符串中查找某个字符串的问题，在工程中，一般使用哈希表或红黑树。


### 主要应用
####1.自动输入补全，比如
- 输入法自动补全
- IDE代码自动补全
- 浏览器搜索关键词自动补全

####2.敏感词过滤
多个模式串，在一个主串上匹配，只需遍历一次，效率高。

### 与其他字符串匹配算法的比较
Trie树是多模式串匹配算法，`BF/RK/BM/KMP`是单模式串匹配算法。