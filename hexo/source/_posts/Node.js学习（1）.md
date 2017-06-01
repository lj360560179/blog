---
title: node
layout: post
comments: true
tag: 
- node.js
category: 前端
date: 2016.5.25 15:44:14
---
### 特点
##### 单线程
&ensp;&ensp;&ensp;&ensp;node.js不为每个客户连接创建一个新的线程，仅仅使用一个线程，当有用户连接，就促发一个内部事件，通过费阻塞I/O、事件驱动机制，让Node.js程序宏观上也是并行的。而且没有线程创建、销毁的时间开销。
<!-- more -->
##### 非阻塞I/O
&ensp;&ensp;&ensp;&ensp;不会等I/O语句结束，而会执行后面的语句。
##### 事件驱动
&ensp;&ensp;&ensp;&ensp;不管是新用户的请求，还是老用户的I/O完成，都将以事件方式加入事件环，等待调度。
![](http://ni484sha.com/images/node_1.png)
### 适合做什么
&ensp;&ensp;&ensp;&ensp;善于I/O，不善于计算。因为Node.js最擅长的就是任务调度，如果你的业务有很多的CPU计算，实际上也相当于这个计算阻塞了这个单线程，就不适合Node开发。