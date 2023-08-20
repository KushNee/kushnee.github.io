---
title: "为什么求两数之和用哈希表比暴力法快"
date: 2023-08-19T23:39:59+08:00
lastmod: 2022-11-28T16:53:08+08:00
draft: false
comments: true
math: true
weight: 1
tags:
    - 哈希表
    - 时间复杂度
---

昨天在 Leetcode 中国上看到两数之和的[官方解法评论](https://leetcode-cn.com/problems/two-sum/solution/liang-shu-zhi-he-by-leetcode-2/)里，有人认为暴力法应该比哈希表更快。想就此讨论下。

<!--more-->

首先，暴力法的时间复杂度是 $O(n^2)$，这个大家都认可。那么使用哈希表的时间复杂度到底是多少呢？查找的时候，索引直接使用哈希值计算得出，所以大部分情况为 $O(1)$。但是还要考虑到插入时的哈希冲突。

对于哈希表，Java 在插入的时候为了避免大量冲突，会进行自动扩容，即源码中的 `resize` 方法。简单来说，当元素数达到容量的 75% 时（默认，可修改），自动扩容为原来的两倍。同时，对已存储的元素重新记算桶下标。

具体源码如下：

```java
if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
```

所以展开后，是 for 循环下套一个 for 循环。这也是不少人认为哈希表的时间复杂度为 $O(n^2)$ 的原因。但是，不要忘记每次扩容的大小为原来的两倍，而一个哈希表的默认初始容量为 16。

精密地说，暴力法的循环次数为 $\sum_{i=1}^n i$，使用等差数列求和公式得到和为 $\frac{(1+n)n}{2} = \frac{n^2+n}{2}$。去除杂项后才得到了 $O(n^2)$ 的复杂度。

而哈希表扩容时，是一个比值为 2 等比数列，为了方便计算，我们可以把首项设为 1（$2^0$）。这里面的重点是：对于相同的 n，哈希表扩容次数为 $\log_2(n)+1$，而不是 n。理由很简单，初始为 1，每次 $\times 2$，当经历了 $\log_2(n)+1$ 次扩容后，实际容量就为 $2n$ 了，存储量永远不会超过容量的 75%，就没必要继续扩容了。

因此，使用哈希表的总循环次数为 ${\sum_{i=0}^{\log_2(n)+1} 2^i}+k (k={n-{\log_2(n)-1}})$（其中 k 指不进行扩容的插入次数）。使用等比数列求和公式得到和为 $\frac{1(1-2^{\log_2(n)+1})}{1-2}+k = 2n-1+k$。去除杂项之后得到的时间复杂度为 $O(n)$。

## 总结

结论很简单，那就是使用哈希表的时间复杂度确实是 $O(n)$，但问题不是在 ContainsKey 方法大部分时间都可以直接命中，而是第一次插入时的扩容上。

这给我们的提示是：时间复杂度的估计不能只是看它出现了几个 for、while 循环，而要真正地考虑、记算它的循环次数。这也是为什么说算法的时候很多人还是会建议多一些数学思维。

