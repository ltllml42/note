---
title: Socket网络编程
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---

网络模型
TCP协议与UDP协议区别
Http协议底层实现原理。


# 网络模型 #
> 网络编程的本质是两个设备之间的数据交换，当然，在计算机网络中，设备主要指计算机。数据传递本身没有多大的难度，不就是把一个设备中的数据发送给两外一个设备，然后接受另外一个设备反馈的数据。
　　现在的网络编程基本上都是基于请求/响应方式的，也就是一个设备发送请求数据给另外一个，然后接收另一个设备的反馈。
　　在网络编程中，发起连接程序，也就是发送第一次请求的程序，被称作客户端(Client)，等待其他程序连接的程序被称作服务器(Server)。客户端程序可以在需要的时候启动，而服务器为了能够时刻相应连接，则需要一直启动。例如以打电话为例，首先拨号的人类似于客户端，接听电话的人必须保持电话畅通类似于服务器。
　　连接一旦建立以后，就客户端和服务器端就可以进行数据传递了，而且两者的身份是等价的。
　　在一些程序中，程序既有客户端功能也有服务器端功能，最常见的软件就是BT、emule这类软件了。
下面来谈一下如何建立连接以及如何发送数据。


## TCP协议
## UDP协议


# 网络模型

![网络模型图](https://github.com/ltllml42/img/2019/10/29/1572335099095.png)





# Socket
Socket就是为网络服务提供的一种机制。
数据在两个Sokcet间通过IO传输。网络通讯其实就是Sokcet间的通讯


## TCP与UDP在概念上的区别：
udp: a、是面向无连接, 将数据及源的封装成数据包中,不需要建立连接
    b、每个数据报的大小在限制64k内
    c、因无连接,是不可靠协议
    d、不需要建立连接,速度快
tcp： a、建议连接，形成传输数据的通道.
    b、在连接中进行大数据量传输，以字节流方式
    c 通过三次握手完成连接,是可靠协议
    d 必须建立连接m效率会稍低
   
   
   UDP服务器端代码
   ```java
   class UdpSocketServer {

	public static void main(String[] args) throws IOException {
		System.out.println("udp服务器端启动连接....");
		DatagramSocket ds = new DatagramSocket(8080);
		byte[] bytes = new byte[1024];
		DatagramPacket dp = new DatagramPacket(bytes, bytes.length);
		// 阻塞,等待接受客户端发送请求
		ds.receive(dp);
		System.out.println("来源:"+dp.getAddress()+",端口号:"+dp.getPort());
		// 获取客户端请求内容
		String str=new String(dp.getData(),0,dp.getLength());
		System.out.println("str:"+str);
		ds.close();
	}

}
   
   
   ```
   