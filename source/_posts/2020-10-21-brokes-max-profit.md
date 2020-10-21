---
title: 求买卖股票的最大利润
date: 2020-10-21 14:28:12
tags:
- C++
- 贪心算法
- 动态规划
categories: 算法
image: img/category/algorithm.jpeg
keywords: [股票买卖, 贪心算法, 动态规划]
description: 当一个数组中存放了每天的股票价格，要求取买卖股票能获取的最大利润时，本文针对这个问题分别用普通算法，贪心算法，动态规划提出了三种解决方案
---

## 1. 问题描述
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

- 示例 1:
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。

- 示例 2:
输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。

- 示例 3:
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
 
- 提示：
1 <= prices.length <= 3 * 10 ^ 4
0 <= prices[i] <= 10 ^ 4

作者：力扣 (LeetCode)
链接：https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2zsx1/

## 2. 问题分析
每当遇到算法问题，我们都要有一个最基本的信念，就是就算用最笨的方法，也要先把问题解决掉先（笑）针对股票买卖问题，题目已经说明了数组的项数是万级的，对这种大量数据的处理特别要注意不能采用时间复杂度过高的算法，复杂度o(n^2)已经是不能接受的了，如果采用o(n^2)算法本题的运算量会到达万万级也就是亿级。。。如果数据再多10倍那么运算时间将会多100倍，这是不可接受的，至少也要用o(n)复杂度的算法来解决这种问题。

## 3. 普通算法
可以先采用普通的算法来实现，也就是死算。。针对每一个价格，找到下一个可以实现最大利润的价格，然后作买卖，再针对下一个价格重复执行这个步骤，最终会得出最高利润。因为只需要遍历一遍整个数组就可以完成，循环次数最多也只是n次，所以这种算法的时间复杂度为o(n)。
``` cpp
#include<vector>
using namespace std;
// 普通实现
int maxProfit(vector<int>& prices) {
    if(prices.size() == 0 || prices.size() == 1)
    {
        return 0;
    }
    int pre = 0, next = 1;
    int sum = 0;    // 总收益
    int curMax = 0; // 当前时间段最大收益
    while(next < prices.size())
    {
        int diff = prices[next] - prices[pre];
        if(diff > curMax)
        {
            curMax = diff;
            ++next;
        }
        else if(diff < curMax)   // 沽
        {
            sum += curMax;
            curMax = 0;
            pre = next;
            ++next;
        }
        else
        {
            ++next;
        }
        
        if(next == prices.size())
        {
            sum += curMax;
        }
    }
    return sum;
}

int main()
{
    int a[6] = {7,1,5,3,6,4};
    vector<int>prices(a, a+6);
    int max = maxProfit(prices);
    cout << max << endl;    // 7
}
```

## 4. 贪心算法
我们可以针对普通的算法作一点优化，现在想象我们是一个贪心的投资者，我们不想像普通算法那样，一直找到后面一个价格的极高点时再卖出，而是只着眼于眼前的利益，每当我发现后面的某一天的价格比今天的价格高就在今天买入那一天卖出，也就是一旦有利润就先赚了再说，这种方法即贪心算法。可以看到无论是贪心算法还是普通算法，得出的最大利润是一样的。
``` cpp
#include<iostream>
#include<vector>
using namespace std;

// 贪心算法实现
int maxProfit(vector<int>& prices) {
    if(prices.size() <= 1)
    {
        return 0;
    }
    int sum = 0;
    for(int i = 1; i < prices.size(); i++)
    {
        sum += max(prices[i] - prices[i - 1], 0);
    }
    return sum;
}

int main()
{
    int a[6] = {7,1,5,3,6,4};
    vector<int>prices(a, a+6);
    int max = maxProfit(prices);
    cout << max << endl;    // 7
}
```

## 5. 动态规划
题目给定了一组股票价格，要求我们给出每一天的买卖操作，并且求得这一组操作得到的最终的最大利润，这种问题属于多阶段决策问题。每一天都需要我们根据当天的股票价格和之前的买卖情况作出决策，每一天所作的决策都会影响到后面的收益，这种能够分成多个阶段而且每个阶段都要求我们根据当前的情况作出决策以求得最优解的问题，可以应用动态规划的方法来解决。

- 每一天的操作都会带来当天的收益，而每一天的操作只可能是持有或者不持有，假设我们用0表示不持有，用1表示持有，那么第i天不持有时的收益可记为dp[i][0]，第i天持有时的收益可记为dp[i][1]。
- 根据数据我们可以先得到动态规划的初始状态：
``` cpp
    dp[0][0] = 0;           // 第0天不持有时，收益为0
    dp[0][1] = -prices[0]   // 第0天持有时，要支出当天价格
```
- 然后我们便可以根据价格数据一天一天地记录下当天所有操作所产生的利润数据：
  + 第i天不持有时，若前一天持有，即第i天卖了股票，此时第i天的收益为`dp[i-1][1] + prices[i]`
  + 第i天不持有时，若前一天也不持有，那么收益和之前相等`dp[i-1][0]`
  + 第i天持有时，若前一天也持有，那么收益和之前相等`dp[i-1][1]`
  + 第i天持有时，若前一天不持有，即第i天买入了股票，此时第i天的收益为`dp[i-1][0]-prices[i]`
  然后每一天都比较前一天持有或不持有时的收益，取其较大者便是当天达到最大收益的操作
- 最后获得的最大收益，即最后一天不持有股票时的收益
``` cpp
#include<iostream>
#include<vector>
using namespace std;

// 动态规划实现
int maxProfit(vector<int>& prices) {
    if(prices.size() <= 1)
    {
        return 0;
    }
    int dp[prices.size()][2];
    dp[0][0] = 0;
    dp[0][1] = -prices[0];
    for(int i = 1; i < prices.size(); i++)
    {
        dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
        dp[i][1] = max(dp[i - 1][0] - prices[i], dp[i - 1][1]);
    }
    return dp[prices.size() - 1][0];
}

int main()
{
    int a[6] = {7,1,5,3,6,4};
    vector<int>prices(a, a+6);
    int max = maxProfit(prices);
    cout << max << endl;    // 7
}
```