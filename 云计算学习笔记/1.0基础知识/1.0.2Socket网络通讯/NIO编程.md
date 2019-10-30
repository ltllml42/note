---
title: NIO编程 
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---


[TOC!]

# 概述
## 什么是NIO(是非阻塞的) 

Java NIO(New IO)是一个可以替代标准Java IO API的IO API（从Java 1.4开始)，Java NIO提供了与标准IO不同的IO工作方式。
Java NIO: Channels and Buffers（通道和缓冲区）
标准的IO基于字节流和字符流进行操作的，而NIO是基于通道（Channel）和缓冲区（Buffer）进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。
Java NIO: Non-blocking IO（非阻塞IO）
Java NIO可以让你非阻塞的使用IO，例如：当线程从通道读取数据到缓冲区时，线程还是可以进行其他事情。当数据被写入到缓冲区时，线程可以继续处理它。从缓冲区写入通道也类似。
Java NIO: Selectors（选择器）
Java NIO引入了选择器的概念，选择器用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个的线程可以监听多个数据通道。
注意:传统IT是单向。


![图片1](https://github.com/ltllml42/img/2019/10/29/1572338582395.png)


![图片2](https://github.com/ltllml42/img/2019/10/29/1572338595653.png)


![图片3](https://github.com/ltllml42/img/2019/10/29/1572338603374.png)


区别：

| IO     | NIO        |
| ------ | ---------- |
| 面向流 | 面向缓冲区 |
| 阻塞IO | 非阻塞IO   |
| 无     | 选择器     |


## 1-通道（Channel）与缓冲区（Buffer）
Java NIO系统的核心在于：通道(Channel)和缓冲区 (Buffer)。通道表示打开到 IO 设备(例如：文件、 套接字)的连接。若需要使用 NIO 系统，需要获取 用于连接 IO 设备的通道以及用于容纳数据的缓冲 区。然后操作缓冲区，对数据进行处理

## 缓冲区

缓冲区（Buffer）：一个用于特定基本数据类 型的容器。由 java.nio 包定义的，所有缓冲区 都是 Buffer 抽象类的子类。

Java NIO 中的 Buffer 主要用于与 NIO 通道进行 交互，数据是从通道读入缓冲区，从缓冲区写
入通道中的。
Buffer 就像一个数组，可以保存多个相同类型的数据。根 据数据类型不同(boolean 除外) ，有以下 Buffer 常用子类：  
**ByteBuffer** 
**CharBuffer** 
**ShortBuffer** 
**IntBuffer** 
**LongBuffer** 
**FloatBuffer** 
**DoubleBuffer** 
 上述缓冲区的管理方式几乎一致，通过 allocate() 获取缓冲区
二、缓冲区存取数据的两个核心方法：
put() : 存入数据到缓冲区中
get() : 获取缓冲区中的数据
三、缓冲区中的四个核心属性：
capacity : 容量，表示缓冲区中最大存储数据的容量。一旦声明不能改变。
limit : 界限，表示缓冲区中可以操作数据的大小。（limit 后数据不能进行读写）
position : 位置，表示缓冲区中正在操作数据的位置。
mark : 标记，表示记录当前 position 的位置。可以通过 reset() 恢复到 mark 的位置
 0 <= mark <= position <= limit <= capacity
 

![enter description here](https://github.com/ltllml42/img/2019/10/29/1572340634991.png)
 ![常用方法](https://github.com/ltllml42/img/2019/10/29/1572352389464.png)
 
 ```java
/**
 * (缓冲区)buffer 用于NIO存储数据 支持多种不同的数据类型 <br>
 * 1.byteBuffer <br>
 * 2.charBuffer <br>
 * 3.shortBuffer<br>
 * 4.IntBuffer<br>
 * 5.LongBuffer<br> 
 * 6.FloatBuffer <br>
 * 7.DubooBuffer <br>
 * 上述缓冲区管理的方式 几乎<br>
 * 通过allocate（） 获取缓冲区 <br>
 * 二、缓冲区核心的方法 put 存入数据到缓冲区 get <br> 获取缓冲区数据 flip 开启读模式
 * 三、缓冲区四个核心属性<br>
 * capacity:缓冲区最大容量，一旦声明不能改变。 limit:界面(缓冲区可以操作的数据大小) limit后面的数据不能读写。
 * position:缓冲区正在操作的位置
 */
public class Test004 {

	public static void main(String[] args) {
		// 1.指定缓冲区大小1024
		ByteBuffer buf = ByteBuffer.allocate(1024);
		System.out.println("--------------------");
		System.out.println(buf.position());
		System.out.println(buf.limit());
		System.out.println(buf.capacity());
		// 2.向缓冲区存放5个数据
		buf.put("abcd1".getBytes());
		System.out.println("--------------------");
		System.out.println(buf.position());
		System.out.println(buf.limit());
		System.out.println(buf.capacity());
		// 3.开启读模式
		buf.flip();
		System.out.println("----------开启读模式...----------");
		System.out.println(buf.position());
		System.out.println(buf.limit());
		System.out.println(buf.capacity());
		byte[] bytes = new byte[buf.limit()];
		buf.get(bytes);
		System.out.println(new String(bytes, 0, bytes.length));
		System.out.println("----------重复读模式...----------");
		// 4.开启重复读模式
		buf.rewind();
		System.out.println(buf.position());
		System.out.println(buf.limit());
		System.out.println(buf.capacity());
		byte[] bytes2 = new byte[buf.limit()];
		buf.get(bytes2);
		System.out.println(new String(bytes2, 0, bytes2.length));
		// 5.clean 清空缓冲区  数据依然存在,只不过数据被遗忘
		System.out.println("----------清空缓冲区...----------");
		buf.clear();
		System.out.println(buf.position());
		System.out.println(buf.limit());
		System.out.println(buf.capacity());
		System.out.println((char)buf.get());
	}

}

```
 

### 非直接缓冲区


 **非直接缓冲区**：通过 allocate() 方法分配缓冲区，将缓冲区建立在 JVM 的内存中
 


提高效率
 
![非直接缓冲区](https://github.com/ltllml42/img/2019/10/29/1572352552568.png)

直接缓冲区和非直接缓冲区的区别在于 非直接缓冲区无需拷贝（通过物理映射文件进行）
### 直接缓冲区
 **直接缓冲区**：
 通过 allocateDirect() 方法分配直接缓冲区，将缓冲区建立在物理内存中。可以
![直接缓冲区](https://github.com/ltllml42/img/2019/10/29/1572352560939.png)