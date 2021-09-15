# String创建对象问题

```java
String s = "a"+"b"+"C"
```

## 这样会创建几个字符串对象？

自己可以创建一下，然后javac编译一下。

```java
package gongzhonghao;

/**
 * @Author yamon
 * @Date 2021-09-02 13:51
 * @Description
 * @Version 1.0
 */
public class Test {
    public static void main(String[] args) {
        String s = "a" + "b" + "c";
    }
}

```

javac编译之后：

```jvm
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package gongzhonghao;

public class Test {
    public Test() {
    }

    public static void main(String[] var0) {
        String var1 = "abc";
    }
}

```

可以到只创建了一个String对象。

为什么只会创建一个对象？

> 这是因为在编译期间，应用了编译器优化中一种被称为**常量折叠**(Constant Folding)的技术，会将**编译期常量**的加减乘除的运算过程在编译过程中折叠。编译器通过语法分析，会将常量表达式计算求值，并用求出的值来替换表达式，而不必等到运行期间再进行运算处理，从而在运行期间节省处理器资源。

需要我们注意的是常量才会这样。

编译常量的特点：

- 被声明为final
- 基本类型或者字符串类型
- 声明时已经初始化
- 使用常量表达式进行初始化。

强烈建议看下这个文章：[String s="a"+"b"+"c"，到底创建了几个对象？](http://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247509874&idx=2&sn=0a7fe7513d2708287f05d7ee97064ecd&chksm=cea1e8b9f9d661af3e90dc95b02c31444dcd503c6bc5e7de5cd43eb9153d94bcf0aa1dc3010b&scene=21#wechat_redirect)

-----

## String类型的变量和常量做+运算发生了什么？

![image-20210902141752089](https://gitee.com/yamonc/blogImage/raw/master//img/blogImage/image-20210902141752089.png)

一道笔试题；先自我分析一下

a和b属于字符串常量，所以d=a+b后，会成为这样：

```
(new StringBuilder()).append(var1).append(var2).append(var3).toString();
```

编译期的时候，会使用StringBuilder对象拼接字符串，而c=“a”+“b”的话，是编译期常量，所以这样的：

```
String var5 = "abc";
```

所以不会相等。

使用final之后，两个变量都是编译器变量了，就相等了。



### 解释：

```java
String str1 = "str";
String str2 = "ing";

String str3 = "str" + "ing";//常量池中的对象
String str4 = str1 + str2; //在堆上创建的新的对象
String str5 = "string";//常量池中的对象
System.out.println(str3 == str4);//false
System.out.println(str3 == str5);//true
System.out.println(str4 == str5);//false
```

对于编译器可以确定的值的字符串，也就是常量字符串，jvm会将其存进字符串常量池。

> 字符串常量池：JVM为了提升性能和减少内存消耗，针对String类专门开辟了一块区域，主要目的是为了避免字符串的重复创建。

```java
String aa = "ab" //放在常量池中
String bb = "ab" //在常量池中查找，如果有的话直接引用。所以String类是final，不可更改。
```

jdk7之前运行时常量池逻辑包含字符串常量池存放在方法区，jdk7之后，字符串常量池被从方法区拿到了堆中。

并且，字符串常量拼接的字符串常量在编译阶段就已经被存放在字符串常量池中，这个得益于编译器的优化。

因此str1和str2和str3都是字符串常量池中的对象，同时指向一个对象。

而引用的值在编译期是无法确定的，编译器无法对其进行优化。对象的引用和“+”的字符串拼接方式，实际上是通过StringBuilder调用append方法实现的，拼接完成之后，得到String对象。

因此str4不是字符串常量池中的对象，属于堆上的新对象。

不过，字符串使用 `final` 关键字声明之后，可以让编译器当做常量来处理。

```java
final String str1 = "str";
final String str2 = "ing";
// 下面两个表达式其实是等价的
String c = "str" + "str2";// 常量池中的对象
String d = str1 + str2; // 常量池中的对象
System.out.println(c == d);// true
```

被 `final` 关键字修改之后的 `String` 会被编译器当做常量来处理，编译器在程序编译期就可以确定它的值，其效果就想到于访问常量。

---

## 创建字符串的方式以及流程

```java
String a = "abcd";
String b = new String("abcd");
String c = new String("abcd");
```

上述一共两种创建字符串的方式。

先说第一种：

```java
String a = "abcd"
```

这种情况下，jvm首先会检查字符串常量池中有没有字符串常量“abcd”，如果没有的话，创建一个，然后a指向这个对象。如果有的话，直接a指向“abcd”这个对象。

所以a指向的字符串常量池中的对象。

第二种：

```java
String b = new String("abcd");
String c = new String("abcd");
```

只要用new创建对象，便需要创建新的对象：

1. 在堆中创建一个字符串对象。
2. 检查字符串常量池中是否有和new的字符串值相等的字符串常量
3. 如果没有的话，需要在字符串常量池中也创建一个值相等的字符串常量，如果有的话，直接返回堆中的字符串实例对象地址。

所以bc是新建对象。

字符串常量池比较特殊，主要使用方法有两种：

1. 使用双引号声明出来的String对象会直接存到常量池中。比如String a = “a”；

2. 如果不是双引号声明的Sting对象，使用String提供的intern()方法也有同样的效果

   > String.intern()是一个Native方法，作用：如果运行时常量池中已经包含了一个等于此String对象内容的字符串，则返回常量池中引用；如果没有，jdk1.7之前（不包括1.7）的处理方式是在常量池中创建与String内容相同的字符串，并返回常量池中创建的字符串引用，之后是在常量池中记录此字符串的引用，返回该引用。

