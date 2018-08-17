---
title: mysql 索引结构
layout: post
comments: true
tag: mysql
category: db
date: 2018.7.19 12:34:14 
---
mysql 索引结构
<!-- more -->
### Inoodb
![](http://ni484sha.com/images/Inoodb.jpg)

优化原则：
- 永远用小结果集驱动大结果集
- 只取出自己需要的字段(1.数据量 2.排序占用)
- 使用最有效的过滤条件
- 避免复杂的查询

### MyISAM
![](http://ni484sha.com/images/MyISAM.jpg)

### Join
![](http://ni484sha.com/images/mysqljoin.jpg)

```sql
/*查看join buffer 大小*/
SHOW VARIABLES LIKE '%join%' 
```
优化原则：
- 永远用小结果集驱动大结果集
- 保证驱动表上的join条件字段已经被索引
- 调整Join Buffer(join_buffer_size)大小(空间换时间)

### OrderBy
![](http://ni484sha.com/images/orderby.jpg)

```sql
/*查看join buffer 大小*/
SHOW VARIABLES LIKE '%sort%' 
SHOW VARIABLES LIKE '%max_length_for_sort_data%'
```
优化原则：
- 索引顺序一致的话不需要在排序
- 加大max_length_for_sort_data,把数据跟需要排序的字段放到内存中处理后一起返回。
- 加大innodb_sort_buffer_size(空间换时间)

### GroupBy

GroupBy是在OrderBy的基础上进行的 所以优化与OrderBy同理

### Distinct

Distinct是在GroupBy的基础上进行的 所以优化与OrderBy同理
