## 序言
有一千万个整数，范围在1到1亿之间，如何快速地查找某个整数在这一千万个整数当中。

我们可以快速的想到使用哈希表，假设一个整数占4个字节，不考虑哈希冲突，那么一千万个整数大概会占用40M的内存空间。

那么有没有一种数据结构能够占用更少的内存空间来满足这个需求呢，答案就是位图，bitmap。

### 位图设计
一个整数是否存在只有两种可能，所以可以用0和1来表示，1bit就可以满足。

按此设计，1亿范围的数据会用到1,0000,0000 bit，约等于12M，相对于使用哈希表实现，节省了3/4的内存。

### 位图实现
```java
public class BitMap {
    
    private byte[] bytes;
    private int nBits;

    public BitMap(int nBits) {
        this.nBits = nBits;
        this.bytes = new byte[nBits/8+1];
    }

    public void add(int number) {
        if (number > nBits) {
            return;
        }
        int byteIndex = number / 8;
        int bitIndex = number % 8;
        bytes[byteIndex] |= (1<<bitIndex);
    }

    public boolean contains(int number) {
        if (number > nBits) {
            return false;
        }
        int byteIndex = number / 8;
        int bitIndex = number % 8;
        return (bytes[byteIndex] & (1<<bitIndex)) != 0;
    }
}
```

### 使用场景
数据量和数据范围相差不大的判重场景。

> 如果数据量小，数据范围大，那么使用位图不能达到节省空间的作用。
