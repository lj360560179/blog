---
title: quartz 使用spring @Scheduled注解执行定时任务 
layout: post
comments: true
tag: 
- java
- quartz 
category: java
---
### quartz 使用spring @Scheduled注解执行定时任务 
##### 1.maven依赖
```xml
    <dependency>
      <groupId>org.quartz-scheduler</groupId>
      <artifactId>quartz</artifactId>
      <version>1.8.4</version>
    </dependency>
```
<!-- more -->
##### 2.spring配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:task="http://www.springframework.org/schema/task"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context  
    http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/task 
    http://www.springframework.org/schema/task/spring-task.xsd ">
    
	<task:annotation-driven />
	<context:annotation-config />
	//配置扫描位置
 	<context:component-scan base-package="org.seckill.quartz"/>
</beans>
```
xsi:schemaLocation下要加上  
http://www.springframework.org/schema/task  
http://www.springframework.org/schema/task/spring-task-3.1.xsd 
##### 3.org.seckill.quartz包下的实现类
```java
package org.seckill.quartz;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
/**
 * 业务相关的作业调度
 * 
           字段               允许值                           允许的特殊字符
	秒	 	0-59	 	, - * /
	分	 	0-59	 	, - * /
	小时	 	0-23	 	, - * /
	日期	 	1-31	 	, - * ? / L W C
	月份	 	1-12 或者 JAN-DEC	 	, - * /
	星期	 	1-7 或者 SUN-SAT	 	, - * ? / L C #
	年（可选）	 	留空, 1970-2099	 	, - * /
	*  字符代表所有可能的值
	/  字符用来指定数值的增量
	L  字符仅被用于天（月）和天（星期）两个子表达式，表示一个月的最后一天或者一个星期的最后一天
	6L 可以表示倒数第６天
 *
 */
@Component
public class BizQuartz {

	/**
	 * 每天9点到17点每过1分钟执行
	 */
	@Scheduled(cron = "0 0/1 9-17 * * ? ")
	public void addUserScore() {
		System.out.print("每天9点到17点每过1分钟执行一次");
	}
}
```


