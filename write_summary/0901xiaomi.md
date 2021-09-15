# 9月1日小米笔试

笔试题总结

## 单选：

1. 关于网络协议错误的是：

   Socket通信只能用TCP协议进行通信

   错误，也可以用UDP协议通信。

2. 在一棵二叉树上第10层的节点数最多是？

   二叉树最多的情况就是满二叉树，满二叉树的节点为2<sup>n-1</sup>

3. 关于进程内存空间描述错误的是？

   程序结束时，在堆上分配的没有释放的内存会造成泄露。不会，会被回收。

4. 单链表和双向链表的知识点，为什么要用双链表

5. 使用Linux内核的系统：红帽、安卓、ubuntu。macOS不是Linux内核。

6. 看程序写结果，没什么可写的。

7. 完成5！计算，不能完成此操作的程序段。

8. Linux中进程和线程错误的是：B：当进程处于TASK_UNINTERRUPTIBLE状态时可以被杀死。Task_Interruptable 即进程是可以被打断的睡眠状态（sleep）。D项是正确的，在Linux中没有线程可以视为进程，一种与其他进程共享某些资源的进程。

9. HashMap的put，如果put的val为null并且put的key也是null的时候，问map的size为多少？

   比如put（1,2），put(3,4),put(null, 1),put(3,null)，问size多少。答案：4。

10. 不会触发进程调度的是？

    正在运行的进程访问临界区的时候，保护次临界区的互斥信号量已经被其他进程加锁。线程阻塞。不触发进程调度。

11. 给定二维数组，问指定位置是多少。

## 多选

1. 关于操作系统分段机制描述正确的是？

   代码段可执行文件的操作指令，代码段是只读的，不能写。

   > 用来存放可执行文件完整的操作指令（机器码）和只读数据，为了防止代码段被非法修改，代码段的特点是只读不写的。如果一个程序有多个运行实体，则这些实体共享同一个代码段。

   BBS段：用来存放可执行文件中未初始化的全局变量，在内存中全部置零。注意：BSS段中的变量只占用程序运行时的内存空间，而不占用程序文件的储存空间。

   数据段：用来存放可执行文件中已经初始化了的全局变量，也就是在程序中静态分配的变量和全局变量。数据段在编译时分配。注意：数据段中的变量既占程序运行时的内存空间，也占程序文件的储存空间。

   分段  和分页都是为了提高内存利用率减少内存碎片。

   > 引申：分段和分页的区别？为什么要分页、为什么要分段、段页式解决什么问题

2. 文件系统，描述不正确的是?

   一个文件在磁盘上存放的磁盘块必须是相邻的。可以非连续存放。错

   删除一个文件，同时会删除与此文件对应的文件控制块 应该正确吧。

   >为了实现按名存取，文件系统会为每个文件设置一个文件控制块 (FCB)。它是一种用于存放控制文件需要的各种信息的数据结构。通常包含以下信息：
   >
   >基本信息 (文件名、物理地址、逻辑结构)
   >存取控制信息 (文件权限相关)
   >使用信息 (文件建立、修改信息)
   >FCB的有序集合叫做文件目录，一个FCB就是一个文件目录项。
   >
   >创建一个新文件，实际上是系统分配一个FCB，存放在文件目录中的一个目录项。

   符号连接所连接的文件被删除后，符号连接也消失，正确。类似软连接，快捷方式。

   进程终止后，这个进程打开的文件也会被删除。错误。

3. 排序算法正确的是。

4. 希尔排序：希尔是插入排序的改进版本，非稳定的。

5. 虚拟内存：虚拟内存使得多个应用程序之间切换花费更少的时间。正确，解决上下文切换的时间消耗问题。

   虚拟内存是计算机系统内存管理的一种技术，使应用程序认为有连续可用的内存。正确。

   虚拟内存和物理内存的映射通过页表实现。正确。

6. 冒泡排序算法

7. 数组和链表。

8. 汇编指令

9. C语言存储空间。

## 编程

### 第一个：两个有序数组合并

#### 题目描述：

给定两个有序数组A和B，将B合并到A，使得A成为一个有序数组。

说明：初始化A和B的元素数量为m和n。A有足够空间来存B的元素。默认升序。

输入描述：

```
数组A，以及数组A元素的数量

数组B，以及数组B元素的数量

A=[1,6,7,0,0,0] m=3

B=[2,4,6], n=3
```

输出描述

```
合并后A的数组A=[1,2,3,6,6,7]
```

样例输入：

```java
m=2,n=2
1,3
2,4
```

样例输出

```java
1 2 3 4
```

#### 代码

```java
package com.test.xiaomi;

import java.util.*;

/**
 * @Author yamon
 * @Date 2021-09-01 19:30
 * @Description
 * @Version 1.0
 */
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String[] inputs = sc.nextLine().split(",");
        int m = Integer.parseInt(inputs[0].substring(2));
        int n = Integer.parseInt(inputs[1].substring(2));
        String[] str1 = sc.nextLine().split(",");
        int[] nums1 = new int[m+n];
        for (int i = 0; i < m; i++) {
            nums1[i] = Integer.parseInt(str1[i]);
        }

        String[] str2 = sc.nextLine().split(",");
        int[] nums2 = new int[n];
        for (int i = 0; i < n; i++) {
            nums2[i] = Integer.parseInt(str2[i]);
        }
        merge(nums1, nums2, m, n);
        StringBuilder sb = new StringBuilder();
        for (int num:nums1){
            sb.append(num+" ");
        }
        System.out.println(sb.delete(sb.length()-1, sb.length()));
    }

    public static void merge(int[] A, int[] B, int m, int n) {
        int a = 0, b = 0;
        int[] ans = new int[m + n];
        int cur;
        while (a < m || b < n) {
            if (a == m) {
                //如果a到头了，将b中剩余的所有元素放到数组内,然后b指针后移
                cur = B[b++];
            } else if (b == n) {
                //当b走到头了，a中剩余元素放到数组内，a后移
                cur = A[a++];
            } else if (A[a] < B[b]) {
                //如果A中元素小于B中的元素，将A中元素放到数组中，然后A后移
                cur = A[a++];
            } else {
                //这里包含的条件是大于等于，等于的时候将B元素的数字放进数组，同时b直着后移
                cur = B[b++];
            }
            //放数组
            ans[a + b - 1] = cur;
        }
        //将数组整理放到A中。
        // 也可以采用API的方式，但是没在考试中测试，只是在IDE中测过。System.arraycopy(ans, 0, A, 0, m+n);
        for (int i = 0;i!=m+n;i++){
            A[i] = ans[i];
        }
    }

}

```

### 第二个：整数列表求三的倍数

给定一个1-n的整数列表，从第一个数字开始计数，遇到3的倍数的时候，将该数从列表中删除，直至列表末尾。在剩下的数字中，从第一个数字开始，继续之前的计数值，同样遇到3的倍数时，删除该数，循环上述操作，只剩下一个数字。根据n，判断剩下哪个数字。

#### 输入描述

```
输入：n=5
过程：第一步 1,2,3,4,5 （3）
第二步：1,2,4,5 （1）
第三布：2,4 （2）
剩下4，输出
```

样例输入5

输出4

#### 代码：

```java
package com.test.xiaomi;

import java.util.Scanner;

/**
 * @Author yamon
 * @Date 2021-09-02 10:58
 * @Description
 * @Version 1.0
 */
public class Main2 {
    public static int lastRemain(int n){
        int x = 0;
        for(int i = 2;i <= n;i++){
            x = (x+3) % i;
        }
        return x+1;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int res = lastRemain(n);
        System.out.println(res);
    }
}

```

[leetcode圆圈中最后剩下的数字](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/jian-zhi-offer-62-yuan-quan-zhong-zui-ho-dcow/)







## 参考

[Socket通信](https://blog.csdn.net/qq_43625140/article/details/109090269)

[Linux进程的几种状态](https://www.cnblogs.com/jjpan1997/p/10520050.html)

[Linux内核学习010——进程管理(六)](https://blog.csdn.net/m0_37194132/article/details/88058018)

[操作系统面试题（十一）：分页机制与分段机制的共同点和区别](https://blog.csdn.net/weixin_44478378/article/details/107589110)

[编译原理](https://blog.csdn.net/dcrmg/article/details/83187279)

[文件控制块](https://blog.csdn.net/flying_keyboard/article/details/116697591)

[符号链接文件_在 Linux 中怎样移除（删除）符号链接](https://blog.csdn.net/weixin_32208021/article/details/112584908)

