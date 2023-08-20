---
title: "数组内查询重复(空间复杂度 O(1))"
date: 2023-08-19T23:35:29+08:00
lastmod: 2022-11-28T16:53:08+08:00
draft: false
comments: true
math: true
weight: 1
tags:
---

> 假设有一个长度为 n 的数组，所有数字都在 0~n-1 范围内。数组中某些数字是重复的，但不知道几个数字重复了，也不知道每个数字重复了几次。
> 请找出数组中任意一个重复的数字。
> 例如：一个数组 array: {2, 3, 1, 2, 4, 5}，那么对应输出是重复数字 2

<!--more-->

事实上，使用 HashMap 之类的辅助备忘来完成是很普通且常见的方式。但是如果需要原地，也就是空间复杂度也为 $O(1)$ 则需要一定的技巧。

首先，极端情况，当数组中 `所有数都不重复` 时，数组拥有的数字就是所有 0~n-1 个不同的数字。那么进行排序之后可以得到 `数字和所在下标一致` 的结论。
那么有重复数字就意味着两个或两个以上的下标上的数字是相同的。

那么结论就出来了，我们可以在排序的同时判断重复。

但是实际上我们不需要使用一个通用的排序算法。我们已经证明了所有下标和数字对应的时候是有序的，那我们实际只需要把数字交换到对应下标上就完成了排序。

具体过程就是：

* 从下标 0 对应的数字开始，首先查看它所对应的下标 array[0] 上的数字是否和它重复。若重复则直接可返回；若不重复，则将 array[0] 和 array[array[0]] 交换位置
* 此时若 array[0] != 0，则继续进行同上交换；若 array[0] == 0，则移动到下一个下标，即 1，然后仍然对 array[1] 做同上交换
* 如此反复

这里给出一份代码实现：

``` java
class Solution {
    public static int duplicationNum(int[] nums) {
        int i = 0;
        do {
            if (nums[i] == nums[nums[i]]) {// 若当前值和以当前值为索引的值相同，找到重复，返回
                return nums[i];
            } else {// 否则，交换并在索引和所在位置上的数相同时向后一位继续
                swap(i, nums[i], nums);
                if (i == nums[i]) {
                    ++i;
                }
            }
        } while (i < nums.length);
        return nums.length;// 当不存在重复时，返回数组的长度（其实返回任何一个大于等于数组长度的数都没问题）
    }

    private static void swap(int from, int to, int[] array) {
        int tmp = array[from];
        array[from] = array[to];
        array[to] = tmp;
        System.out.println(Arrays.toString(array));// 输出每次交换后的数组
    }
}
```

当然，你也可以用通用排序后查找重复的方式，基本就是两个指针顺序后移查看是否相同

``` java
public static int duplicationNum2(int[] nums) {
        Arrays.sort(nums);
        int slow = 0, fast = 1;
        do {
            if (nums[slow] == nums[fast]) {
                return nums[slow];
            }
            ++slow;
            ++fast;
        } while (fast <= nums.length);
        return nums.length;

    }
```

顺便说一句， `Arrays.sort` 底层使用的是快排、双轴快排、归并排序、插入排序和计数排序等，时间复杂度随着数组长度不同在 $O(n)$~$O(n\log{}n)$ 之间。
