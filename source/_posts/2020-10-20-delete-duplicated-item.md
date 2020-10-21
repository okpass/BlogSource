---
title: 删除数组中重复元素
date: 2020-10-20 23:01:24
tags:
- C++
- 双指针
categories: 算法
image: img/category/algorithm.jpeg
keywords: [数组, 重复元素, 双指针]
description: 本文针对删除数组中重复元素的问题实现了两种复杂度不同的C++算法
---
## 1. 问题描述
删除排序数组中的重复项
给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

示例 1:

给定数组 nums = [1,1,2], 

函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 

你不需要考虑数组中超出新长度后面的元素。
示例 2:

给定 nums = [0,0,1,1,1,2,2,3,3,4],

函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。

你不需要考虑数组中超出新长度后面的元素。

说明:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:
``` cpp
// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
int len = removeDuplicates(nums);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。

for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

作者：力扣 (LeetCode)
链接：https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2gy9m/

## 2. 时间复杂度o(n^2)的方法
可以按照最傻瓜的思路来解决，就是定义两个指针，用嵌套的两个循环分别遍历整个数组，这样的话两个循环执行的数量级都是n，所以时间复杂度是o(n^2)。下面给出代码实现
``` cpp
#include<iostream>
#include<vector>
using namespace std;
int removeDuplicates(vector<int>& nums) {
    if(nums.size() == 0 || nums.size() == 1)
    {
        return nums.size();
    }
    for(vector<int>::iterator it = nums.begin(); it != nums.end(); ++it){
        vector<int>::iterator start = nums.end(), end = nums.end();
        for(auto it2 = it + 1; it2 != nums.end(); ++it2){
            if(it2 != nums.end()){
                if(*it2 == *it && start == nums.end()){
                    start = it2;
                }
                if(*it2 != *it)
                {
                    end = it2;
                    break;
                }
                if(start != nums.end() && it2 + 1 == nums.end())
                {
                    end = it2 + 1;
                    break;
                }                
            }
        }
        if(start != nums.end()){
            nums.erase(start, end);
        }
    }
    return nums.size();
}

int main()
{
    int a[10] = {0,0,1,1,1,2,2,3,3,4};
    vector<int>nums(a, a+10);
    // nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
    int len = removeDuplicates(nums);
    // 在函数里修改输入数组对于调用者是可见的。
    // 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
    for (int i = 0; i < len; i++) {
        cout << nums[i] << " "; // 0 1 2 3 4
    }
    cout << endl;
}
```

## 3. 时间复杂度o(n)的方法
注意题目描述，给定的数组已经是排好序的数组，如果第1个指针总是遍历每一项，那么如果数组中有很多相同的项，中间的遍历其实都是不需要的，我们可以找出相邻不相同的位置作为边界，直接把下一个不相同的值移动到数组前面，最后返回数组前面的所有不同项即可。可以定义两个指针指向相邻的两项，同时向后移动两个指针并对比值是否相同，若不同则把值放到数组前方当前索引处，并将当前放置位置的索引后移一位，最后将vector重置大小至放置索引即可。因为两个指针是同时移动的，只需移动n-1次即可遍历完整个数组并完成数组的更新，所以算法的时间复杂度是o(n)。这种方法称为双指针法。
``` cpp
#include<iostream>
#include<vector>
using namespace std;
int removeDuplicates(vector<int>& nums) {
    if(nums.size() == 0 || nums.size() == 1)
    {
        return nums.size();
    }

    int pre = 0, next = 1;
    int curPutPos = 1;
    while(next < nums.size())
    {
        if(nums[pre] != nums[next])
        {
            if(nums[curPutPos] != nums[next])
            {
                nums[curPutPos] = nums[next];
            }
            ++curPutPos;
        }
        ++pre;
        ++next;
    }
    nums.resize(curPutPos);
    return nums.size();
}

int main()
{
    int a[10] = {0,0,1,1,1,2,2,3,3,4};
    vector<int>nums(a, a+10);
    // nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
    int len = removeDuplicates(nums);
    // 在函数里修改输入数组对于调用者是可见的。
    // 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
    for (int i = 0; i < len; i++) {
        cout << nums[i] << " "; // 0 1 2 3 4
    }
    cout << endl;
}
```