---
title: leetcode——二分查找
date: 2020-03-12 10:38:27
tags: 二分查找
---


<center>二分查找</center>
​	二分查找，思想不难，主要是弄清楚细节，比如什么时候退出，high是mid+1，还是mid，同理对于low。这里也需要注意元素是否一定在当前的查询数列内，如果元素不在这个数组内，可以在第一时间查询第一个和最后一个。而且如果这个数组可以二分，那么在一定程度上就是有序的。而左分和右分也是看条件的。一般是low=mid+1,high=mid-1，但是也有例外

​	[leetcode](https://leetcode.com/problems/first-bad-version) 里面寻找第一个毁坏的版本，如果mid调用函数得到的是true，那么说明答案在 [mid+1,high]之间，如果是false,那么说明答案在[low,mid]内。这个程序执行到最后的结果是low=high，所以while循环条件里面是low<high，最后返回的就是low。high也可以。mid=low+(high-low)/2;（避免栈溢出，high过大）

​	二分查找也可以转化为寻找左边界，右边界问题。

- 对于左边界：

```c++
if(nums[mid]<target) low=mid+1;
if(nums[mid]>=target) high=mid;
//循环条件是 while(left<right),high=nums.size(),low=0,因为每次循环的搜索区间是[low,high),终止条件是left==right
return left
```

- 右边界

```c++
int low=0,high=nums.size()-1;
while(left<right){
	int mid=low+(high-low)/2;
    if(nums[mid]<=target) low=mid+1;
    if(nums[mid]>) high=mid;
}
return high-1;
```

[参考 labuladong](https://labuladong.gitbook.io/algo/suan-fa-si-wei-xi-lie) 

再就是学习到了一个骚操作，可以leetcode的速度(对于有大量cout,cin)，很多大佬都用C++写C，但是在后面的一种方式中cin和cout的输入和输出效率比第一种低，原来而cin，cout之所以效率低，是因为先把要输出的东西存入缓冲区，再输出，导致效率降低，而ios::sync_with_stdio(false);可以来打消iostream的输入输出缓存，可以节省许多时间，使效率与scanf与printf相差无几。包含在 \#include<bits/stdc++.h> 头文件内。但是关闭流同步可能会炸空间，stdio内scanf和printf输入输出的内部同步可能取消。如果使用文件输入输出的话，一定记住要把这条语句放在freopen()后面 。

​	

