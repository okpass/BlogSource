---
title: 不用加号和减号计算两个数的加法
date: 2020-10-20 14:40:32
tags:
- C++
- 位操作
categories: 算法
image: img/category/algorithm.jpeg
keywords: [加法器, 两个数的加法]
description: 本文用C++实现了循环结构和尾递归结构的两种不使用加号、减号的两个数的加法
---
## 1. 问题描述
设计一个函数把两个数字相加。不得使用 + 或者其他算术运算符。

示例:
输入: a = 1, b = 1
输出: 2
 
提示：
a, b 均可能是负数或 0
结果不会溢出 32 位整数

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/add-without-plus-lcci

## 2. 问题分析
一般这种不准使用原先的符号来解决的问题都是考察对功能实现原理的了解，而加法在计算机中的实现其实就是数字电路课程中学习过的加法器的实现。对于数字电路，加法器是由基本的位相与、位异或组合实现的，下面来看一下一个例子

        1101
    +   0101
    =   1000

二进制数1101和0101相加，若不考虑进位，只做无进位的加法，则结果是1000
再来看看两个数按位异或的真值表

|   a   |   b   |  a^b  |
| :---: | :---: | :---: |
|   0   |   0   |   0   |
|   0   |   1   |   1   |
|   1   |   0   |   1   |
|   1   |   1   |   0   |

可以观察到，按位异或的结果与无进位加法的结果相同，实际上，加法器的无进位加法就是用位异或实现的。

实现了无进位加法，再来看进位问题如何解决，如果用0和1分别表示该位相加后无进位和有进位，那么结果如下

         1101
         0101
    进位  0101

但是进位后的值是要加到高一位上的，所以进位结果0101只要左移1位再与无进位加法的结果相加即可得到最终的加法结果。那么怎么得出进位这个数呢？再来看看两个数按位相与的真值表

|   a   |   b   |  a&b  |
| :---: | :---: | :---: |
|   0   |   0   |   0   |
|   0   |   1   |   0   |
|   1   |   0   |   0   |
|   1   |   1   |   1   |

可以发现，按位相与的结果就是进位的值0101，于是我们可以用位相与操作来得出进位值。
下面来介绍两种实现方法。

## 3. 循环结构实现
``` cpp 
# include <iostream>
using namespace std;
int add(int a, int b) {
    while(b != 0)
    {
        unsigned int jinwei = (unsigned int)(a & b) << 1;
        a = a ^ b;
        b = jinwei;
    }
    return a;
}

int main()
{
    cout << add(-1, 2) << endl; // 1
}
```
在计算位相与的时候要转化为无符号整形数，否则在某些编译器上可能禁止左移一个负数。
在第一次循环时，先得出进位的值，然后将初始的两个数ab按位异或实现相加，将jinwei赋给b后，第二次循环开始就是计算进位值和总和的相加。由于加上进位值之后还有可能出现进位，于是当进位值为0之后即计算完毕。

## 4. 递归结构实现
``` cpp
# include <iostream>
using namespace std;
int add(int a, int b) {
    auto jinwei = (unsigned int)(a & b) << 1;
    int sum = (int)(a ^ b);
    if(jinwei == 0)
    {
        return sum;
    }
    return add(sum, jinwei);
}

int main()
{
    cout << add(-1, 2) << endl;
}
```
通过上面分析可以知道，在计算出进位值之后也是要加到总和上的，等于不断调用加法，于是可以考虑用尾递归来实现。代码中将sum和jinwei不断代入add函数，直到进位值为0即计算完毕。