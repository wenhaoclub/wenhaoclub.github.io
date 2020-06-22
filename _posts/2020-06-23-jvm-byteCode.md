---
layout: post
title: Java字节码分析
categories: [Java, Jvm]
description: java字节码分析
keywords: wenhao  , 文浩  , 似水似流年  , wenhaoclub  , fuwenhao.club , 文浩的博客 , java , jvm  ,  字节码
---


## 序：

> 了解VM字节码对于开发人员，可以更准确、直观地理解Java语言中更深层次的东西。

## 准备：
- Java代码如下：

```
public class FwhByteCode {
    private String userName;

    public String getUserName() {

        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }
}
```

- Java反编译如下：

```
javap -v FwhByteCode.class 
Classfile /Users/fwh/A_FWH/GItHub/fwh-JVM/src/main/java/club/fuwenhao/FwhByteCode.class
  Last modified 2020-6-22; size 426 bytes
  MD5 checksum 60e2a803967b906ca43ac8a38e0d8113
  Compiled from "FwhByteCode.java"
public class club.fuwenhao.FwhByteCode
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #4.#17         // java/lang/Object."<init>":()V
   #2 = Fieldref           #3.#18         // club/fuwenhao/FwhByteCode.userName:Ljava/lang/String;
   #3 = Class              #19            // club/fuwenhao/FwhByteCode
   #4 = Class              #20            // java/lang/Object
   #5 = Utf8               userName
   #6 = Utf8               Ljava/lang/String;
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               getUserName
  #12 = Utf8               ()Ljava/lang/String;
  #13 = Utf8               setUserName
  #14 = Utf8               (Ljava/lang/String;)V
  #15 = Utf8               SourceFile
  #16 = Utf8               FwhByteCode.java
  #17 = NameAndType        #7:#8          // "<init>":()V
  #18 = NameAndType        #5:#6          // userName:Ljava/lang/String;
  #19 = Utf8               club/fuwenhao/FwhByteCode
  #20 = Utf8               java/lang/Object
{
  public club.fuwenhao.FwhByteCode();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 10: 0

  public java.lang.String getUserName();
    descriptor: ()Ljava/lang/String;
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: getfield      #2                  // Field userName:Ljava/lang/String;
         4: areturn
      LineNumberTable:
        line 15: 0

  public void setUserName(java.lang.String);
    descriptor: (Ljava/lang/String;)V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0
         1: aload_1
         2: putfield      #2                  // Field userName:Ljava/lang/String;
         5: return
      LineNumberTable:
        line 19: 0
        line 20: 5
}
SourceFile: "FwhByteCode.java"

```

- 二进制编辑器(EditPlus)打开如下：

```
cafe babe 0000 0034 0015 0a00 0400 1109
0003 0012 0700 1307 0014 0100 0875 7365
724e 616d 6501 0012 4c6a 6176 612f 6c61
6e67 2f53 7472 696e 673b 0100 063c 696e
6974 3e01 0003 2829 5601 0004 436f 6465
0100 0f4c 696e 654e 756d 6265 7254 6162
6c65 0100 0b67 6574 5573 6572 4e61 6d65
0100 1428 294c 6a61 7661 2f6c 616e 672f
5374 7269 6e67 3b01 000b 7365 7455 7365
724e 616d 6501 0015 284c 6a61 7661 2f6c
616e 672f 5374 7269 6e67 3b29 5601 000a
536f 7572 6365 4669 6c65 0100 1046 7768
4279 7465 436f 6465 2e6a 6176 610c 0007
0008 0c00 0500 0601 0019 636c 7562 2f66
7577 656e 6861 6f2f 4677 6842 7974 6543
6f64 6501 0010 6a61 7661 2f6c 616e 672f
4f62 6a65 6374 0021 0003 0004 0000 0001
0002 0005 0006 0000 0003 0001 0007 0008
0001 0009 0000 001d 0001 0001 0000 0005
2ab7 0001 b100 0000 0100 0a00 0000 0600
0100 0000 0a00 0100 0b00 0c00 0100 0900
0000 1d00 0100 0100 0000 052a b400 02b0
0000 0001 000a 0000 0006 0001 0000 000f
0001 000d 000e 0001 0009 0000 0022 0002
0002 0000 0006 2a2b b500 02b1 0000 0001
000a 0000 000a 0002 0000 0013 0005 0014
0001 000f 0000 0002 0010 
```

## 教程分析：
![](https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/images/Java/JVM/class_contant.png)

- 参照class文件结构图分析：
    - 魔数: 文件的开头的 四个字节 是固定 值位 0xCAFEBABE
    - 次版本号(minor version):二个字节00 00 表示jdk的次版本号
    - 主版本号(major version):二个字节 00 34 表示为jdk的主版本号，34对于10 进制为52 
    - 那么52代表的是1.8，51代表的是1.7 等等一直类推下去
    - 常量池入口，占用二个字节,表示常量池中的个数=00 19 (25)-1=24个, 为啥 需要-1，因为常量池中的第0个位置被我们的jvm占用了表示为null 所以我们通过 编译出来的常量池索引是从1开始的

- 常量池结构表如图所示：
    - u1,u2,u4,u8分别代表1个字节,2个字节,4个字节,8个字节的无符号数
    - 对照结构图分析：
        - ~~具体几个字节为一段，需要查看对应tag上“index值的长度”~~
        - 第一个是标志位，第二个需要参照对应表解释长度。一般为u1,u2,u4,u8
        - 前面07开头中，其中07表示标志位-需要对应结构表图中tag编号。
        - 记住-其中00表示16进制的值为“16”
        - 对照计算出的位置，就是反编译中“Constant pool:”的具体位置数值。

- 粘贴出部分字节码属性：

```
"method_count":"u2(00 03)->desc:表示我们的方法的个数为三个",
  "method_infos": {
    "method_info[0]": {
      "access_flag": "u2(00 01)->desc:表示我们的方法访问权限为ACC_PUBLIC",
      "name_index": "u2(00 07)->desc:表示方法名称的索引指向常量池第七个位置#7 表示<init> 再编译时期生成的默认的构造函数",
      "descciptor_index": "u2(00 08)->desc:表示方法描述索引指向常量池#8 表示:()V 无参数,无返回值",
      "attribute_count": "u2(00 01)->desc:表示方法的属性个数",
      "attribute_infos": {
        "Code": {
          "attribute_name_index": "u2(00 09)->desc:我们属性的名称指向常量值索引的#9 位置 值为Code",
          "attribute_length": "u4(00 00 00 2F)-desc:表示我们的Code属性紧接着下来的47个字节是Code的内容",
          "max_stack": "u2(00 01)->desc：表示该方法的最大操作数栈的深度1",
          "max_locals": "u2(00 01)->desc:表示该方法的局部变量表的个数为1",
          "Code_length": "u4(00 00 00 05)->desc:指令码的长度为5",
          "Code[Code_length]": "2A B7 00 01 B1 其中0x002A->对应的字节码注记符是aload_0;0xB7->invokespecial;00 01表示表示是B7指令码操作的对象指向常量池中的#1(java/lang/Object.<init>:()V);B1表示调用方法返回void",
          "exception_table_length": "u2(00 00)->表示该方法不抛出异常,故exception_info没有异常信息",
          "exception_info": {},
          "attribute_count": "u2(00 02)->desc表示code属性表的属性个数为2",
          "attribute_info": {
            "LineNumberTable": {
              "attribute_name_index": "u2(00 0A)当前属性表名称的索引指向我们的常量池#10(LineNumberTable)",
              "attribute_length": "u4(00 00 00 06)当前属性表属性的字段占用6个字节是用来描述line_number_info",
              "mapping_count": "u2(00 01)->desc:该方法指向的指令码和源码映射的对数  表示一对",
              "line_number_infos": {
                "line_number_info[0]": {
                  "start_pc": "u2(00 00)->desc:表示指令码的行数",
                  "line_number": "u2(00 06)->desc:源码的行号"
                }
              }
            },
            "localVariableTable": {
              "attribute_name_index": "u2(00 0B)当前属性表名称的索引指向我们的常量池#10(localVariableTable)",
              "attribute_length": "u4(00  00 00 0C)当前属性表属性的字段占用12个字节用来描述local_variable_info",
              "local_variable_length": "u2(00 01)->desc:表示局部变量的个数1个",
              "local_variable_infos": {
                "local_variable_info[0]": {
                  "start_pc": "u2(00 00 )->desc:这个局部变量的生命周期开始的字节码偏移量",
                  "length:": "u2(00 05)->作用范围覆盖的长度为5",
                  "name_index": "u2(00 0c)->字段的名称索引指向常量池12的位置 this",
                  "desc_index": "u2(00 0D)局部变量的描述符号索引->指向#13的位置Lcom/tuling/smlz/jvm/classbyatecode/TulingByteCode;",
                  "index": "u2(00 00)->desc:index是这个局部变量在栈帧局部变量表中Slot的位置"
                }
              }
            }
          }
        }
      }
    }
```
- 注意：里面指的是操作数栈的深度。

## 琐碎知识点：
- javap -verbose **.class  进行反编译
- 固定值：0xCAFEBABE魔数的固定值是Java之父JamesGosling制定的，为CafeBabe（咖啡宝贝）
- 相关idea插件：Jclasslib
- 十六进制数的基数是16，采用的数码是0、1、2、3、4、5、6、7、8、9、A、B、C、D、E、F。其中A-F分别表示十进制数字10-15.

## 问题：
- 局部变量表是在什么时候确定的？
    - 再编译的时候确定，而不是运行的时候。
- 为什么再实例的方法中可以调用this？
    - 因为隐式的入参，放在局部变量表的第一的位置。
- Java中最多能实现多少个接口？
    - java中的动态代理中有判断不能超过65535
    - 即FFFF：15*16^3+15*16^2+15*16^1+15 == 65535

## 相关链接：
- [字节码增强技术探索 ](https://tech.meituan.com/2019/09/05/java-bytecode-enhancement.html)
- [深入理解Java虚拟机.pdf](https://pan.baidu.com/s/1iPLX2VTW93wScyNVlcQkGA) （可复制版）

## 总结：
<a href="https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/files/java/jvm/class%E6%96%87%E4%BB%B6%E7%BB%93%E6%9E%84V4.pdf" target="_blank">完整版文档地址</a>

<a href="https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/files/java/jvm/TulingByteCode.json" target="_blank">完整版分析json数据</a>

<a href="https://cdn.jsdelivr.net/gh/wenhaoclub/blog-assets/files/java/jvm/class%E6%96%87%E4%BB%B6%E7%BB%93%E6%9E%84%E5%8F%82%E7%85%A7%E8%A1%A8%E5%85%A8%E9%9B%86.pdf" target="_blank">class文件结构参照表全集</a>

<a href="https://www.processon.com/view/link/5eb50263e401fd16f42345f4" target="_blank">一网打尽各种常量池</a>


