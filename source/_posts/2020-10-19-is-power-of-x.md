---
title: 判断一个数是否x的n次幂
date: 2020-10-19 14:44:43
tags:
- 幂次判断
categories: 算法
image: img/category/algorithm.jpeg
keywords: [2的n次幂, 4的n次幂, x的n次幂]
description: 本文介绍了解决判断一个数是否2，4, x的n次幂的解题方法
---

在日常的生产需求中，经常会出现需要判断一个数是否另一个数的n次幂，最常见的便是判断2的n次幂,4的n次幂，乃至x的n次幂的问题，下面我们来介绍一下这些题目的解决方法。

对于幂次的判断，我们需要用计算机的思维来解决，计算机常用的计数进制不是10进制，而是2进制，8进制，16进制等等，所以我们先从2进制的角度来思考如何解决这类问题。
## 1. 判断一个数是否2的n次幂
对于2进制数，数的每一位上的值是2的n次方，所以对于每一个2的n次幂数，他的2进制形式只有1位是1，其他位都是0，于是问题就变成了判断一个2进制数是否只有1位为1。而1只有与1相与结果才是1，所以可以通过不断判断最低位是否为1然后进行右移来计算1的个数。
``` cpp
# include <iostream>
using namespace std;

bool isPowerOfTwo(int num)
{
    if(num < 0)
    {
        return false;
    }
    if(num == 0)
    {
        return true;
    }
    int oneNum = 0; // 1的个数
    while(num > 0)
    {
        if(num & 1)
        {
            ++oneNum;
        }
        num = num >> 1;
    }
    return oneNum == 1;
}

int main()
{
    cout << isPowerOfTwo(128) << endl; // 1
    cout << isPowerOfTwo(255) << endl; // 0
}
```

## 2. 判断一个数是否4的n次幂
判断是否4的次幂可以从判断2的次幂出发，一个数如果是4的n次幂则必然也是2的次幂；其次，可以看出2进制数在偶数位上的值表示的是4的幂数，比如00010000=16=4^2，所以可以在此基础上判断是否偶数位上为1。
``` cpp
# include <iostream>
using namespace std;

bool isPowerOfFour(int num)
{
    if(num < 0)
    {
        return false;
    }
    if(num == 0)
    {
        return true;
    }
    int oneNum = 0;         // 1的个数
    int curPos = -1;        // 记录当前位置
    int pos = -1;           // 记录1所在的位置，只有符合2的次幂时pos才指向正确位置
    while(num > 0)
    {
        ++curPos;
        if(num & 1)
        {
            if(pos == -1)
            {
                pos = curPos;
            }
            ++oneNum;
        }
        num = num >> 1;
    }
    return oneNum == 1 && pos % 2 == 0;
}

int main()
{
    cout << isPowerOfFour(-1) << endl;          // 0
    cout << isPowerOfFour(128) << endl;         // 0
    cout << isPowerOfFour(64) << endl;          // 1
    cout << isPowerOfFour(64 + 8) << endl;      // 0
    cout << isPowerOfFour(64 + 4) << endl;      // 0
    cout << isPowerOfFour(16 + 1) << endl;      // 0
    cout << isPowerOfFour(16) << endl;          // 1
}
```

## 3. 判断一个数是否某个数x的n次幂的一般算法
可以发现这样的规律，若将某一个数表示成x进制，则每一位都需要x个符号来表示；若x进制的的数只有一个位上的数为1，则这个数是x的n次幂。比如三进制数100的值为1*3^2=9，即3的平方数。
所以判断一个数是否为x的n次幂，只需要将其写成x进制的形式，再判断是否只有1位为1即可。
``` cpp
# include <iostream>
#include<string>
#include<sstream>
using namespace std;

bool isPowerOfX(int base, int num)
{
    int oneNum = 0;
    int mod = 0;
    do{
        mod = num % base;
        num = num / base;
        if(mod != 1 && mod != 0)
        {
            return false;
        }
        else if(mod == 1)
        {
            ++oneNum;
        }
    }while(num > 0);
    return oneNum == 1;
}

int main()
{
    for(int i = 2; i < 10; i++)
    {
        cout << i << "的幂次数：" << endl;
        for(int j = 0; j < 1000; j++)
        {
            if(isPowerOfX(i, j))
            {
                cout << j << " ";

            }
        }
        
        cout << endl << "-----" << endl;
    }
}
```
用上述函数isPowerOfX可以判断任意正数num是否为base的n次幂，main函数输出了1000以内的2~9的所有幂次数，说明isPowerOfX解决了判断一个数是否为x的n次幂问题。
{% codeblock lang:cpp line_number:false %}
2的幂次数：
1 2 4 8 16 32 64 128 256 512 
-----
3的幂次数：
1 3 9 27 81 243 729 
-----
4的幂次数：
1 4 16 64 256 
-----
5的幂次数：
1 5 25 125 625 
-----
6的幂次数：
1 6 36 216 
-----
7的幂次数：
1 7 49 343 
-----
8的幂次数：
1 8 64 512 
-----
9的幂次数：
1 9 81 729 
-----
{% endcodeblock %}