---
title: Druid简单配置
layout: post
comments: true
tag: 
- java
- 数据库 
category: java
date: 2016.8.19 12:34:14 
---
### Druid简单配置
这里只是最简单的配置，更多详细的配置可以去[druid](https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)查看。
<!-- more -->
##### 1.maven依赖
```xml
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.0.20</version>
    </dependency>
```
##### 2.spring-dao.xml 中dataSource配置
```xml
   <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <!-- 数据源驱动类可不写，Druid默认会自动根据URL识别DriverClass -->
        <property name="driverClassName" value="${jdbc.driver}" />

        <!-- 基本属性 url、user、password -->
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />

        <!-- 配置初始化大小、最小、最大 -->
        <property name="initialSize" value="${jdbc.pool.init}" />
        <property name="minIdle" value="${jdbc.pool.minIdle}" />
        <property name="maxActive" value="${jdbc.pool.maxActive}" />

        <!-- 配置获取连接等待超时的时间 -->
        <property name="maxWait" value="60000" />

        <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 -->
        <property name="timeBetweenEvictionRunsMillis" value="60000" />

        <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
        <property name="minEvictableIdleTimeMillis" value="300000" />

        <property name="validationQuery" value="${jdbc.testSql}" />
        <property name="testWhileIdle" value="true" />
        <property name="testOnBorrow" value="false" />
        <property name="testOnReturn" value="false" />

        <!-- 打开PSCache，并且指定每个连接上PSCache的大小（Oracle使用）
        <property name="poolPreparedStatements" value="true" />
        <property name="maxPoolPreparedStatementPerConnectionSize" value="20" /> -->

        <!-- 配置监控统计拦截的filters -->
        <property name="filters" value="stat" />
    </bean>
```
##### 3.在web.xml中配置监控页面
```xml
<servlet>
    <servlet-name>DruidStatView</servlet-name>
    <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>DruidStatView</servlet-name>
    <url-pattern>/druid/*</url-pattern>
</servlet-mapping>
```    
最后打开http://localhost:8080/{youwebname}/druid/index.html 就可以看到内置监控页面。如下图。
![](http://ni484sha.com/images/druid.png)
    
    