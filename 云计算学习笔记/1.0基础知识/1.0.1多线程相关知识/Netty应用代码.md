---
title: Netty应用代码
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---

# Netty学习代码注释

```java
public class AllocateBuffer {
    public static void main(String[] args) {
        System.out.println("----------Test allocate--------");
        System.out.println("before alocate:"+Runtime.getRuntime().freeMemory());
        //堆上分配
        ByteBuffer buffer = ByteBuffer.allocate(10240);
        System.out.println(buffer);
        System.out.println("after alocate:"+Runtime.getRuntime().freeMemory());
        //用直接内存分配
        ByteBuffer directBuffer = ByteBuffer.allocateDirect(10240);
        System.out.println(directBuffer);
        System.out.println("after direct alocate:"+Runtime.getRuntime().freeMemory());
        System.out.println("----------Test wrap--------");
        byte[] bytes = new byte[32];
    }
}



```













