---
layout: post
comments: true
title: GC相关
tag: 
- java
- jvm
- GC
category: java
date: 2017.6.14  
---

### GC 参数
#### 堆的回顾

![](http://ni484sha.com/images/gcc1.png)

#### GC参数 - 串行收集器

* 最古老，最稳定
* 效率高
* 可能会产生较长的停顿
* -XX:+UseSerialGC
* 新生代、老年代使用串行回收
* 新生代复制算法  
* 老年代标记-压缩

![](http://ni484sha.com/images/gcc2.png)


#### GC参数 -并行收集器

##### ParNew收集器
  - -XX:+UseParNewGC()
    * 新生代并行
    * 老年代串行
  - Serial收集器新生代的并行版本
  - 复制算法
  - 多线程，需要多核支持
  - -XX:ParallelGCThreads 限制线程数量

多线程不一定快哦！

![](http://ni484sha.com/images/gcc99.png)



* Parallel收集器
 * 类似ParNew  
 * 新生代复制算法  
 * 老年代 标记-压缩  
 * 更加关注吞吐量
 * XX:+UseParallelGC  
  - 使用Parallel收集器 + 老年代串行  
 * XX:+UseParallelOldGC
  - 使用Parallel收集器 + 并行老年代

![](http://ni484sha.com/images/gcc3.png)

* -XX:MaxGCPauseMills
 - 最大停顿时间，单位毫秒  
 - GC尽力保证回收时间不超过设定值
* -XX:GCTimeRatio
 - 0-100的取值范围
 - 垃圾收集时间占总时间的比
 - 默认99，即最大允许1%时间做GC
* 这两个参数是矛盾的。因为停顿时间和吞吐量不可能同时调优


####  CMS收集器

##### CMS收集器
 - Concurrent Mark Sweep 并发标(与用户线程一起执行)记清除  
 - 标记-清除算法  
 - 与标记-压缩相比  
 - 并发阶段会降低吞吐量  
 - 老年代收集器（新生代使用ParNew）  
 - XX:+UseConcMarkSweepGC
##### CMS运行过程比较复杂，着重实现了标记的过程，可分为
 - 初始标记
   * 根可以直接关联到的对象
   * 速度快
 - 并发标记（和用户线程一起）
 - 主要标记过程，标记全部对象
 - 重新标记
   * 由于并发标记时，用户线程依然运行，因此在正式清理前，再做修正
 - 并发清除（和用户线程一起）  
 - 基于标记结果，直接清理对象

![](http://ni484sha.com/images/gcc4.png)

##### 特点
 - 尽可能降低停顿
 - 会影响系统整体吞吐量和性能
   * 比如，在用户线程运行过程中，分一半CPU去做GC，系统性能在GC阶段，反应速度就下降一半
 - 清理不彻底
   * 因为在清理阶段，用户线程还在运行，会产生新的垃圾，无法清理
 - 因为和用户线程一起运行，不能在空间快满时再清理
   * -XX:CMSInitiatingOccupancyFraction设置触发GC的阈值
   * 如果不幸内存预留空间不够，就会引起concurrent mode failure


使用串行收集器作为后备

* 有关碎片
 - 标记-清除和标记-压缩

![](http://ni484sha.com/images/gcc5.png)

* -XX:+ UseCMSCompactAtFullCollection Full GC后，进行一次整理
 - 整理过程是独占的，会引起停顿时间变长
* -XX:+CMSFullGCsBeforeCompaction 
 - 设置进行几次Full GC后，进行一次碎片整理
* -XX:ParallelCMSThreads
 - 设定CMS的线程数量

#### GC参数整理

-XX:+UseSerialGC：在新生代和老年代使用串行收集器
-XX:SurvivorRatio：设置eden区大小和survivior区大小的比例 //8:1:1
-XX:NewRatio:新生代和老年代的比 
-XX:+UseParNewGC：在新生代使用并行收集器
-XX:+UseParallelGC ：新生代使用并行回收收集器
-XX:+UseParallelOldGC：老年代使用并行回收收集器
-XX:ParallelGCThreads：设置用于垃圾回收的线程数
-XX:+UseConcMarkSweepGC：新生代使用并行收集器，老年代使用CMS+串行收集器(作为担保)
-XX:ParallelCMSThreads：设定CMS的线程数量
-XX:CMSInitiatingOccupancyFraction：设置CMS收集器在老年代空间被使用多少后触发
-XX:+UseCMSCompactAtFullCollection：设置CMS收集器在完成垃圾收集后是否要进行一次内存碎片的整理
-XX:CMSFullGCsBeforeCompaction：设定进行多少次CMS垃圾回收后，进行一次内存压缩
-XX:+CMSClassUnloadingEnabled：允许对类元数据进行回收
-XX:CMSInitiatingPermOccupancyFraction：当永久区占用率达到这一百分比时，启动CMS回收
-XX:UseCMSInitiatingOccupancyOnly：表示只在到达阀值的时候，才进行CMS回收








