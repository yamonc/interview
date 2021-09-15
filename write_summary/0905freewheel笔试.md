# 0905freewheel笔试

第一道题SQL题

用SQL语句查询汽车类广告主时长不超过20s的广告创意占多少？

不会，没做出来。

第二道题：关于树。

一条任务线，任务之间的依赖可以看成一个二叉树的结构，同一层的任务由同一个组负责：

比如这样：

![image-20210905185414958](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210905185414958.png)

任务0由A组负责，

任务1、2由B负责

任务3、4、5、6由C负责

任务7、8由D组负责。

有如下规则：

1. 当任务失败的时候，需要通知其所有的下游节点，比如0失败了需要通知到1、2、3、4、5、6、7、8.任务1失败时只需要通知3、4、7
2. 节点可以被父节点或者同层的其他节点通知到。比如任务0失败了可以被0或者2通知到。3失败了可以被1、4、5、6通知到。
3. 每个节点通知子节点的时间由timecost数组给出，同一层节点间通知时间耗时为0，比如timecost为[1,3,2,4,5,6,7,8,9],下标为任务标号，值为通知子节点耗时。

求某一节点失败时，通知到下游节点最少耗时。



输入：{0,1,2,3,4,5,6,7,#,#,#,8},0,[1,3,2,4,5,6,7,8,9]

输出7

```java
package com.test.freewheel;

import com.labuladong.preDefine.TreeNode;

import java.util.ArrayList;
import java.util.Deque;
import java.util.LinkedList;
import java.util.List;

/**
 * @Author yamon
 * @Date 2021-09-05 15:43
 * @Description
 * @Version 1.0
 */
public class Main {
    public int GetMinTimeCost (TreeNode root, int failId, int[] timeCost) {
        // write code
        if(root == null){
            return 0;
        }
        int sum = 0;
        TreeNode failNode = dfs(root, failId);
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(failNode);
        //sum += timeCost[root.val];
        int last = -1;
        while (!queue.isEmpty()){
            int min = Integer.MAX_VALUE;
            List<Integer> level = new ArrayList<>();
            int levelNum = queue.size();
            for (int i = 0; i < levelNum; i++) {
                TreeNode node = queue.poll();
                level.add(node.val);
                min = Math.min(min, timeCost[node.val]);
                if (node.left!= null){
                    queue.add(node.left);
                }
                if (node.right!=null){
                    queue.add(node.right);
                }
            }
            sum += min;
            last = min;
        }
        return sum-last;
    }
    private TreeNode dfs(TreeNode root, int faild){
        if (root == null){
            return null;
        }
        if (faild == root.val){
            return root;
        }
        TreeNode left = dfs(root.left, faild);
        if (left!=null){
            return left;
        }
        return dfs(root.right, faild);
    }

    public static void main(String[] args) {
        TreeNode root = new TreeNode(0);
        TreeNode l = new TreeNode(1);
        TreeNode r = new TreeNode(2);
        TreeNode ll = new TreeNode(3);
        TreeNode lr = new TreeNode(4);
        TreeNode rl = new TreeNode(5);
        TreeNode rr = new TreeNode(6);
        TreeNode lll = new TreeNode(7);
        TreeNode rll = new TreeNode(8);
        root.left = l;
        root.right = r;
        l.left = ll;
        l.right = lr;
        r.left = rl;
        r.right =rr;
        ll.left = lll;
        rl.left = rll;
        Main main = new Main();
        main.GetMinTimeCost(root,2, new int[]{1,3,2,4,5,6,7,8,9});
    }
}

```

第三题不会做。看了说是什么记忆化搜索。

