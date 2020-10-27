---
title: 判断数组中是否存在重复元素
date: 2020-10-27 21:59:41
tags:
- 查找
- C++
- 数组
- 堆排序
categories: 算法
image: img/category/algorithm.jpeg
keywords: [数组, 重复元素, 查找]
description: 本文提出了几种判断数组中是否存在重复元素的算法，并对各种算法进行了时间、空间复杂度的分析
---
## 1. 问题描述
给定一个整数数组，判断是否存在重复元素。

如果任意一值在数组中出现至少两次，函数返回 true 。如果数组中每个元素都不相同，则返回 false 。

**示例 1:**

> 输入: [1,2,3,1]
输出: true

**示例 2:**

> 输入: [1,2,3,4]
输出: false

**示例 3:**

> 输入: [1,1,1,3,3,4,3,2,4,2]
输出: true

作者：力扣 (LeetCode)
链接：https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x248f5/

## 2. 问题分析
- 最初看到这道题的时候看这么少字，以为很简单，结果开始试了几种方法都是超时~开始肯定是国际惯例，从最简单粗暴的方式入手，要判断是否有重复元素只需要每一个元素都和其他元素比较一次就行了，于是可以用双循环的方法来搜索，发现有相同元素返回即可，但这种方法通常都只适用数据量小的情况，毕竟时间复杂度是O(n^2)呢，果不其然测试用例里有一个30万项左右的输入数组。。。于是双循环法肯定是超时了。
- 既然双循环不行，我想能不能先把数组排个序，排序之后判断相邻元素是否相同即可解决。于是先试用冒泡排序，但毕竟也是O(n^2)的算法，冒泡排序也当然超时了。
- 于是可以直接用std::sort函数进行排序，sort函数底层应该是用快速排序，平均时间复杂度为O(nlogn)，果然使用sort函数可以通过最后一个大量项数的用例了。
- 然后再试了一下最初想到的方法之一，将每个数字出现的次数用std::map计数，当出现第2次的时候即可判断重复，这种方法虽然可以通过，但无论时执行用时还是内存消耗都不理想，应该是把资源都耗费在map的建立和维持上了。
- 最后试着用堆排序来实现排序部分算法，之后再通过比较相邻元素是否相同来解决，堆排序的时间复杂度为为O(nlogn)，运行效果和使用std::sort大致一样。

## 3. 代码
```cpp
#include<iostream>
#include<vector>
#include<map>
#include<algorithm>
using namespace std;

class Solution {
public:
    // 暴力解法
    static bool containsDuplicate1(vector<int>& nums) {
        if(nums.size() <= 1)
        {
            return false;
        }
        for(int i = 0; i < nums.size(); i++)
        {
            for(int j = i + 1; j < nums.size(); j++)
            {
                if(nums[i] == nums[j])
                {
                    return true;
                }
            }
        }
        return false;
    };
    // 先冒泡排序，再判断重复
    static bool containsDuplicate2(vector<int>& nums) {
        if(nums.size() <= 1)
        {
            return false;
        }
        int len = nums.size();
        while(len > 1)
        {
            for(int i = 0; i < nums.size() - 1; ++i)
            {
                if(nums[i] == nums[i + 1])
                {
                    return true;
                }
                if(nums[i] > nums[i + 1])
                {
                    int tmp = nums[i];
                    nums[i] = nums[i + 1];
                    nums[i + 1] = tmp;
                }
            }
            --len;
        }
        
        for(int i = 0; i < nums.size() - 1; ++i)
        {
            if(nums[i] == nums[i + 1])
            {
                return true;
            }
        }

        return false;
    };
    // 使用哈希表存放每个数出现次数
    static bool containsDuplicate3(vector<int>& nums) {
        if(nums.size() <= 1)
        {
            return false;
        }
        map<int, int> a;
        for(int i = 0; i < nums.size(); i++)
        {
            a[nums[i]] = 0;
        }
        for(int i = 0; i < nums.size(); i++)
        {
            int idx = nums[i];
            ++a[idx];
            if(a[idx] > 1)
            {
                return true;
            }
        }
        return false;
    };
    // std::sort排序后比较
    static bool containsDuplicate4(vector<int>& nums) {
        if(nums.size() <= 1)
        {
            return false;
        }
        std::sort( nums.begin(), nums.end());
        for(int i = 0; i < nums.size() - 1; i++)
        {
            if(nums[i] == nums[i + 1])
            {
                return true;
            }
        }
        return false;
    }
    // 堆排序后比较
    static bool containsDuplicate5(vector<int>& nums) {
        if(nums.size() <= 1)
        {
            return false;
        }

        // 交换数组两个位置的值
        auto swap_item = [](vector<int>& nums, int pos1, int pos2)
        {
            int tmp = nums[pos1];
            nums[pos1] = nums[pos2];
            nums[pos2] = tmp;
        };

        // 从某个结点pos开始调整堆
        auto heap_sort = [=](vector<int>& nums, int pos, int len)
        {
            int dad = pos;
            int son = 2 * pos + 1;
            while(son < len)
            {
                // 找出最大子结点的值
                if(son + 1 < len && nums[son + 1] > nums[son])
                {
                    ++son;
                }
                if(nums[son] <= nums[dad])
                {
                    return;
                }
                else
                {
                    swap_item(nums, son, dad);
                    dad = son;
                    son = 2 * dad + 1;
                }
            }
        };

        // 堆排序
        auto myHeapSort = [=](vector<int>& nums)
        {
            // 先构造大顶堆
            int len = nums.size();
            for(int i = len / 2 - 1; i >= 0; --i)
            {
                heap_sort(nums, i, len);
            }
            // 先交换堆顶元素和最后一个元素，然后缩短数组，继续堆排序前面的数组
            for(int i = nums.size() - 1; i >= 0; --i)
            {
                swap_item(nums, 0, i);
                heap_sort(nums, 0, i - 1);
            }
        };
        
        myHeapSort(nums);
        for(int i = 0; i < nums.size() - 1; i++)
        {
            if(nums[i] == nums[i + 1])
            {
                return true;
            }
        }
        return false;
    };
};

int main()
{
    // int a[1] = {1};
    // vector<int> nums(a, a + 1);
    int a[10] = {1,1,1,3,3,4,3,2,4,2};
    vector<int> nums(a, a + 10);
    cout << "contains duplicate ? : " << Solution::containsDuplicate5(nums) << endl;    // 1
    for(auto it : nums)
    {
        cout << it << " ";  // 1 1 1 2 2 3 3 3 4 4
    }
    cout << endl;
}
```

堆排序在远古时期好像学过，现在重新认真学习一下，看看明天专门写一篇文章来说明堆排序的具体操作，分析一下他的时间复杂度吧。
