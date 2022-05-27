## 序言
字符串匹配功能，应该是开发工程师使用最多的功能之一，它的实现有很多，本节介绍最简单且好理解的两个实现：BF和RK算法。


## 基本概念

- 主串：被遍历匹配的字符串
- 模式串：待匹配的字符串

## BF
Brute Force，暴力匹配法，又称为朴素匹配法。

> 虽然实际复杂度大O(n*m)，实际开发中，BF是一个常用的字符串匹配算法
1. 简单(KISS)
2. 实际开发中，大部分情况下，主串和模式串都比较短。


```java

public class BF {

    public static int index(String main, String pattern) {
        for (int i=0; i<main.length(); i++) {
            int j=0;
            while (j < pattern.length()) {
                if (main.charAt(i+j) != pattern.charAt(j)) {
                    break;
                }
                j += 1;
            }
            if (j == pattern.length()) {
                return i;
            }
        }
        return -1;
    }

}

```

## RK
BF算法的升级版，BF每次检查主串和模式串是否匹配时，需要依次遍历每个字符，RK算法引入哈希算法，将复杂度从 O(m*n) 降到了 O(n);

```java
public class RK {

    private static final long[] PRE_26_POW = new long[26];

    static {
        for (int i=0; i<26; i++) {
            PRE_26_POW[i] = (long) Math.pow(26, i);
        }
    }

    public static int index(String main, String pattern) {
        int n = main.length(), m = pattern.length();
        long targetHash = hash(pattern);
        long curHash = hash(main.substring(0, m));
        if (targetHash == curHash) {
            return 0;
        }
        for(int i=1; i+m-1<n; i++) {
            curHash = (curHash - aCharHash(main.charAt(i-1), m-1)) * 26 + aCharHash(main.charAt(i+m-1), 0);
            if (curHash == targetHash) {
                return i;
            }
        }
        return -1;
    }

    private static long hash(String str) {
        int ans = 0;
        for (int i=0; i<str.length(); i++) {
            ans += aCharHash(str.charAt(i), str.length()-1-i);
        }
        return ans;
    }

    private static long aCharHash(char ch, int bitN) {
        return (ch-'a') * PRE_26_POW[bitN];
    }

}
```

### RK算法的问题

#### 1. 哈希值溢出
模式串过长，哈希值会超出long长度限制，发生溢出，导致结果错误。

只能选用另外的哈希函数来解决这个问题。

#### 2. 哈希冲突
上述RK实现使用的哈希函数没有冲突问题，但是存在溢出的风险，我们可以选用其他的哈希函数来接近溢出问题，随着而来的代价就是会发生哈希冲突。

发生哈希冲突是正常现象，只要在合理范围内，发生哈希冲突时，我们再逐个字符比价模式串和字串即可，性能不会下降很多。

如何哈希冲突太频繁，那么RK会退化成BF。