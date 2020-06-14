---
layout: post
title: JVM内存模型深度剖析与优化
categories: [Jvm, Java]
description: some word here
keywords: wenhao, 文浩  ,  fuwenhao.club , wenhaoclub 
---

## 序：
> 一个main方法执行所流转的过程，以及GC回收处理……必备知识点。

## 分析: 
### JDK体系结构：
![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/JDK%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84.png)

### Java语言跨平台特性
![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/java%E8%AF%AD%E8%A8%80%E8%B7%A8%E5%B9%B3%E5%8F%B0%E7%89%B9%E6%80%A7.png)


### JVM整体结构及内存模型
![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/jvm%E6%95%B4%E4%BD%93%E6%9E%B6%E6%9E%84%E5%9B%BE.png)

### JVM内存参数设置
![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/jvm%E5%86%85%E5%AD%98%E5%8F%82%E6%95%B0%E8%AE%BE%E7%BD%AE.png)


- Spring Boot程序的JVM参数设置格式(Tomcat启动直接加在bin目录下catalina.sh文件里):
	>  java‐Xms2048M‐Xmx2048M‐Xmn1024M‐Xss512K‐XX:MetaspaceSize=256M‐XX:MaxMetaspaceSize=256M‐jarmicroservice‐eurek a‐server.jar

- 关于元空间的JVM参数有两个:-XX:MetaspaceSize=N和 -XX:MaxMetaspaceSize=N，对于64位JVM来说，元空间的默认初始大小是 21MB，**默认的元空间的最大值是无限。**
- -XX:MaxMetaspaceSize: 设置元空间最大值， 默认是-1， 即不限制， 或者说只受限于本地内存大小。
- -XX:MetaspaceSize: 指定元空间的初始空间大小， 以字节为单位，默认是21M，达到该值就会触发full gc进行类型卸载， 同时收集 器会对该值进行调整: 如果释放了大量的空间， 就适当降低该值; 如果释放了很少的空间， 那么在不超过-XX: MaxMetaspaceSize(如果设置了的话) 的情况下， 适当提高该值。
- 由于调整元空间的大小需要Full GC，这是非常昂贵的操作，如果应用在启动的时候发生大量Full GC，通常都是由于永久代或元空间发生 了大小调整，基于这种情况，一般建议在JVM参数中将MetaspaceSize和MaxMetaspaceSize设置成一样的值，并设置得比初始值要大， 对于8G物理内存的机器来说，一般我会将这两个值都设置为256M。

- StackOverflowError示例:

```
// JVM设置 ‐Xss128k(默认1M) publicclassStackOverflowTest{ static int count = 0;static void redo() { count++;
redo(); 11 public static void main(String[] args) {
12 try{
13 redo();
14 } catch (Throwable t) {
15 t.printStackTrace();
16 System.out.println(count);
17 }
18 }
19 }
20
21 运行结果:
22 java.lang.StackOverflowError
23 at com.tuling.jvm.StackOverflowTest.redo(StackOverflowTest.java:12)
24 at com.tuling.jvm.StackOverflowTest.redo(StackOverflowTest.java:13)
25 at com.tuling.jvm.StackOverflowTest.redo(StackOverflowTest.java:13)
```
- 结论：
	- -Xss设置越小count值越小，说明一个线程栈里能分配的栈帧就越少，但是对JVM整体来说能开启的线程数会更多
- JVM内存参数大小该如何设置? 
	- JVM参数大小设置并没有固定标准，需要根据实际项目情况分析，给大家举个例子
	![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/GC%E9%85%8D%E7%BD%AE.png)

- 结论: 通过上面这些内容介绍，大家应该对JVM优化有些概念了，就是尽可能让对象都在新生代里分配和回收，尽量别 让太多对象频繁进入老年代，避免频繁对老年代进行垃圾回收，同时给系统充足的内存大小，避免新生代频繁的进行垃 圾回收


## 琐碎知识点：
- **一个方法对应一块栈帧的内存区域**
- 一个方法内部：
	- 局部变量表：
		- 存储变量值
	- 操作数栈：
		- 进行压栈-出栈操作。
		- FILO（first in  last out） 先进后出  。
	- 动态链接：
		- 方法对应的位置，存储在此。
	- 方法出口：
		- 知道继续从下一步哪个方法去执行
- main方法的局部变量表有一些不一样：
	- 存放的是堆的位置。
	- 对象的指针
- 栈和帧的关系：
	- 栈里面存放的  都是  对象在堆里面的内存地址
- 方法区：（元空间）
	- 放入方法中 ，即运行时常量区  
	- 存放：静态常量值和静态变量值 和类信息
	- 方法区与堆之间的关系：
		- 方法区存放的 都是 对象再堆里面的内存地址
		- 堆和方法区是共享线程
- 本地方法栈：
	- native修饰  底层C++实现
- 栈、本地方法栈、程序计数器都是独立线程

- new的对象 会先放在Eden区 后续GC回收
	- minor gc 垃圾收集
	- gc root根引用。 （一般直接或者间接的引用，有关联关系）
	- 一般反复循环**15次**处理后，会存放到老年代。这个分代年龄值可能不同
	- 静态对象会放入老年代中
	- full gc
		- 回收堆、回收方法区 
	- 建议：生产环境设置元空间和扩容空间大小值一致
- JVM的STW机制 (stop the world)
	- 作用：停止用户线程。 会有影响性能。
	- 个人理解：再GC遍历过程中，突然线程结束了。所以触发这个机制，将遍历结果放入survivor中。再即系线程流转。
	- [相关概念参考地址](https://blog.csdn.net/zkkzpp258/article/details/80080764)
- 字节码文件：
- 方法再内存中占有一定的位置
- 反汇编命令：
	- javap -c  ***.class
	- javap -v  **.class  详细输出
- [参考Jvm指令手册](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/files/java/jvm/00-JVM%E6%8C%87%E4%BB%A4%E6%89%8B%E5%86%8C.pdf)
- 程序计数器--可以理解为执行存储code的顺序值
- 每次执行完毕后，字节码执行引擎会去修改它。记录指令行号。
- 注意：程序计数器是独立线程的，不要混淆。

## 总结：
- 学会指令方便实践了解JVM底层逻辑，而不是停留在概念层次上