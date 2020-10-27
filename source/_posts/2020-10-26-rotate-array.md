---
title: 旋转数组
date: 2020-10-26 13:14:01
tags:
- 数组
- C++
categories: 算法
image: img/category/algorithm.jpeg
keywords: [数组, 旋转, 原地算法]
description: 本文提出了几种将数组元素向左或右旋转的算法
---
## 1. 问题描述
给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

示例 1:

> **输入**: [1,2,3,4,5,6,7] 和 k = 3
**输出**: [5,6,7,1,2,3,4]
**解释**:
向右旋转 1 步: [7,1,2,3,4,5,6]
向右旋转 2 步: [6,7,1,2,3,4,5]
向右旋转 3 步: [5,6,7,1,2,3,4]

示例 2:

> **输入**: [-1,-100,3,99] 和 k = 2
**输出**: [3,99,-1,-100]
**解释**: 
向右旋转 1 步: [99,-1,-100,3]
向右旋转 2 步: [3,99,-1,-100]

说明:

尽可能想出更多的解决方案，至少有三种不同的方法可以解决这个问题。
要求使用空间复杂度为 O(1) 的 原地 算法。

作者：力扣 (LeetCode)
链接：https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2skh7/

## 2. 问题分析
要可以向左向右旋转一个数组，因为现在数组是满了的，又要求空间复杂度为O(1)，并且是**原地**算法，所以不能另外定义一个数组空间来操作，额外使用的空间也需要是常数级别的，无论数组规模变得多大额外使用的空间大小都是固定的。

- 那么首先肯定是想最直接简单的方法来解决，要旋转一个满了的数组，至少要借助1个int变量来存储当前被移动到位置之前存放的数据，然后按顺序移动一遍整个数组即可，这是第一种解法。

- 再来分析一下数组是怎么实现旋转的。将下面的数组向右旋转3个单位，对比一下旋转前后的特征
    > 1 2 3 4 **5 6 7**     -> **5 6 7** 1 2 3 4

    我们发现旋转结果相当于把5 6 7整一块移动到了数组头部，将1 2 3 4整块移动到了后部，这跟我们把数组直接整个翻转有点相似：
    > 1 2 3 4 **5 6 7**    -> **7 6 5** 4 3 2 1

    唯一的区别就是只需再把两部分再自行翻转一次就行了，也就是
    > 7 6 5 -> 5 6 7
    > 4 3 2 1 -> 1 2 3 4

    于是，我们可以通过先整体翻转整个数组，再对两个部分分别进行一次翻转得到旋转结果。

- 上述两种做法都是通过一步步的操作来将数组元素移动到最终位置的，但其实我们可以一步到位，使每一个元素都操作一次就直接到达最终位置不是吗？如下图，只需要额外用一个int变量存储要存放位置原来的数据就可以直接把每个元素放到最终位置。另外，有些情况可能下一个要存放的位置又循环回已经移动过的位置，所以还要记录一下当前的起始点，如果又回到起始点只需要令索引+1的位置作为新起始点继续操作，直到所有元素都已经移动到最终位置即可。
  {% asset_img 1.png %}

## 3. 代码
```cpp
#include<iostream>
#include<vector>
using namespace std;

class Solution{
public:
    // 暴力解法
    static void rotate1(vector<int>& nums, int k) {
        k = k % nums.size();
        if(nums.size() <= 1)
        {
            return;
        }
        if(k <= nums.size() / 2)
        {
            for(int i = 0; i < k; i++)
            {
                nums.insert(nums.begin(), nums[nums.size() - 1]);
                nums.pop_back();
            }
        }
        else{
            for(int i = 0; i < nums.size() - k; i++){
                nums.push_back(nums[0]);
                nums.erase(nums.begin());
            }
        }
    };
    // 三次旋转
    static void rotate2(vector<int>& nums, int k) {
        k = k % nums.size();
        std::reverse(nums.begin(), nums.begin() + (nums.size() - k));
        std::reverse(nums.begin() + (nums.size() - k), nums.end());
        std::reverse(nums.begin(), nums.end());
    }
    // 一步到位
    static void rotate3(vector<int>& nums, int k) {
        if(k == 0) return;
        int len = nums.size();
        k = k % len;
        int curStartIdx = 0, preIdx = curStartIdx;
        int pre = nums[curStartIdx];
        int excCount = 0;
        do{
            bool needChange = false;
            int pickIdx = (preIdx + k) % len;
            if(pickIdx == curStartIdx)
            {
                needChange = true;
            }
            int tmp = nums[pickIdx];
            nums[pickIdx] = pre;
            pre = tmp;
            preIdx = pickIdx;
            ++excCount;
            if(needChange)
            {
                (++curStartIdx);
                curStartIdx %= len;
                preIdx = curStartIdx;
                pre = nums[curStartIdx];
                pickIdx = (preIdx + k) % len;                
            }
        }while(excCount < len);
    }
};

int main()
{
    int a[7] = {1,2,3,4,5,6,7};
    vector<int> nums(a, a + 7);

    Solution::rotate1(nums, 3);
    // Solution::rotate3(nums, 3);
    // Solution::rotate3(nums, 3);

    for(int i = 0; i < nums.size(); i++)
    {
        cout << nums[i] << " "; // 5 6 7 1 2 3 4
    }
    cout << endl;
}
```
- 暴力解法直接使用了vector的insert和erase方法，因为vector底层是数组实现，每次插入或删除元素都要将其他元素相应地后移或前移相应位置，所以两者的时间复杂度都是O(n)，再加上一层循环便是O(n^2)的复杂度，所以暴力解法并不太快，但符合要求为空间复杂度O(1)的原地算法。注意到如果向右移动的位数超过数组长度的一半的话，换成向左移动是可以操作更少步数的，虽然平均时间复杂度还是一样的数量级，但如果像将长度为10000的数组向右旋转9999位变成向左旋转1位这样的极端情况还是能很好地减少运行时间的。
- 三次旋转方法代码和思路都很简洁，vector的reverse也要移动一遍所有元素，时间复杂度为O(n)，所以三次旋转方法的时间复杂度为O(n)，性能比暴力解法要好，但相当于每个元素都操作了2次（整体翻转1次，单独翻转1次），大概要执行2n次操作。
- 最后是一步到位的方法，只需要遍历一次所有元素把他们放到最终位置即可，大概要执行n次操作，比复杂度同为O(n)的三次旋转法要快一些。



