# 常用算法

## ACM输入格式

通常我们使用的是Scanner sc = new Scanner(System.in);但是有的时候会通不过测试用例。建议使用

```java
BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(System.out));
reader.readLine();//读取一行数据
//输出的话：
writer.write(Integer.toString(r));
                writer.newLine();
这样输出即可。
    当然最后不要忘记
    writer.flush();
```

## 动态规划

### 最长公共子序列

给定两个字符串，返回这两个字符串的公共子序列的长度，如果不存在，返回0；

```
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace" ，它的长度为 3 。
```

```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length();
        int n = text2.length();
        int[][] dp = new int[m+1][n+1];
        for(int i = 1;i<=m;i++){
            for(int j = 1;j<=n;j++){
                if(text1.charAt(i-1) == text2.charAt(j-1)){
                    dp[i][j] = dp[i-1][j-1]+1;
                }else{
                    dp[i][j] = Math.max(dp[i][j-1], dp[i-1][j]);
                }
            }
        }
        return dp[m][n];
    }
}
```

变形题：找到之后返回子串。

```java
import java.util.*;


public class Solution {
    /**
     * longest common substring
     * @param str1 string字符串 the string
     * @param str2 string字符串 the string
     * @return string字符串
     */
    public String LCS (String str1, String str2) {
        // write code here
        //记录最长公共子串的长度
        int maxLength = 0;
        //记录最长公共子串最后一个元素在str1中的位置
        int maxLastIndex  = 0;
        int[][] dp = new int[str1.length()+1][str2.length()+1];
        for(int i = 0;i<str1.length();i++){
            for(int j = 0;j<str2.length();j++){
                if(str1.charAt(i) == str2.charAt(j)){
                    //如果两个字符相等的话
                    dp[i+1][j+1] = dp[i][j] + 1;
                    //如果遇到了更长的子串，更新最长子串的长度,以及最后子串的最后一个位置
                    if(dp[i+1][j+1] > maxLength){
                        maxLength = dp[i+1][j+1];
                        maxLastIndex  = i;
                    }
                }else{
                    dp[i+1][j+1] = 0;
                }
            }
        }
        return str1.substring(maxLastIndex  - maxLength +1, maxLastIndex + 1);
    }
}
```

薪资大于x并且所在city的平均薪资大于y的员工姓名

```sql
select name from T
where salary>x
and city in(
   select city from T
   group by city
   having avg(salary)>y
)
```

- 讲讲[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)，为什么要有[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)，跟avl相比好在哪，它的特点？

### [91. 解码方法](https://leetcode-cn.com/problems/decode-ways/)

```java
一条包含字母 A-Z 的消息通过以下映射进行了 编码 ：
'A' -> 1
'B' -> 2
...
'Z' -> 26
要 解码 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，"11106" 可以映射为：
"AAJF" ，将消息分组为 (1 1 10 6)
"KJF" ，将消息分组为 (11 10 6)
注意，消息不能分组为  (1 11 06) ，因为 "06" 不能映射为 "F" ，这是由于 "6" 和 "06" 在映射中并不等价。
给你一个只含数字的 非空 字符串 s ，请计算并返回 解码 方法的 总数 。
题目数据保证答案肯定是一个 32 位 的整数。
```

主要思路：

```
对于给定的的字符串s，可以表示为s[1],s[2],s[3],...s[n].那么具体地，我们可以定义dp：dp[i]表示字符串s的前i个字符s[1-i]的解码方法数。
选择：选择一个字符，或者选择两个字符。
动态转移方程：
	1. 当选择了一个字符的时候，只要这个字符不是0的话，那么就能解码。
	2. 当选择了两个字符的时候，只要s[i]和s[i-1]小于等于26的时候，可以解码。
	所以动态转移方程为：
	dp[i] = dp[i] + dp[i-1];//选择一个字符，前1至i-1已经可以解码
	dp[i] = dp[i] + dp[i-2];//选择两个字符的时候。
```

```java
  public int numDecoding(String s) {
 int n = s.length();
        int[] f = new int[n + 1];
        f[0] = 1;
        for (int i = 1; i <= n; ++i) {
            if (s.charAt(i - 1) != '0') {
                f[i] += f[i - 1];
            }
            if (i > 1 && s.charAt(i - 2) != '0' && ((s.charAt(i - 2) - '0') * 10 + (s.charAt(i - 1) - '0') <= 26)) {
                f[i] += f[i - 2];
            }
        }
        return f[n];
    }
```



## 回溯

### 全排列

```java
 private List<List<Integer>> res = new LinkedList<>();
    public List<List<Integer>> permute(int[] nums) {
        LinkedList<Integer> trace = new LinkedList<>();
        backtrace(nums, trace);
        return res;
    }
    public void backtrace(int[] nums, LinkedList<Integer> trace){
        //触发结束条件
        if (trace.size()==nums.length){
            res.add(new LinkedList<>(trace));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            //排除不合法的选择
            if (trace.contains(nums[i])){
                continue;
            }
            //做选择
            trace.add(nums[i]);
            //进入下一层递归树中
            backtrace(nums, trace);
            //取消选择
            trace.removeLast();
        }
    }
```

### 全排列II

包含重复的全排列，需要使用一个visit数组表示是否访问过这一元素。

先排序，然后再回溯，回溯过程中判断重复访问的条件：`!visit[i] || (i>0 && nums[i-1]==nums[i] && !visit[i])`。

```java
 public List<List<Integer>> permuteUnique(int[] nums){
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> temp = new ArrayList<>();
        boolean[] visit = new boolean[nums.length];
        Arrays.sort(nums);
        backtrack(nums, ans, 0, temp, visit);
        return ans;
    }

    private void backtrack(int[] nums, List<List<Integer>> ans, int idx, List<Integer> temp, boolean[] visit) {
        if (idx == nums.length){
            ans.add(new ArrayList<>(temp));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (visit[i] || (i>0 && nums[i] == nums[i-1] && !visit[i-1])){
                continue;
            }
            temp.add(nums[i]);
            visit[i] = true;
            backtrack(nums, ans, idx+1, temp, visit);
            visit[i] = false;
            temp.remove(idx);
        }
    }

```

### [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

```
给定一个无重复元素的正整数数组 candidates 和一个正整数 target ，找出 candidates 中所有可以使数字和为目标数 target 的唯一组合。
candidates 中的数字可以无限制重复被选取。如果至少一个所选数字数量不同，则两种组合是唯一的。 
对于给定的输入，保证和为 target 的唯一组合数少于 150 个。
示例 1：
输入: candidates = [2,3,6,7], target = 7
输出: [[7],[2,2,3]]
示例 2：
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
示例 3：
输入: candidates = [2], target = 1
输出: []
示例 4：
输入: candidates = [1], target = 1
输出: [[1]]
```

```java
class Solution {
   public List<List<Integer>> combinationSum(int[] candidates, int target){
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> combine = new ArrayList<>();
        dfs(candidates, target, ans, combine, 0);
        return ans;
    }

    public void dfs(int[] candidates, int target, List<List<Integer>> ans, List<Integer> combine, int idx){
        if (idx == candidates.length){
            return;
        }
        if (target == 0){
            ans.add(new ArrayList<>(combine));
            return;
        }
        //跳过
        dfs(candidates, target, ans, combine, idx+1);
        //选择当前数字
        if (target-candidates[idx] >= 0){
            combine.add(candidates[idx]);
            dfs(candidates, target-candidates[idx], ans, combine, idx);
            combine.remove(combine.size()-1);
        }
    }
}
```

----

以下为岛屿问题：

岛屿问题的DFS遍历的框架代码：

```java
void dfs(int[][] grid, int r, int c) {
    // 判断 base case
    if (!inArea(grid, r, c)) {
        return;
    }
    // 如果这个格子不是岛屿，直接返回
    if (grid[r][c] != 1) {
        return;
    }
    grid[r][c] = 2; // 将格子标记为「已遍历过」
    
    // 访问上、下、左、右四个相邻结点
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}

// 判断坐标 (r, c) 是否在网格中
boolean inArea(int[][] grid, int r, int c) {
    return 0 <= r && r < grid.length 
        	&& 0 <= c && c < grid[0].length;
}
```

### [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

DFS

```java
给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。
岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。
此外，你可以假设该网格的四条边均被水包围。
示例 1：
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
示例 2：
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

```java
public int numIslands(char[][] grid) {
        int res = 0;
        for (int r = 0; r < grid.length; r++) {
            for (int c = 0; c < grid[0].length; c++) {
                if (grid[r][c] == '1') {
                    res++;
                    dfs(grid, r, c);
                }
            }
        }
        return res;
    }

    void dfs(char[][] grid, int r, int c) {
        // 判断 base case
        if (!inArea(grid, r, c)) {
            return;
        }
        // 如果这个格子不是岛屿，直接返回
        if (grid[r][c] - '0' != 1) {
            return;
        }
        // 将格子标记为「已遍历过」
        grid[r][c] = '2';

        // 访问上、下、左、右四个相邻结点
        dfs(grid, r - 1, c);
        dfs(grid, r + 1, c);
        dfs(grid, r, c - 1);
        dfs(grid, r, c + 1);
    }

    // 判断坐标 (r, c) 是否在网格中
    boolean inArea(char[][] grid, int r, int c) {
        return 0 <= r && r < grid.length
                && 0 <= c && c < grid[0].length;
    }
```

### [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

![image-20210915195226155](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210915195226155.png)

```java
class Solution {
   public int maxAreaOfIsland(int[][] grid) {
        int max = 0;
        for (int r = 0; r < grid.length; r++) {
            for (int c = 0; c < grid[0].length; c++) {
                if (grid[r][c] == 1) {
                    int a = area(grid, r, c);
                    max = Math.max(a, max);
                }
            }
        }
        return max;
    }

    private int area(int[][] grid, int r, int c) {
        if (!inArea(grid, r, c)) {
            return 0;
        }
        if (grid[r][c] != 1) {
            return 0;
        }
        grid[r][c] = 2;
        return 1 + area(grid, r - 1, c) + area(grid, r + 1, c) + area(grid, r, c - 1) + area(grid, r, c + 1);
    }

    private boolean inArea(int[][] grid, int r, int c) {
        return r >= 0 && r < grid.length && c >= 0 && c < grid[0].length;
    }
}
```













## 栈

#### [字符串解码](https://leetcode-cn.com/problems/decode-string/)

```
输入：s = "3[a]2[bc]"
输出："aaabcbc"
输入：s = "3[a2[c]]"
输出："accaccacc"
```

```java
public class Test222 {
    public static String getRes(String s) {
        int len = s.length();
        int num = 0;
        //保存数字的栈
        Stack<Integer> numStack = new Stack<>();
        //保存字符串的栈
        Stack<String> stringStack = new Stack<>();
        //当前的字符串，也是最后返回的字符串
        StringBuilder cur = new StringBuilder();
        for (int i = 0; i < len; i++) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                //是否是数字，如果是数字的话，先计算个位数字，有可能会出现12[a]13[c]这样的，如果只记录一个数字的话有可能会通不过。碰到下一个还是数字的话，也能相加得出正确的数字。
                num = 10 * num + c - '0';
            } else if (c == '[') {
                //碰到[左括号，先把前面的数字压进栈中。
                numStack.push(num);
                //注意这里的string也要压进栈中，应该刚开始cur没有内容，所以开始的时候压入的是空字符串。
                stringStack.push(cur.toString());
                //注意这里需要重置一下num和cur，表示进入内层括号。比如3[a2[c]]。
                num = 0;
                cur = new StringBuilder();
            } else if (Character.isLetter(c)) {
                //这里不需要压栈操作，直接拼接cur当前字符串。
                cur.append(c);
            } else if (c == ']') {
                //这里的设计比较巧妙，这也是将2[a]3[b]和3[a2[c]]这种测试用力的区别
                // 当匹配到]的时候，证明一个内括号已经走完了，这时候需要弹出数字栈，用作循环。
                Integer k = numStack.pop();
                //记录一个temp为栈顶元素，并弹出。比如3[a2[c]]的时候，当第一次匹配到]的时候，c是栈顶元素，将c弹出
                StringBuilder temp = new StringBuilder(stringStack.pop());
                //接着用弹出的次数进行循环，比如对上述用例3[a2[c]]的时候，这时候k为2，栈顶元素为c。
                for (int j = 0; j < k; j++) {
                    temp.append(cur);
                }
                //两次循环之后成为temp = cc,这时候需要给外层传递这个变量，所以将temp传给cur。此时的cur为cc。
                // 说明接下来的步骤：下一次匹配到]的时候，先将弹出来的元素赋给变量temp，然后用temp拼接cur，这就做到了类似栈的操作，这也是难点之一。
                cur = temp;
            }else{
                //当是连续字符的时候需要拼接。
                cur.append(c);
            }
        }
        return cur.toString();
    }
}

```

## 数学

### 回文数字

```
你一个整数 x ，如果 x 是一个回文整数，返回 true ；否则，返回 false 。
回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。例如，121 是回文，而 123 不是。
示例 1：
输入：x = 121
输出：true
示例 2：
输入：x = -121
输出：false
解释：从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
示例 3：
输入：x = 10
输出：false
解释：从右向左读, 为 01 。因此它不是一个回文数。
示例 4：
输入：x = -101
输出：false
```

思路：反转一半即可

```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x<0 || (x % 10 == 0 && x != 0) ){
            return false;
        }
        int reverseNumber = 0;
        while(x>reverseNumber){
            reverseNumber = reverseNumber*10+x%10;
            x/=10;
        }
        return x == reverseNumber || x==reverseNumber/10;
    }
}
```

### [1399. 统计最大组的数目](https://leetcode-cn.com/problems/count-largest-group/) 

```java
给你一个整数 n 。请你先求出从 1 到 n 的每个整数 10 进制表示下的数位和（每一位上的数字相加），然后把数位和相等的数字放到同一个组中。
请你统计每个组中的数字数目，并返回数字数目并列最多的组有多少个。
示例 1：
输入：n = 13
输出：4
解释：总共有 9 个组，将 1 到 13 按数位求和后这些组分别是：
[1,10]，[2,11]，[3,12]，[4,13]，[5]，[6]，[7]，[8]，[9]。总共有 4 个组拥有的数字并列最多。
```

```java
class Solution {
    public int countLargestGroup(int n) {
        Map<Integer, Integer> hashMap = new HashMap<>();
        int max = 0;
        for (int i = 1; i <= n; i++) {
            int key=0, i0 = i;
            while (i0!= 0){
                key += i0%10;
                i0 /= 10;
            }
            hashMap.put(key, hashMap.getOrDefault(key, 0)+1);
            max =Math.max(max, hashMap.get(key));
        }
        int count = 0;
        for(Map.Entry<Integer, Integer> kvPair:hashMap.entrySet()){
            if (kvPair.getValue() == max){
                ++count;
            }
        }
        return count;
    }
}
```



## 并查集

### 最长连续序列

```
给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。
请你设计并实现时间复杂度为 O(n) 的算法解决此问题。

示例 1：
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
示例 2：
输入：nums = [0,3,7,2,5,8,4,6,0,1]
输出：9
```

代码

```java
package com.leetcode.month9;

import java.util.HashMap;
import java.util.Map;

public class LongestConsecutive {
   public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            set.add(num);
        }
        int longest = 0;
        for (int num:set){
            if (!set.contains(num-1)){
                //当set中没有这个元素的时候
                int currentNum = num;
                int currentStreak = 1;
                while (set.contains(currentNum+1)){
                    currentNum += 1;
                    currentStreak++;
                }
                longest = Math.max(longest, currentStreak);
            }
        }
        return longest;
    }

    public static void main(String[] args) {
        int[] arr = {1, 100, 3, 2, 4, 20};
        new LongestConsecutive().longestConsecutive(arr);
    }
}

```

## 双指针

### 接雨水

给定一个整形数组arr，已知其中所有的值都是非负的，将这个数组看作一个柱子高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![image-20210907151345743](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210907151345743.png)

代码：

```java
 public int trap(int[] height) {
        int ans = 0;
        int left = 0, right = height.length - 1;
        //找出左右边界最小的值作为水位高度
        int mark = Math.min(height[left], height[right]);
        while (left < right) {
            //如果左边低的话，左边界向右遍历，否则右边界左移
            if (height[left] < height[right]) {
                left++;
                //如果当前标尺小于水位，则水量增加
                if (height[left] < mark){
                    ans += mark-height[left];
                }else{
                    //否则的话，将此标尺和右边边界进行比较，找到剩下数组中的薪水为。
                    mark = Math.min(height[left], height[right]);
                }
            }else{
                right--;
                // 同理，如果当前标尺小于水位，则水量累加
                if (height[right] < mark) {
                    ans += mark - height[right];
                } else { // 否则，将此标尺和左边界的高度进行比较，找出剩余数组中的新水位
                    mark = Math.min(height[right], height[left]);
                }
            }
        }
        return ans;
    }
```

## leetcode没有的题

### 求数组中比左边元素都大，比右边元素都小的元素

求数组中比左边元素都大同时比右边元素都小的元素，返回这些元素的索引.

比如：2，3，1，8，9，20，12

通过分析可以得到，对于每个元素，如果它比**左侧最大的值**要大，同时比**右侧最小的值**要小，就满足条件。

那如果有这样两个数组，

`left_max[i]` 表示原数组 [0, i) 的最大值

`right_min[i]` 表示原数组 (i, n) 的最小值

内循环就可以通过 `left_max[i] < nums[i] && nums[i] < right_min[i]` 来判断了。

对于 left_max 和 right_min 这两数组，提前先算好，每个数组都能 得到。

要求时间复杂度 o(n)

```c++
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

vector<int> find(vector<int> nums) {
    vector<int> res;
    int n = nums.size();
    vector<int> left_max(n, INT_MIN);
    vector<int> right_min(n, INT_MAX);
    for (int i = 1; i < n; i++) {
        left_max[i] = max(left_max[i - 1], nums[i - 1]);
    }
    for (int i = n - 2; i >= 0; i--) {
        right_min[i] = min(right_min[i + 1], nums[i + 1]);
    }
    for (int i = 0; i < n; i++) {
        if (left_max[i] < nums[i] && nums[i] < right_min[i]) {
            res.push_back(i);
        }
    }
    return res;
}
int main()
{
    vector<int> arr = {2,3,1,8,9,20,12};
    auto res = find(arr);
    for (int i = 0; i < res.size(); i++)
    {
        cout << res[i] << ' ';
    }
}

```

## 链表

### 反转链表

输入一个链表，反转链表后，输出新链表的表头。

1，2，3->3,2,1

#### 递归

```java
public ListNode ReverseList(ListNode head) {
        if (head == null || head.next == null){
            return head;
        }
        ListNode last  = ReverseList(head.next);
        head.next.next = head;
        head.next = null;
        return last;
    }
```

#### 迭代

```java
  public ListNode reverseList(ListNode head) {
        ListNode pre= head;
        ListNode cur = null;
        while (pre!=null){
            ListNode temp = pre.next;
            pre.next = cur;
            cur = pre;
            pre = temp;
        }
        return cur;

    }
```

### 对链表进行插入排序

```java
 public ListNode insertionSortList(ListNode head) {
          ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode lastSorted = head;
        ListNode cur = head.next;
        while (cur!=null){
            if (lastSorted.val<=cur.val){
                lastSorted = lastSorted.next;
            }else {
                ListNode prev = dummy;
                while (prev.next.val <= cur.val){
                    prev = prev.next;
                }
                lastSorted.next = cur.next;
                cur.next = prev.next;
                prev.next = cur;
            }
            cur = lastSorted.next;
        }
        return dummy.next;    
    }
```

### [1669. 合并两个链表](https://leetcode-cn.com/problems/merge-in-between-linked-lists/)

![image-20210916184557532](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210916184557532.png)

[合并两个链表](https://leetcode-cn.com/problems/merge-in-between-linked-lists/solution/he-bing-liang-ge-lian-biao-by-zhang_cj-idp0/)

思路：
先查找list1中第a个结点的前一个结点，第b个结点的后一个结点。每遍历一个结点，a--，b--，当a=0时，则遍历到第a个结点，并使用list1BeforeA记录第a-1个结点，当b=0时，则遍历到第b个结点，使用list1BeforeA记录第b-1个结点。然后再使用list1BeforeA.next=list2，然后使用list2Curr.next=list1AfterB，将两个链表连接起

```java
 public ListNode mergeInBetween(ListNode list1, int a, int b, ListNode list2) {
        ListNode list1BeforeA  = list1;
        ListNode list2AfterB = list1;

        ListNode list1Curr = list1;
        ListNode list2Curr= list2;

        while (b>0 && list1Curr!=null){
            if (a>0){
                list1BeforeA = list1Curr;
                a--;
            }
            list1Curr = list1Curr.next;
            b--;
        }
        list2AfterB = list1Curr.next;
        while (list2Curr.next!=null){
            list2Curr= list2Curr.next;
        }
        list2Curr.next = list2AfterB;
        list1BeforeA.next = list2;
        return list1;

    }
```



## 树

### 求根节点到叶子节点数字之和

```
给你一个二叉树的根节点 root ，树中每个节点都存放有一个 0 到 9 之间的数字。
每条从根节点到叶节点的路径都代表一个数字：

例如，从根节点到叶节点的路径 1 -> 2 -> 3 表示数字 123 。
计算从根节点到叶节点生成的 所有数字之和 。

叶节点 是指没有子节点的节点。
```

代码：

```java
 int res = 0;
    public int sumNumbers(TreeNode root) {
        if (root == null){
            return 0;
        }
        dfs(root, 0);
        return res;
    }
    public void dfs(TreeNode root, int num){
        num = num * 10 + root.val;
        if (root.left==null && root.right == null){
            res += num;
        }
        //遍历左子树
        if (root.left!=null){
            dfs(root.left, num);
        }
        if (root.right!=null){
            dfs(root.right, num);
        }

    }
```

### 平衡二叉树的判定

```java
package com.newcode.interview;

import com.labuladong.preDefine.TreeNode;

public class Balance {
    public boolean isBalance(TreeNode root){
        if (root == null){
            return true;
        }
        return Math.abs(depth(root.left)-depth(root.right)) <= 1 && isBalance(root.left) && isBalance(root.right);
    }
    public static int depth(TreeNode root){
        if (root == null){
            return 0;
        }
        int l = depth(root.left);
        int r = depth(root.right);
        return Math.max(l, r)+1;
    }
}

```

### 根据二叉树地前序和中序还原一颗二叉树

![image-20210908150740530](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210908150740530.png)

```java
package com.newcode.interview;

import java.util.HashMap;
import java.util.Map;
class TreeNode {
    TreeNode left;
    TreeNode right;
    int val;

    public TreeNode(int val) {
        this.val = val;
    }

    public TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}

public class MyBuildTree {
    private Map<Integer, Integer> indexMap;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int n = preorder.length;
        //构造哈希映射
        indexMap = new HashMap<>();
        for (int i = 0; i < n; i++) {
            indexMap.put(inorder[i], i);
        }
        return mybuildTree(preorder, inorder, 0, n - 1, 0, n - 1);
    }

    public TreeNode mybuildTree(int[] preorder, int[] inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        if (preorder_left > preorder_right) {
            return null;
        }
        //根节点
        int rootVal = preorder[preorder_left];
        //创建根节点
        TreeNode root = new TreeNode(rootVal);
        //根据根节点找到在中序遍历中地位置
        int pIndex = indexMap.get(rootVal);
        //递归地构建左子树，并连接到根节点
        //先序遍历从左边界+1 开始地sizeOfLeftSubTree个元素就对应地是中序遍历中地左边界开始到根节点-1地元素
        root.left = mybuildTree(preorder, inorder, preorder_left+1, pIndex-inorder_left+preorder_left, inorder_left, pIndex-1);

        // 递归地构造右子树，并连接到根节点
        // 先序遍历中「从 左边界+1+左子树节点数目 开始到 右边界」的元素就对应了中序遍历中「从 根节点定位+1 到 右边界」的元素
        root.right = mybuildTree(preorder, inorder, pIndex-inorder_left+preorder_left+1, preorder_right, pIndex+1, inorder_right);
        return root;
    }
}

```



## 排序

### 快排

```java
   public void quickSort(int[] arr, int left, int right) {
        int n = arr.length;
        int low = left;
        int high = right;
        if (low > high) {
            return;
        }
        int temp = arr[low];
        while (low < high) {
            while (low < high && arr[high] >= temp) {
                high--;
            }
            arr[low] = arr[high];
            while (low < high && arr[low] <= temp) {
                low++;
            }
            arr[high] = arr[low];
        }
        arr[low] = temp;

        quickSort(arr, left, low - 1);
        quickSort(arr, low + 1, right);
    }
```

### 堆

```java
package com.base.sort.compare.exchange;
import java.lang.reflect.Array;
import java.util.Arrays;
public class HeapSort {
    /**
     * 交换数组arr中m和n的位置
     *
     * @param arr 待交换数组
     * @param m   数组下标为m
     * @param n   数组下标为n
     */
    public void swap(int[] arr, int m, int n) {
        int temp = arr[m];
        arr[m] = arr[n];
        arr[n] = temp;
    }

    /**
     * 用给定的数组创建成堆
     *
     * @param arr 给定的数组
     */
    public void createHeap(int[] arr) {
        for (int i = arr.length / 2; i >= 0; i--) {
            shiftDown(arr, i, arr.length);
        }
    }

    /**
     * 下移交换，把当前节点有效变换成为一个堆（小根堆）
     *
     * @param arr   创建的数组
     * @param index 线标
     * @param len   长度
     */
    public void shiftDown(int[] arr, int index, int len) {
        //0号位置不需要
        int leftChild = index * 2 + 1;
        int rightChild = index * 2 + 2;
        if (leftChild >= len) {
            return;
        } else if (rightChild < len && arr[rightChild] < arr[index] && arr[rightChild] < arr[leftChild]) {
            //交换节点值
            swap(arr, index, rightChild);
            //可能会对孩子节点的堆有影响，向下重构
            shiftDown(arr, rightChild, len);
        } else if (arr[leftChild] < arr[index]) {
            //交换做孩子
            swap(arr, index, leftChild);
            shiftDown(arr, leftChild, len);
        }
    }

    public void heapSort(int[] arr){
        System.out.println("原数组为："+ Arrays.toString(arr));
        //临时存储结果
        int[] val = new int[arr.length];
        //建堆
        createHeap(arr);
        System.out.println("建立堆之后的序列为："+ Arrays.toString(arr));
        // 进行n次取值建堆，每次取堆顶元素放入val数组中，最终结果即为一个递增排序的序列
        for(int i = 0;i<arr.length;i++){
            //将堆顶放入结果中
            val[i] = arr[0];
            //删除堆顶元素，将末尾元素放到堆顶
            arr[0] = arr[arr.length-i-1];
            //将这个堆调整为合法的小根堆，主义长度上的变化
            shiftDown(arr, 0, arr.length-i);
        }
        //数值克隆
        for(int i = 0;i<arr.length;i++){
            arr[i] = val[i];
        }
        System.out.println("堆排序后的序列为:"+Arrays.toString(arr));
    }

    public static void main(String[] args) {
        int[] arr = {3,7,4,6,8,5,2};
        new HeapSort().heapSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}

```

### 选择排序

每一轮选择未排序的最小的元素交换到未排序的最开头，然后下一轮从排好序的下一个元素开始，再找剩下最小的放在第二个位置。。。

```java
 public int[] sortArray(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            int min = nums[i];
            for (int j = i+1; j < n; j++) {
                if (nums[j]<min){
                    min = nums[j];
                    swap(nums, i, j);
                }
            }
        }
        return nums;
    }
     public void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j]= temp;
    }
```

### 插入排序

每次将一个数字插入到有序数组里，成为一个更长的有序数组，有限次操作之后，数组整体有序。

![](https://pic.leetcode-cn.com/710dd138492c0da4324657033971f3bee0355514f2ab2834756c988a90398cbb-file_1585624920301)

```java
  // 插入排序：稳定排序，在接近有序的情况下，表现优异

    public int[] sortArray(int[] nums) {
        int len = nums.length;
        // 循环不变量：将 nums[i] 插入到区间 [0, i) 使之成为有序数组
        for (int i = 1; i < len; i++) {
            // 先暂存这个元素，然后之前元素逐个后移，留出空位
            int temp = nums[i];
            int j = i;
            // 注意边界 j > 0
            while (j > 0 && nums[j - 1] > temp) {
                nums[j] = nums[j - 1];
                j--;
            }
            nums[j] = temp;
        }
        return nums;
    }
```



## 区间集合

### [435. 无重叠区间](https://leetcode-cn.com/problems/non-overlapping-intervals/)

```
给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。
注意:
可以认为区间的终点总是大于它的起点。
区间 [1,2] 和 [2,3] 的边界相互“接触”，但没有相互重叠。
示例 1:

输入: [ [1,2], [2,3], [3,4], [1,3] ]

输出: 1

解释: 移除 [1,3] 后，剩下的区间没有重叠。
```

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
         //特判
        if (intervals.length == 0) {
            return 0;
        }
        //按照右侧升序排序
        Arrays.sort(intervals, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[1] - o2[1];
            }
        });
        int count = 1;
        int x_end = intervals[0][1];
        for (int[] in : intervals) {
            int start = in[0];
            if (start >= x_end) {
                count++;
                x_end = in[1];
            }
        }
        return intervals.length-count;
    }
}
```

#### 



## 设计题

### LRU

```java
import java.util.HashMap;
import java.util.Map;
public class LRU {
    class DoubleLinkedList{
        int key;
        int value;
        DoubleLinkedList prev;
        DoubleLinkedList next;
        public DoubleLinkedList(){

        }
        public DoubleLinkedList(int key, int value){
            this.key = key;
            this.value = value;
        }
    }
    private Map<Integer,DoubleLinkedList> cache = new HashMap<>();
    private int size;
    private int capacity;
    private DoubleLinkedList head, tail;

    public LRU(int capacity){
        this.size = 0;
        this.capacity = capacity;
        head = new DoubleLinkedList();
        tail = new DoubleLinkedList();
        head.next = this.tail;
        tail.prev = this.head;
    }

    public int get(int key){
        DoubleLinkedList doubleLinkedList = cache.get(key);
        if (doubleLinkedList==null){
            return -1;
        }else{
            //证明存在，需要调整节点位置移到最前面
            moveToHead(doubleLinkedList);
            return  doubleLinkedList.value;
        }
    }

    private void moveToHead(DoubleLinkedList node) {
        removeNode(node);
        addToHead(node);
    }
    public void removeNode(DoubleLinkedList node){
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    public void addToHead(DoubleLinkedList node){
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    public DoubleLinkedList removeTail(){
        DoubleLinkedList res = this.tail.prev;
        removeNode(res);
        return res;
    }

    public void put(int key, int value){
        //先从cache中存取
        DoubleLinkedList node = cache.get(key);
        if (node == null){
            //如果key不存在，创建一个新的节点
            DoubleLinkedList newNode = new DoubleLinkedList(key, value);
            //存放进cache中
            cache.put(key, newNode);
            //添加至双向链表的头部
            addToHead(newNode);
            ++size;
            if (size>capacity){
                //如果要大于容量需要删除链表尾部的节点
                DoubleLinkedList tail = removeTail();
                //删除cache中的项
                cache.remove(tail.key);
                --size;
            }
        }else{
            //当key存在，先通过hash表定位，然后再修改value，并移到头部
            node.value = value;
            moveToHead(node);
        }
    }
}

```





