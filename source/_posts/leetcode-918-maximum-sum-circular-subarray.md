---
title: LeetCode 918 - Maximum Sum Circular Subarray
tags:
  - algorithm
  - leetcode
  - array
  - circular
  - prefix sum
  - deque
  - pointer
date: 2018-11-03 20:35:56
categories:
  - online judge
---

Given a circular array C of integers represented by A, find the maximum possible sum of a non-empty subarray of C.

<!-- more -->

# 题目详情

LeetCode周赛105期有这样一道题

{% blockquote LeetCode https://leetcode.com/problems/maximum-sum-circular-subarray/description/ Maximum Sum Circular Subarray %}
Given a circular array C of integers represented by A, find the maximum possible sum of a non-empty subarray of C.
{% endblockquote %}

# 解决环形类问题的两个关键  

## 直接暴力数组x2一下  

那么这道题就变成如何在长度为`2N`的数组中求子数组和最大,并且长度不超过`N`的非空子数组.

## 首尾相连与首尾不相连分开讨论  

比如说这道题, 最大子数组要么是连在一起出现在`A0`到`An-1`之间,要么是套环,从`A0`到`Ai`,然后中间断开,再从`Aj`到`An-1`相当于从`Aj`到`Ai`,那这个时候其实就是要在`A0`到`An-1`之间找一个最小子数组,这样剩下两边的数就最大了.

# 根据以上思路具体实现

## 直接暴力数组x2一下

数组有正有负意味着无法使用双指针,又要找一个数左边的最小(前缀和),那就要想到双端队列.

```python
class Solution:
    def maxSubarraySumCircular(self, A):
        """
        :type A: List[int]
        :rtype: int
        """
        if not A:
            return 0
        k = len(A)
        doublea = A + A
        ps = [0]  # prefix sum
        for num in doublea:
            ps.append(ps[-1] + num)

        maxsum = -math.inf
        ascdeq = collections.deque()  # [ (prefix sum, index of the prefix sum) ]
        for i, presum in enumerate(ps):
            # 双端队列 端口操作 while loop疯狂pop 维护端口
            while ascdeq and ascdeq[-1][0] >= presum:
                maxsum = max(maxsum, presum - ascdeq[-1][0])
                ascdeq.pop()
            # 至此 双端队列 严格递增 并且保持元素入列时间的单调性
            # 如果 双端队列 while loop疯狂popleft 维护端尾
            # 尾端元素 距离当前ps已经超过k个距离 意味着环装数组已经套圈 不可取
            while ascdeq and i - ascdeq[0][1] > k:
                ascdeq.popleft()
            # 双端队列 端尾取值操作
            if ascdeq:
                maxsum = max(maxsum, presum - ascdeq[0][0])
            # 记得将元素入列
            ascdeq.append((presum, i))
        return maxsum
```

## 首尾相连与首尾不相连分开讨论

其实上面关于收尾分开讨论的思路已经描述得很具体了,唯一需要注意的就是数组非空的要求. 当数组元素全部为负数的时候,如果再去考虑找整个数组的最小子数组,那就会找到整个数组,导致`totalsum - minsum`为0,也就是求得一个空数组,不符合题意. 所以当数组元素全部为负数的时候,我们不必再考虑首尾不相连.

```python
class Solution:
    def maxSubarraySumCircular(self, A):
        """
        :type A: List[int]
        :rtype: int
        """
        if not A:
            return 0

        totalsum = sum(A)
        maxsum1 = self.get_max_subsum(A)

        if max(A) < 0:
            return maxsum1

        # reverse sign
        for i, num in enumerate(A):
            A[i] = -num 
        
        minsum = 0 - self.get_max_subsum(A)
        maxsum2 = totalsum - minsum

        return max(maxsum1, maxsum2)

    def get_max_subsum(self, A):
        ps = [0]
        for num in A:
            ps.append( ps[-1] + num )
        
        maxsum = -math.inf
        for i, prefixsum in enumerate(ps):
            if i == 0:
                minps = prefixsum
            else:
                maxsum = max(maxsum, prefixsum - minps )
                minps = min(minps, prefixsum)
        return maxsum
```
