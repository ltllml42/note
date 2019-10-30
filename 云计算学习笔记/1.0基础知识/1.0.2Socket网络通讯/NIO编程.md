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

### 缓冲区

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
 

#### 非直接缓冲区


 **非直接缓冲区**：通过 allocate() 方法分配缓冲区，将缓冲区建立在 JVM 的内存中
 
![非直接缓冲区](https://github.com/ltllml42/img/2019/10/29/1572352552568.png)

 ```java
 //利用通道完成文件的复制（非直接缓冲区）
	@Test
	public void test1(){//10874-10953
		long start = System.currentTimeMillis();
		
		FileInputStream fis = null;
		FileOutputStream fos = null;
		//①获取通道
		FileChannel inChannel = null;
		FileChannel outChannel = null;
		try {
			fis = new FileInputStream("d:/1.mkv");
			fos = new FileOutputStream("d:/2.mkv");
			
			inChannel = fis.getChannel();
			outChannel = fos.getChannel();
			
			//②分配指定大小的缓冲区
			ByteBuffer buf = ByteBuffer.allocate(1024);
			
			//③将通道中的数据存入缓冲区中
			while(inChannel.read(buf) != -1){
				buf.flip(); //切换读取数据的模式
				//④将缓冲区中的数据写入通道中
				outChannel.write(buf);
				buf.clear(); //清空缓冲区
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			if(outChannel != null){
				try {
					outChannel.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
			
			if(inChannel != null){
				try {
					inChannel.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
			
			if(fos != null){
				try {
					fos.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		
			if(fis != null){
				try {
					fis.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		}	
		long end = System.currentTimeMillis();
		System.out.println("耗费时间为：" + (end - start));
	}
 ```



直接缓冲区和非直接缓冲区的区别在于 非直接缓冲区无需拷贝（通过物理映射文件进行）
#### 直接缓冲区
 **直接缓冲区**：
 通过 allocateDirect() 方法分配直接缓冲区，将缓冲区建立在物理内存中。可以
![直接缓冲区](https://github.com/ltllml42/img/2019/10/29/1572352560939.png)
代码
```java?linenums
//使用直接缓冲区完成文件的复制(内存映射文件)
	@Test
	public void test2() throws IOException{//2127-1902-1777
		long start = System.currentTimeMillis();
		
		FileChannel inChannel = FileChannel.open(Paths.get("d:/1.mkv"), StandardOpenOption.READ);
		FileChannel outChannel = FileChannel.open(Paths.get("d:/2.mkv"), StandardOpenOption.WRITE, StandardOpenOption.READ, StandardOpenOption.CREATE);
		
		//内存映射文件
		MappedByteBuffer inMappedBuf = inChannel.map(MapMode.READ_ONLY, 0, inChannel.size());
		MappedByteBuffer outMappedBuf = outChannel.map(MapMode.READ_WRITE, 0, inChannel.size());
		
		//直接对缓冲区进行数据的读写操作
		byte[] dst = new byte[inMappedBuf.limit()];
		inMappedBuf.get(dst);
		outMappedBuf.put(dst);
		
		inChannel.close();
		outChannel.close();
		
		long end = System.currentTimeMillis();
		System.out.println("耗费时间为：" + (end - start));
	}

```
## 通道（Channel）
 通道（Channel）：由 java.nio.channels 包定义 的。Channel 表示 IO 源与目标打开的连接。 Channel 类似于传统的“流”。只不过 Channel 本身不能直接访问数据，Channel 只能与 Buffer 进行交互
 ![](https://github.com/ltllml42/img/2019/10/30/1572398751359.png)

### Java提供的主要实现类
**FileChannel**：用于读取、写入、映射和操作文件的通道。
**DatagramChannel**：通过 UDP 读写网络中的数据通道。
**SocketChannel**：通过 TCP 读写网络中的数据。
**ServerSocketChannel**：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。

### 获取通道的方式
#### getChannel()方法;
#### Files.newByteChannel();


### 分散和聚集
分散读取（Scattering Reads）是指从 Channel 中读取的数据“分 散”到多个 Buffer 中。

![enter description here](https://github.com/ltllml42/img/2019/10/30/1572422733993.png)

聚集写入（Gathering Writes）是指将多个 Buffer 中的数据“聚集” 到 Channel
![enter description here](https://github.com/ltllml42/img/2019/10/30/1572422786139.png)

代码
```java?linenums

	@Test
	public void test4() throws IOException{
		RandomAccessFile raf1 = new RandomAccessFile("1.txt", "rw");
		
		//1. 获取通道
		FileChannel channel1 = raf1.getChannel();
		
		//2. 分配指定大小的缓冲区
		ByteBuffer buf1 = ByteBuffer.allocate(100);
		ByteBuffer buf2 = ByteBuffer.allocate(1024);
		
		//3. 分散读取
		ByteBuffer[] bufs = {buf1, buf2};
		channel1.read(bufs);
		
		for (ByteBuffer byteBuffer : bufs) {
			byteBuffer.flip();
		}		
		System.out.println(new String(bufs[0].array(), 0, bufs[0].limit()));
		System.out.println("-----------------");
		System.out.println(new String(bufs[1].array(), 0, bufs[1].limit()));
	
		//4. 聚集写入
		RandomAccessFile raf2 = new RandomAccessFile("2.txt", "rw");
		channel2.write(bufs);
	}

```



### 通道之间的数据传输
transferFrom()
transferTo()

### 字符集转化
```javascript?linenums
	//字符集
	@Test
	public void test6() throws IOException{
		Charset cs1 = Charset.forName("GBK");
		//获取编码器
		CharsetEncoder ce = cs1.newEncoder();
		//获取解码器
		CharsetDecoder cd = cs1.newDecoder();
		CharBuffer cBuf = CharBuffer.allocate(1024);
		cBuf.put("尚硅谷威武！");
		cBuf.flip();
		
		//编码
		ByteBuffer bBuf = ce.encode(cBuf);
		
		for (int i = 0; i < 12; i++) {
			System.out.println(bBuf.get());
		}
		
		//解码
		bBuf.flip();
		CharBuffer cBuf2 = cd.decode(bBuf);
		System.out.println(cBuf2.toString());
		
		System.out.println("------------------------------------------------------");
		
		Charset cs2 = Charset.forName("GBK");
		bBuf.flip();
		CharBuffer cBuf3 = cs2.decode(bBuf);
		System.out.println(cBuf3.toString());
	}

```




## 案例：
```java
// 使用直接缓冲区完成文件的复制(内存映射文件)
	static public void test2() throws IOException {
		long start = System.currentTimeMillis();
		FileChannel inChannel = FileChannel.open(Paths.get("f://1.mp4"), StandardOpenOption.READ);
		FileChannel outChannel = FileChannel.open(Paths.get("f://2.mp4"), StandardOpenOption.WRITE,
				StandardOpenOption.READ, StandardOpenOption.CREATE);
		// 内存映射文件
		MappedByteBuffer inMappedByteBuf = inChannel.map(MapMode.READ_ONLY, 0, inChannel.size());
		MappedByteBuffer outMappedByteBuffer = outChannel.map(MapMode.READ_WRITE, 0, inChannel.size());
		// 直接对缓冲区进行数据的读写操作
		byte[] dsf = new byte[inMappedByteBuf.limit()];
		inMappedByteBuf.get(dsf);
		outMappedByteBuffer.put(dsf);
		inChannel.close();
		outChannel.close();
		long end = System.currentTimeMillis();
		System.out.println(end - start);
	}

	// 1.利用通道完成文件的复制(非直接缓冲区)
	static public void test1() throws IOException { // 4400
		long start = System.currentTimeMillis();
		FileInputStream fis = new FileInputStream("f://1.mp4");
		FileOutputStream fos = new FileOutputStream("f://2.mp4");
		// ①获取通道
		FileChannel inChannel = fis.getChannel();
		FileChannel outChannel = fos.getChannel();
		// ②分配指定大小的缓冲区
		ByteBuffer buf = ByteBuffer.allocate(1024);
		while (inChannel.read(buf) != -1) {
			buf.flip();// 切换为读取数据
			// ③将缓冲区中的数据写入通道中
			outChannel.write(buf);
			buf.clear();
		}
		outChannel.close();
		inChannel.close();
		fos.close();
```

## NIO 网络通信


**传统的 IO 流都是阻塞式的**。也就是说，当一个线程调用 read() 或 write() 时，该线程被阻塞，直到有一些数据被读取或写入，该线程在此期间不 能执行其他任务。因此，在完成网络通信进行 IO 操作时，由于线程会 阻塞，所以服务器端必须为每个客户端都提供一个独立的线程进行处理， 当服务器端需要处理大量客户端时，性能急剧下降。

```java?linenums
   @Test
    public void client() throws IOException {
        //获取通道
        SocketChannel socket = SocketChannel.open(new InetSocketAddress("127.0.0.1",8080));
        /**
         * java.nio.file.StandardOpenOption
         *
         * READ	    以读取方式打开文件
         * WRITE　　	已写入方式打开文件
         * CREATE	如果文件不存在，创建
         * CREATE_NEW	如果文件不存在，创建；若存在，异常。
         * APPEND	在文件的尾部追加
         * DELETE_ON_CLOSE	当流关闭的时候删除文件
         * TRUNCATE_EXISTING	把文件设置为0字节
         * SPARSE	文件不够时创建新的文件
         * SYNC	同步文件的内容和元数据信息随着底层存储设备
         * DSYNC	同步文件的内容随着底层存储设备
         *
         */
        FileChannel fileChannel = FileChannel.open(Paths.get("D:\\ideaproject\\jeeSpringCloud\\cloud\\cloud-base\\src\\main\\java\\1.txt"), StandardOpenOption.READ);

        //2.分配指定大小的缓冲区
        ByteBuffer buf = ByteBuffer.allocate(1024);
        //3.读取本地文件，并发送到服务端
        while (fileChannel.read(buf)!=-1){
            //将缓冲器切换到读模式
            buf.flip();
            socket.write(buf);
            buf.clear();
        }
		
		socket.shutdownOutput();
        //关闭通道
        fileChannel.close();
        socket.close();
    }

    @Test
    public void server() throws IOException {
        //1.获取通道
        ServerSocketChannel ssc = ServerSocketChannel.open();
        //2.绑定连接
        ssc.bind(new InetSocketAddress(8080));
        //3.获取客户端连接的通道
        SocketChannel sChannel = ssc.accept();

        FileChannel file = FileChannel.open(Paths.get("D:\\ideaproject\\jeeSpringCloud\\cloud\\cloud-base\\src\\main\\java\\2.txt"),StandardOpenOption.WRITE,StandardOpenOption.CREATE);

        ByteBuffer buf = ByteBuffer.allocate(1024);

        //4.分配指定大小的缓冲区
        while(sChannel.read(buf)!=-1){
            buf.flip();
            //5.接受客户端的数据，并保存到本地
            file.write(buf);
            buf.clear();
        }
        //6.关闭通道
        file.close();
        sChannel.close();
    }

```



**Java NIO 是非阻塞模式的**。当线程从某通道进行读写数据时，若没有数 据可用时，该线程可以进行其他任务。线程通常将非阻塞 IO 的空闲时 间用于在其他通道上执行 IO 操作，所以单独的线程可以管理多个输入 和输出通道。因此，NIO 可以让服务器端使用一个或有限几个线程来同 时处理连接到服务器端的所有客户端。


```java?linenums

/**
 * 非阻塞模式
 */
public class SocketDemo {

    @Test
    public void client() throws IOException {
        //1. 获取通道
        SocketChannel s = SocketChannel.open(new InetSocketAddress("127.0.0.1",8080));
        //2. 切换非阻塞模式
        s.configureBlocking(false);
        //3.分配指定大小的缓冲区
        ByteBuffer bb = ByteBuffer.allocate(1024);
        //4.发送数据给服务端
        bb.put(new Date().toString().getBytes());
        bb.flip();
        s.write(bb);
        bb.clear();
        s.close();

    }

    @Test
    public void server() throws IOException {
        //1.获取通道
        ServerSocketChannel ssc = ServerSocketChannel.open();
        //2.切换非阻塞模式
        ssc.configureBlocking(false);
        //3.绑定连接
        ssc.bind(new InetSocketAddress(8080));
        //4.获取选择器
        Selector selector = Selector.open();
        //5.将通道注册到选择器
        ssc.register(selector, SelectionKey.OP_ACCEPT);
        //6.轮询式的获取选择器上已经“准备就绪”的事件
        while(selector.select()>0){
            //7.获取当前选择器中所有注册的“选择键（已经及就绪的监听事件）”
            Iterator<SelectionKey> it = selector.selectedKeys().iterator();
            while(it.hasNext()){
                //8.获取准备“就绪”的事件
                SelectionKey sk = it.next();
                //9.判断具体是什么事件准备就绪
                if(sk.isAcceptable()){
                    //10.若“接收就绪”，获取客户端连接
                    SocketChannel sChannel = ssc.accept();
                    //11.切换费阻塞模式
                    sChannel.configureBlocking(false);
                    //12.将该通道注册到选择器上
                    sChannel.register(selector,SelectionKey.OP_READ);
                }else if(sk.isReadable()){
                    //13.获取当前选择器上“读就绪”状态的通道
                    SocketChannel sChannel = (SocketChannel) sk.channel();
                    //14.读取数据
                    ByteBuffer buf = ByteBuffer.allocate(1024);
                    int len = 0;
                    while((len = sChannel.read(buf))>0){
                        buf.flip();
                        System.out.println(new String(buf.array(),0,len));
                        buf.clear();
                    }
                }
                //15.取消选择键
                it.remove();
            }
        }
    }
}

```







