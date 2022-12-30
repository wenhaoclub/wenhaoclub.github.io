---
layout: post
title: JVM对象创建与内存分配机制深度剖析
categories: [Jvm, Java]
description: JVM对象创建与内存分配机制深度剖析
keywords: wenhao, 文浩  ,  fuwenhao.club , wenhaoclub 
link: https://gcore.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/head2.jpg
---

# 序：
> 北京疫情二次复发，精神小伙表示小命要紧，没事宅在家coding多美好……

## 分析：

### 对象的创建：
对象创建的主要流程:

![](https://gcore.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/%E5%AF%B9%E8%B1%A1%E5%88%9B%E5%BB%BA.png)

1. **类加载检查** 
	- 虚拟机遇到一条new指令时，首先将去检查这个指令的参数是否能在常量池中定位到一个类的符号引用，并且检查这个符号引用代表的类是否已被加载、解析和初始化过。如果没有，那必须先执行相应的类加载过程。 
	- new指令对应到语言层面上讲是，new关键词、对象克隆、对象序列化等。
2. **分配内存**
	- 在类加载检查通过后，接下来虚拟机将为新生对象分配内存。对象所需内存的大小在类 加载完成后便可完全确定，为对象分配空间的任务等同于把 一块确定大小的内存从Java堆中划分出来。
	- 这个步骤有两个问题:
		- 1.如何划分内存。
		- 2.在并发情况下， 可能出现正在给对象A分配内存，指针还没来得及修改，对象B又同时使用了原来的指针来分配内存的 情况。
	- **划分内存的方法:**
		- “指针碰撞”(Bump the Pointer)(默认用指针碰撞)
			- 如果Java堆中内存是绝对规整的，所有用过的内存都放在一边，空闲的内存放在另一边，中间放着一个指针作为分界点 的指示器，那所分配内存就仅仅是把那个指针向空闲空间那边挪动一段与对象大小相等的距离。
		- “空闲列表”(Free List)
			- 如果Java堆中的内存并不是规整的，已使用的内存和空 闲的内存相互交错，那就没有办法简单地进行指针碰撞了，虚拟 机就必须维护一个列表，记 录上哪些内存块是可用的，在分配的时候从列表中找到一块足够大的空间划分给对象实例， 并更新列表上的记录
		- 解决并发问题的方法:
			- CAS(compare and swap) 虚拟机采用CAS配上失败重试的方式保证更新操作的原子性来对分配内存空间的动作进行同步处理。
			- 本地线程分配缓冲(Thread Local Allocation Buffer,TLAB)
		- 把内存分配的动作按照线程划分在不同的空间之中进行，即每个线程在Java堆中预先分配一小块内存。通过­XX:+/­ UseTLAB参数来设定虚拟机是否使用TLAB(JVM会默认开启­XX:+UseTLAB)，­XX:TLABSize 指定TLAB大小。 
3. **初始化**
	- 内存分配完成后，虚拟机需要将分配到的内存空间都初始化为零值(不包括对象头)， 如果使用TLAB，这一工作过程也 可以提前至TLAB分配时进行。这一步操作保证了对象的实例字段在Java代码中可以不赋初始值就直接使用，程序能访问 到这些字段的数据类型所对应的零值。
4.  **设置对象头**
	- 初始化零值之后，虚拟机要对对象进行必要的设置，例如这个对象是哪个类的实例、如何才能找到类的元数据信息、对 象的哈希码、对象的GC分代年龄等信息。这些信息存放在对象的对象头Object Header之中。 
	- 在HotSpot虚拟机中，对象在内存中存储的布局可以分为3块区域:对象头(Header)、 实例数据(Instance Data) 和对齐填充(Padding)。 HotSpot虚拟机的对象头包括两部分信息，第一部分用于存储对象自身的运行时数据， 如哈 希码(HashCode)、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时 间戳等。对象头的另外一部分 是类型指针，即对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例。

![](https://gcore.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/%E5%AF%B9%E8%B1%A1%E5%A4%B4%E5%88%86%E6%9E%90.png)

	对象头在hotspot的C++源码里的注释如下:
	
	```
	Bit‐formatofanobjectheader(mostsignificantfirst,bigendianlayoutbelow): 2 //
	3 //32bits:
	4 //‐‐‐‐‐‐‐‐
	5 //hash:25‐‐‐‐‐‐‐‐‐‐‐‐>|age:4biased_lock:1lock:2(normalobject)
	6 //JavaThread*:23epoch:2age:4biased_lock:1lock:2(biasedobject)
	7 //size:32‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐>|(CMSfreeblock)
	8 //PromotedObject*:29‐‐‐‐‐‐‐‐‐‐>|promo_bits:3‐‐‐‐‐>|(CMSpromotedobject)
	9 //
	10 //64bits:
	11 //‐‐‐‐‐‐‐‐
	12 //unused:25hash:31‐‐>|unused:1age:4biased_lock:1lock:2(normalobject)
	13 //JavaThread*:54epoch:2unused:1age:4biased_lock:1lock:2(biasedobject)
	14 //PromotedObject*:61‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐>|promo_bits:3‐‐‐‐‐>|(CMSpromotedobject)
	15 //size:64‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐>|(CMSfreeblock)
	16 //
	17 //unused:25hash:31‐‐>|cms_free:1age:4biased_lock:1lock:2(COOPs&&normalobject)
	18 //JavaThread*:54epoch:2cms_free:1age:4biased_lock:1lock:2(COOPs&&biasedobject)
	19 //narrowOop:32unused:24cms_free:1unused:4promo_bits:3‐‐‐‐‐>|(COOPs&&CMSpromotedobject) 20 //unused:21size:35‐‐>|cms_free:1unused:7‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐‐>|(COOPs&&CMSfreeblock)
	```
5. **执行<init>方法**
	- 执行<init>方法，即对象按照程序员的意愿进行初始化。对应到语言层面上讲，就是为属性赋值(注意，这与上面的赋零值不同，这是由程序员赋的值)，和执行构造方法。

**对象大小与指针压缩 **
	- 对象大小可以用jol­core包查看，引入依赖

```
	<dependency>
		<groupId>org.openjdk.jol</groupId> 
		<artifactId>jol‐core</artifactId>
		<version>0.9</version> 
	 </dependency>
```

- **什么是java对象的指针压缩?**
	- 1.jdk1.6 update14开始，在64bit操作系统中，JVM支持指针压缩 
	- 2.jvm配置参数:UseCompressedOops，compressed­­压缩、oop(ordinary object pointer)­­对象指针 
	- 3.启用指针压缩:­XX:+UseCompressedOops(默认开启)，禁止指针压缩:­XX:­UseCompressedOops
- **为什么要进行指针压缩? **
	- 1.在64位平台的HotSpot中使用32位指针，内存使用会多出1.5倍左右，使用较大指针在主内存和缓存之间移动数据， 占用较大宽带，同时GC也会承受较大压力
	- 2.为了减少64位平台下内存的消耗，启用指针压缩功能 
	- 3.在jvm中，32位地址最大支持4G内存(2的32次方)，可以通过对对象指针的压缩编码、解码方式进行优化，使得jvm 只用32位地址就可以支持更大的内存配置(小于等于32G) 
	- 4.堆内存小于4G时，不需要启用指针压缩，jvm会直接去除高32位地址，即使用低虚拟地址空间 
	- 5.堆内存大于32G时，压缩指针会失效，会强制使用64位(即8字节)来对java对象寻址，这就会出现1的问题，所以堆内存不要大于32G为好

### 对象内存分配：
#### 对象内存分配流程图：

![](https://gcore.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/%E5%AF%B9%E8%B1%A1%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D%E6%B5%81%E7%A8%8B%E5%9B%BE.png)
  
#### 对象栈上分配
- 我们通过JVM内存分配可以知道JAVA中的对象都是在堆上进行分配，当对象没有被引用的时候，需要依靠GC进行回收内存，如果对象数量较多的时候，会给GC带来较大压力，也间接影响了应用的性能。为了减少临时对象在堆内分配的数量，JVM通过逃逸分析确定该对象不会被外部访问。如果不会逃逸可以将该对象在栈上分配内存，这样该对象所占用的 内存空间就可以随栈帧出栈而销毁，就减轻了垃圾回收的压力。
- 对象逃逸分析:就是分析对象动态作用域，当一个对象在方法中被定义后，它可能被外部方法所引用，例如作为调用参数传递到其他地方中。

- JVM对于这种情况可以通过开启逃逸分析参数(-XX:+DoEscapeAnalysis)来优化对象内存分配位置，使其通过标量替换优 先分配在栈上(栈上分配)，JDK7之后默认开启逃逸分析，如果要关闭使用参数(-XX:-DoEscapeAnalysis)
-  **标量替换**:通过逃逸分析确定该对象不会被外部访问，并且对象可以被进一步分解时，JVM不会创建该对象，而是将该 对象成员变量分解若干个被这个方法使用的成员变量所代替，这些代替的成员变量在栈帧或寄存器上分配空间，这样就 不会因为没有一大块连续空间导致对象内存不够分配。开启标量替换参数(-XX:+EliminateAllocations)，JDK7之后默认 开启。 
-  **标量与聚合量**:标量即不可被进一步分解的量，而JAVA的基本数据类型就是标量(如:int，long等基本数据类型以及 reference类型等)，标量的对立就是可以被进一步分解的量，而这种量称之为聚合量。而在JAVA中对象就是可以被进一 步分解的聚合量。

结论:栈上分配依赖于逃逸分析和标量替换

#### 对象在Eden区分配
- 大多数情况下，对象在新生代中 Eden 区分配。当 Eden 区没有足够空间进行分配时，虚拟机将发起一次Minor GC。我 们来进行实际测试一下。
- 在测试之前我们先来看看 Minor GC和Full GC 有什么不同呢?
	- Minor GC/Young GC:指发生新生代的的垃圾收集动作，Minor GC非常频繁，回收速度一般也比较快。
	- Major GC/Full GC:一般会回收老年代 ，年轻代，方法区的垃圾，Major GC的速度一般会比Minor GC的慢 10倍以上。
- Eden与Survivor区默认8:1:1
	- 大量的对象被分配在eden区，eden区满了后会触发minor gc，可能会有99%以上的对象成为垃圾被回收掉，剩余存活 的对象会被挪到为空的那块survivor区，下一次eden区满了后又会触发minor gc，把eden区和survivor区垃圾对象回 收，把剩余存活的对象一次性挪动到另外一块为空的survivor区，因为新生代的对象都是朝生夕死的，存活时间很短，所 以JVM默认的8:1:1的比例是很合适的，让eden区尽量的大，survivor区够用即可， JVM默认有这个参数-XX:+UseAdaptiveSizePolicy(默认开启)，会导致这个8:1:1比例自动变化，如果不想这个比例有变 化可以设置参数-XX:-UseAdaptiveSizePolicy

<a href="https://gcore.jsdelivr.net/gh/wenhaoclub/blog-assets/files/java/jvm/03-VIP-JVM%E5%AF%B9%E8%B1%A1%E5%88%9B%E5%BB%BA%E4%B8%8E%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D%E6%9C%BA%E5%88%B6%E6%B7%B1%E5%BA%A6%E5%89%96%E6%9E%90.pdf" target="_blank">完整版内容链接</a>

> #### 大对象直接进入老年代：
> #### 长期存活的对象将进入老年代：
> #### 对象动态年龄判断：
> #### 老年代空间分配担保机制：
> ### 对象内存回收：
> #### 引用计数法：
> #### 可达性分析算法：
> #### 常见引用类型：
> #### finalize()方法最终判定对象是否存活：
> #### 如何判断一个类是无用的类：

