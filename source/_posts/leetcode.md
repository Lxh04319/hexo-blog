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

### 写在前面...😒

* 面向leetcode学习
* 本质上来说是第一次从头开始学算法
* 仅为了记录学习过程和各个题型之间需要注意的点
* 因此无数据结构与算法的介绍
* 全面学完了就不记录刷题了(期待着那一天...)
* 因为我真的很懒，也是个大笨蛋捏
* algorithm is so difficult...

---

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

##### [LCR 146.螺旋遍历二维数组](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/description/)

与上一题的区别：m.n可能为0 需添加判断

```java
if(array==null||array.length==0||array[0].length==0){
            return new int[0];
}
```

---

### 链表

#### [203.移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/description/)

题目描述：
给你一个链表的头节点 head 和一个整数 val ，请你删除链表中所有满足 Node.val == val 的节点，并返回 新的头节点 。

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        //if(head==null) return head;
        while(head!=null&&head.val==val){
            head=head.next;
        }
        if(head==null) return head;
        ListNode p=head;
        ListNode t=head.next;
        while(t!=null){
            if(t.val==val){
                p.next=t.next;
            }
            else {
                p=p.next;
            }
            t=t.next;
        }
        return head;
    }
}
```

#### [206.反转链表](https://leetcode.cn/problems/reverse-linked-list/description/)

题目描述：
给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        //if(head==null) return head;
        ListNode p=null,t=head,nextt=null;
        while(t!=null){
            nextt=t.next;
            t.next=p;
            p=t;
            t=nextt;
        }
        return p;
    }
}
```

#### [92.反转链表||](https://leetcode.cn/problems/reverse-linked-list-ii/description/)

题目描述：
给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 。

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        ListNode dummyhead=new ListNode(0);
        dummyhead.next=head;
        ListNode l=dummyhead,r=head;
        //ListNode temp=null;
        for(int i=1;i<left;i++){
            l=l.next;
            r=r.next;
        }
        for(int i=1;i<=right-left;i++){
            ListNode temp=r.next;
            r.next=r.next.next;
            //l=r;
            temp.next=l.next;
            l.next=temp;
            //r=temp;
        }
        return dummyhead.next;
    }
}
```

#### [707.设计链表](https://leetcode.cn/problems/design-linked-list/description/)

题目描述：
你可以选择使用单链表或者双链表，设计并实现自己的链表。
单链表中的节点应该具备两个属性：val 和 next 。val 是当前节点的值，next 是指向下一个节点的指针/引用。
如果是双向链表，则还需要属性 prev 以指示链表中的上一个节点。假设链表中的所有节点下标从 0 开始。
实现 MyLinkedList 类：
MyLinkedList() 初始化 MyLinkedList 对象。
int get(int index) 获取链表中下标为 index 的节点的值。如果下标无效，则返回 -1 。
void addAtHead(int val) 将一个值为 val 的节点插入到链表中第一个元素之前。在插入完成后，新节点会成为链表的第一个节点。
void addAtTail(int val) 将一个值为 val 的节点追加到链表中作为链表的最后一个元素。
void addAtIndex(int index, int val) 将一个值为 val 的节点插入到链表中下标为 index 的节点之前。如果 index 等于链表的长度，那么该节点会被追加到链表的末尾。如果 index 比长度更大，该节点将 不会插入 到链表中。
void deleteAtIndex(int index) 如果下标有效，则删除链表中下标为 index 的节点。

```java
class listnode{
    int val;
    listnode next;
    listnode(){}
    listnode(int val){
        this.val=val;
    }
}
class MyLinkedList {
    listnode head;
    int size;
    public MyLinkedList() {
        size=0;
        head=new listnode(0);
    }
    
    public int get(int index) {
        if(index>=size||index<0){
            return -1;
        }
        listnode p=head;
        for(int i=0;i<=index;i++){
            p=p.next;
        }
        return p.val;
    }
    
    public void addAtHead(int val) {
        listnode add=new listnode(val);
        add.next=head.next;
        head.next=add;
        size++;
    }
    
    public void addAtTail(int val) {
        listnode temp=new listnode(val);
        listnode p=head;
        for(int i=0;i<size;i++){
            p=p.next;
        }
        p.next=temp;
        size++;
    }
    
    public void addAtIndex(int index, int val) {
        if(index>size) return;
        listnode temp=new listnode(val);
        listnode p=head;
        for(int i=0;i<index;i++){
            p=p.next;
        }
        temp.next=p.next;
        p.next=temp;
        size++;
    }
    
    public void deleteAtIndex(int index) {
        if(index>=size) return;
        listnode p=head;
        for(int i=0;i<index;i++){
            p=p.next;
        }
        p.next=p.next.next;
        size--;
    }
}
```

#### [24.两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/description/)

题目描述：
给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode dummy=new ListNode(-1);
        dummy.next=head;
        ListNode pre=dummy;
        ListNode first,second;
        while(pre.next!=null&&pre.next.next!=null){
            first=pre.next;
            second=first.next;
            pre.next=second;
            first.next=second.next;
            second.next=first;
            pre=first;
        }
        return dummy.next;
    }
}
```

#### [19.删除链表的倒数第N个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/description/)

题目描述：
给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy=new ListNode(-1);
        dummy.next=head;
        ListNode fast=dummy,slow=dummy;
        for(int i=0;i<n;i++){
            fast=fast.next;
        }
        while(fast.next!=null){
            fast=fast.next;
            slow=slow.next;
        }
        slow.next=slow.next.next;
        return dummy.next;
    }
}
```

#### [141.环形链表](https://leetcode.cn/problems/linked-list-cycle/description/)

题目描述：
给你一个链表的头节点 head ，判断链表中是否有环。
如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。注意：pos 不作为参数进行传递 。仅仅是为了标识链表的实际情况。
如果链表中存在环 ，则返回 true 。 否则，返回 false 。

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode fast=head,slow=head;
        while(fast!=null&&fast.next!=null){
            slow=slow.next;
            fast=fast.next.next;
            if(fast==slow){
                //circle
                return true;
            }
        }
        return false;
    }
}
```

#### [142.环形链表||](https://leetcode.cn/problems/linked-list-cycle-ii/description/)

题目描述：
给定一个链表的头节点  head ，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。
不允许修改 链表。

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast=head,slow=head;
        while(fast!=null&&fast.next!=null){
            slow=slow.next;
            fast=fast.next.next;
            if(slow==fast){
                //have circle
                ListNode p=head;
                while(fast!=p){
                    fast=fast.next;
                    p=p.next;
                }
                return p;
            }
        }
        return null;
    }
}
```

#### [160.相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/description/)

题目描述：
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode a=headA,b=headB;
        int lena=0,lenb=0;
        while(a!=null){
            lena++;
            a=a.next;
        }
        while(b!=null){
            lenb++;
            b=b.next;
        }
        int dif=Math.abs(lena-lenb);
        a=headA;
        b=headB;
        if(lena>lenb){
            for(int i=0;i<dif;i++) a=a.next;
        }
        else for(int i=0;i<dif;i++) b=b.next;
        while(a!=null&&b!=null){
            if(a==b) return a;
            a=a.next;
            b=b.next;
        }
        return null;
    }
}
```

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode a=headA,b=headB;
        //strange method
        while(a!=b){
            if(a==null) a=headB;
            else a=a.next;
            if(b==null) b=headA;        
            else b=b.next;
        }
        return a;
    }
}
```

### 哈希表

#### [242.有效的字母异位词](https://leetcode.cn/problems/valid-anagram/description/)

题目描述：
给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。
注意：若 s 和 t 中每个字符出现的次数都相同，则称 s 和 t 互为字母异位词。

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        int[] hash=new int[26];
        for(int i=0;i<s.length();i++){
            hash[s.charAt(i)-'a']++;
        }
        for(int i=0;i<t.length();i++){
            hash[t.charAt(i)-'a']--;
        }
        for(int num:hash){
            if(num!=0) return false;
        }
        return true;
    }
}
```

#### [383.赎金信](https://leetcode.cn/problems/ransom-note/description/)

题目描述：
给你两个字符串：ransomNote 和 magazine ，判断 ransomNote 能不能由 magazine 里面的字符构成。
如果可以，返回 true ；否则返回 false 。
magazine 中的每个字符只能在 ransomNote 中使用一次。

```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] hash=new int[26];
        for(int i=0;i<magazine.length();i++){
            hash[magazine.charAt(i)-'a']++;
        }
        for(int i=0;i<ransomNote.length();i++){
            if(hash[ransomNote.charAt(i)-'a']!=0){
                hash[ransomNote.charAt(i)-'a']--;
            }
            else return false;
        }
        return true;
    }
}
```

#### [49.字母异位词分组](https://leetcode.cn/problems/group-anagrams/description/)

题目描述：
给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。
字母异位词 是由重新排列源单词的所有字母得到的一个新单词。

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map=new HashMap<String,List<String>>();
        for(String s:strs){
            char[] chr=s.toCharArray();
            Arrays.sort(chr);
            String news=new String(chr);
            List<String> l=map.getOrDefault(news,new ArrayList());
            l.add(s);
            map.put(news,l);
        }
        return new ArrayList<List<String>>(map.values());
    }
}
```

#### [438.找到字符串中的所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/description/)

题目描述：
给定两个字符串 s 和 p，找到 s 中所有 p 的 异位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。
异位词 指由相同字母重排列形成的字符串（包括相同的字符串）。

```java
```

#### [349.两个数组的交集](https://leetcode.cn/problems/intersection-of-two-arrays/description/)

题目描述：
给定两个数组 nums1 和 nums2 ，返回 它们的交集 。输出结果中的每个元素一定是 唯一 的。我们可以 不考虑输出结果的顺序 。

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set1=new HashSet<>();
        Set<Integer> set2=new HashSet<>();
        for(int num:nums1){
            set1.add(num);
        }
        for(int num:nums2){
            if(set1.contains(num)){
                set2.add(num);
            }
        }
        int[] res=new int[set2.size()];
        int k=0;
        for(int num:set2){
            res[k++]=num;
        }
        return res;
    }
}
```