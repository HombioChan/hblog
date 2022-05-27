## 序言
字符串匹配算法中，据统计，最常见和高效的是`BM`，但是最出名的却是`KMP`。

### 基本思想
和`BM`一样，`KMP`算法的基本思想是当匹配过程中遇到坏字符时，向右滑动尽可能多的字符。

> 差别在于，BM从右到左遍历模式串，KMP从左到右遍历模式串

## 基本概念
### 好前缀
模式串中在匹配到坏字符之前已经匹配的前缀字符串。

如下，此时的好前缀就是 `asdf`
```
asdfsasdfadfsdf
asdfa
```
### 前缀子串
字符串从左到右，所有可能的子字符串。
```
字符串：asdf
// asdf 的前缀字串有下面四个
a
as
asd
asdf
```
### 后缀子串
与前缀子串相反。
```
字符串：asdf
// asdf 的后缀字串有下面四个
f
df
sdf
asdf
```

### 最长可匹配前缀子串/后缀子串
在一个字符串中，在后缀子串中最长的，且出现在前缀字串中的子串。

- 在后缀子串中，称为最长可匹配后缀子串
- 在前缀字串中，称为最长可匹配前缀子串

比如下面的例子，最长可匹配前缀子串和最长可匹配后缀子串为 `aba`
```
ababfaba
```

## 实现
### 定义失效函数
实现KMP之前，需要先定义好一个数组，用来记录模式串中每个好前缀的最长可匹配前缀子串结束字符的下标，我们把这个数组定义为`next数组`，即`失效函数`。

> next[i] = k

- i: 模式串的下标，0<=i<len(模式串)-1
- k：最长可匹配前缀子串最后一个字符在模式串中的下标，-1表示不存在

### 框架代码
定义好失效函数之后，其实就可以很容易的写出KMP算法的框架代码。
```java
public class KMP {

    public static int index(String main, String pattern) {
        int n = main.length(), m = pattern.length();
        int[] next = getNext(pattern);
        int j = 0;
        for (int i=0; i<n; i++) {
            //从最大子串向最小子串遍历，知道找到一个符合要求的最小匹配前缀子串
            while (j > 0 && main.charAt(i) != pattern.charAt(j)) {
                j = next[j-1] + 1;
            }
            //字符匹配相同，向后移动指针
            if (pattern.charAt(j) == main.charAt(i)) {
                j += 1;
            }
            //找到匹配模式串
            if (j == m) {
                return i-m+1;
            }
        }
        return -1;
    }
    
}
```

### 实现失效函数
```java
public class KMP {

    private static int[] getNext(String pattern) {
        int m = pattern.length();
        int[] next = new int[m];
        next[0] = -1;
        int k = -1;
        for (int i = 1; i < m; ++i) {
            while (k != -1 && pattern.charAt(k+1) != pattern.charAt(i)) {
                k = next[k];
            }
            if (pattern.charAt(k+1) != pattern.charAt(i)) {
                ++k;
            }
            next[i] = k;
        }
        return next;
    }
    
}
    
```

## 复杂度分析
KMP的时间复杂度为 O(n+m)

- getNext: O(m)
- 匹配: O(n)
