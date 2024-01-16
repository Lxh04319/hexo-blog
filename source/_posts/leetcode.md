---
title: ALGORITHM
date: 2024-1-11
categories:
  - Learn
tags:
  - algorithm
description: algorithm is so difficult
abbrlink: 2
---

### 写在前面...

* 面向leetcode学习
* 本质上来说是第一次从头开始学算法 :-(
* 仅为了记录学习过程和各个题型之间需要注意的点
* 因此无数据结构与算法的介绍
* 全面学完了就不记录刷题了(期待着那一天...)
* 因为我真的很懒，也是个大笨蛋捏
* algorithm is so difficult...

### 数组

#### 二分查找

通常是有序数组 O(logn)

##### [704.二分查找](https://leetcode.cn/problems/binary-search/description/)

  题目描述:
  给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

  ```java
  class Solution {//采用左闭右闭区间 模板记一个就行(等于号和减一问题)
    public int search(int[] nums, int target) {
        if(target<nums[0]||target>nums[nums.length-1]){
            return -1;
        }
        int l=0,r=nums.length-1;
        int mid;
        while(l<=r){//注意<=
            mid=l+(l-r)/2;//防溢出
            if(target==nums[mid]) return mid;
            if(target<nums[mid]){
                r=mid-1;//注意-1
            }
            else {
                l=mid+1;
            }
        }
        return -1;
    }
  }
  ```

##### [35.搜索插入位置](https://leetcode.cn/problems/search-insert-position/description/)

  题目描述：
  给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。请必须使用时间复杂度为 O(log n) 的算法。

  ```java
  class Solution {//注意！！！返回left(可分为left==right和left+1==right讨论情况--->最终结果得出都是left)
    public int searchInsert(int[] nums, int target) {
        int l=0,r=nums.length-1;
        int mid;
        while(l<=r){
            mid=l+(r-l)/2;
            if(target==nums[mid]) return mid;
            if(target<nums[mid]) r=mid-1;
            else l=mid+1;
        }
        return l;
    }
  }
  ```

##### [34.在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/description/)

题目描述：
给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。
如果数组中不存在目标值 target，返回 [-1, -1]。你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题。

```java
class Solution {//分别寻找左边界和右边界 只需讨论nums[mid]==target情况即可
    public int[] searchRange(int[] nums, int target) {
        int leftb=lb(nums,target);
        int rightb=rb(nums,target);
        return new int[]{leftb,rightb};
    }
    public int lb(int[] nums,int target){
        int l=0,r=nums.length-1;
        int mid;
        while(l<=r){
            mid=l+(r-l)/2;
            if(target==nums[mid]){//在最左端或左一位小于target 说明已找到左边界
                if(mid==0||nums[mid-1]<target) return mid;
                r=mid-1;
            }
            else if(target<nums[mid]) r=mid-1;
            else l=mid+1;
        }
        return -1;
    }
    public int rb(int[] nums,int target){
        int l=0,r=nums.length-1;
        int mid;
        while(l<=r){
            mid=l+(r-l)/2;
            if(target==nums[mid]){//右边界同理
                if(mid==nums.length-1||nums[mid+1]>target) return mid;
                l=mid+1;
            }
            else if(target<nums[mid]) r=mid-1;
            else l=mid+1;
        }
        return -1;
    }
}
```

#### 双指针

##### [27.移除元素](https://leetcode.cn/problems/remove-element/description/)

题目描述：
给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。
不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。
元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

```java
class Solution {//快慢指针 快指针查找重复元素 慢指针负责指向需要覆盖的位置--->不改变顺序
    public int removeDuplicates(int[] nums) {
        int fast=0,slow=0;
        for(fast=0;fast<nums.length;fast++){
            //if(nums[fast]==nums[slow]) 
            if(nums[fast]!=nums[slow]){
                slow++;
                nums[slow]=nums[fast];
            }
        }
        return slow+1;
    }
}
```

```java
class Solution {//相向双指针 分别指向首尾 找到重复元素后覆盖--->改变顺序
    public int removeElement(int[] nums, int val) {
        int l=0,r=nums.length-1;
        while(l<=r){
            while(l<=r&&nums[l]!=val) l++;
            while(l<=r&&nums[r]!=val) r--;
            if(l<r) nums[l++]=nums[r--];
        }
        return l;
    }
}
```

##### [26.删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/description/)

题目描述：
给你一个 非严格递增排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。然后返回 nums 中唯一元素的个数。
考虑 nums 的唯一元素的数量为 k ，你需要做以下事情确保你的题解可以被通过：
更改数组 nums ，使 nums 的前 k 个元素包含唯一元素，并按照它们最初在 nums 中出现的顺序排列。nums 的其余元素与 nums 的大小不重要。
返回 k 。

```java
class Solution {//快慢指针 覆盖 注意慢指针+1(重复的下一个元素)
    public int removeDuplicates(int[] nums) {
        int fast=0,slow=0;
        for(fast=0;fast<nums.length;fast++){
            if(nums[fast]!=nums[slow]){
                slow++;
                nums[slow]=nums[fast];
            }
        }
        return slow+1;
    }
}
```

##### [283.移动零](https://leetcode.cn/problems/move-zeroes/description/)

题目描述：
给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。
请注意 ，必须在不复制数组的情况下原地对数组进行操作。

```java
class Solution {//两次遍历 第一次快慢指针 第二次把数组后面元素改为0
    public void moveZeroes(int[] nums) {
        if(nums==null) return;
        int fast=0,slow=0;
        for(fast=0;fast<nums.length;fast++){
            //if(nums[fast]==0)
            if(nums[fast]!=0){
                nums[slow++]=nums[fast];
                //nums[slow]=0;
            }
        }
        //if(slow!=nums.length-1){
          //注意此处为i=slow 不是i=slow+1 在第一次遍历中最后一次已进行slow++
            for(int i=slow;i<nums.length;i++){
                nums[i]=0;
            }
        //}
    }
}
```

##### [977.有序数组的平方](https://leetcode.cn/problems/squares-of-a-sorted-array/description/)

题目描述：
给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int l=0,r=nums.length-1;
        int p=nums.length-1;
        int[] res=new int[nums.length];
        while(l<=r){//从后往前更新答案 谁大谁上
            if(nums[l]*nums[l]>nums[r]*nums[r]){
                res[p--]=nums[l]*nums[l];
                l++;
            }
            else{
                res[p--]=nums[r]*nums[r];
                r--;
            }
        }
        return res;
    }
}
```

#### 滑动窗口

##### [209.长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)

题目描述：
给定一个含有 n 个正整数的数组和一个正整数 target 。
找出该数组中满足其总和大于等于 target 的长度最小的 连续子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。

```java
class Solution {//尽量采取while进行更新窗口范围(好理解)--时间复杂度O(n) #不要看见for嵌套while就想到O(n^2)
    public int minSubArrayLen(int target, int[] nums) {
        int res=Integer.MAX_VALUE;
        int i=0,j=0;
        int sum=0;
        for(i=0,j=0;j<nums.length;j++){
            sum+=nums[j];
            //缩小窗口
            while(sum>=target){
                res=Math.min(res,j-i+1);
                sum-=nums[i];
                i++;
            }
        }
        return res==Integer.MAX_VALUE?0:res;    
    }
}
```

##### [76.最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/description/)

题目描述：
给你一个字符串 s 、一个字符串 t 。返回 s 中涵盖 t 所有字符的最小子串。如果 s 中不存在涵盖 t 所有字符的子串，则返回空字符串 "" 。

```java
class Solution {//上一题的加强版
    public String minWindow(String s, String t) {
        //建立两个哈希表分别统计s和t包含的字符和个数
        HashMap<Character,Integer> hashs=new HashMap<>();
        HashMap<Character,Integer> hasht=new HashMap<>();
        for(int i=0;i<t.length();i++){//统计t
            hasht.put(t.charAt(i),hasht.getOrDefault(t.charAt(i),0)+1);
        }
        int l=0,r=0;
        int num=0;
        int min=Integer.MAX_VALUE;
        String ans="";
        for(;r<s.length();r++){
            //统计s
            hashs.put(s.charAt(r),hashs.getOrDefault(s.charAt(r),0)+1);
            //扩大窗口--t包含右指针指向的字符且目前数量小于t中的数量 说明需要计入
            if(hasht.containsKey(s.charAt(r))&&hashs.get(s.charAt(r))<=hasht.get(s.charAt(r))){
                num++;//用于统计已匹配的字符数是否达到t包含的字符数
            }
            //收缩窗口--t中不包含左指针指向的字符或者该字符数已大于t中该字符的数量 说明可以往右缩小窗口
            while(l<r&&(!hasht.containsKey(s.charAt(l))||hashs.get(s.charAt(l))>hasht.get(s.charAt(l)))){
                hashs.put(s.charAt(l),hashs.get(s.charAt(l))-1);
                l++;
            }
            //已匹配数等于t包含的字符数 说明该窗口满足条件
            //更新窗口最小长度和结果
            if(num==t.length()&&r-l+1<min){
                min=r-l+1;
                ans=s.substring(l,r+1);
            }
        }
        return ans;
    }
}
```

#### 模拟

##### [59.螺旋矩阵||](https://leetcode.cn/problems/spiral-matrix-ii/description/)

题目描述：
给你一个正整数 n ，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的 n x n 正方形矩阵 matrix 。

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int k=1;
        int o=0;
        int[][] res=new int[n][n];
        int len=0;
        int i;
        while(len++<n/2){
            for(i=o;i<n-len;i++) res[o][i]=k++;
            for(i=o;i<n-len;i++) res[i][n-len]=k++;    
            for(i=n-len;i>=len;i--) res[n-len][i]=k++;
            for(i=n-len;i>=len;i--) res[i][len-1]=k++;
            //len++;
            o++;
        }
        if(n%2==1){
            res[o][o]=k;
        }
        return res;
    }
}
```

##### [54.螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/description/)

题目描述：给你一个 m 行 n 列的矩阵 matrix ，请按照 顺时针螺旋顺序 ，返回矩阵中的所有元素。

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> res=new ArrayList<>();
        int i,o=0,len=0;
        int m=matrix.length;
        int n=matrix[0].length;
        int t=Math.min(m,n);
        while(len++<t/2){
            for(i=o;i<n-len;i++) res.add(matrix[o][i]);
            for(i=o;i<m-len;i++) res.add(matrix[i][n-len]);
            for(i=n-len;i>=len;i--) res.add(matrix[m-len][i]);
            for(i=m-len;i>=len;i--) res.add(matrix[i][len-1]);
            o++;
        } 
        if(t%2==1){
            if(t==m){
                for(i=o;i<=n-len;i++) res.add(matrix[o][i]);
            }
            else for(i=o;i<=m-len;i++) res.add(matrix[i][o]);
        }
        return res;
    }
}
```

#### [LCR 146.螺旋遍历二维数组](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/description/)

与上一题的区别：m.n可能为0 需添加判断

```java
if(array==null||array.length==0||array[0].length==0){
            return new int[0];
}
```

