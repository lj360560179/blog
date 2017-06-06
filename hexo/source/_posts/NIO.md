---
layout: post
comments: true
title: NIO AIO
tag: 
- java
category: java
date: 2017.6.6 00:34:14 
---
# NIO
### 什么是NIO
NIO是New I/O的简称，与旧式的基于流的I/O方法相对，从名字看，它表示新的一套Java I/O标
准。它是在Java 1.4中被纳入到JDK中的，并具有以下特性：
* NIO是基于块（Block）的，它以块为基本单位处理数据
* 为所有的原始类型提供（Buffer）缓存支持
* 增加通道（Channel）对象，作为新的原始 I/O 抽象
* 支持锁和内存映射文件的文件访问接口
* 提供了基于Selector的异步网络I/O
<!-- more -->
### Buffer && Channel
```java
FileInputStream fin = new FileInputStream(new File("d:\\temp_buffer.tmp"));
FileChannel fc=fin.getChannel();


ByteBuffer byteBuffer=ByteBuffer.allocate(1024);
fc.read(byteBuffer);


fc.close();
byteBuffer.flip();
```

```java
public class NioAndAio {

    public static void nioCopyFile(String resource, String destination)
            throws IOException {
        FileInputStream fis = new FileInputStream(resource);
        FileOutputStream fos = new FileOutputStream(destination);
        FileChannel readChannel = fis.getChannel(); //读文件通道
        FileChannel writeChannel = fos.getChannel(); //写文件通道
        ByteBuffer buffer = ByteBuffer.allocate(1024); //读入数据缓存
        while (true) {
            buffer.clear();
            int len = readChannel.read(buffer); //读入数据
            if (len == -1) {
                break;
                //读取完毕
            }
            buffer.flip();
            writeChannel.write(buffer);
            //写入文件
        }
        readChannel.close();
        writeChannel.close();
    }
    public static void main (String[] args){
        NioAndAio demo = new NioAndAio();
        try{
            demo.nioCopyFile("E:\\git\\blog\\blog\\README.md","E:\\git\\blog\\blog\\README2.md");
        }catch ( IOException e){
            e.printStackTrace();
        }
    }
}
```

```java
public final Buffer rewind()
``` 
* 将position置零，并清除标志位（mark）
```java
 public final Buffer clear()
``` 

* 将position置零，同时将limit设置为capacity的大小，并清除了标志mark
```java
 public final Buffer flip()
``` 
* 先将limit设置到position所在位置，然后将position置零，并清除标志位mark
通常在读写转换时使用




