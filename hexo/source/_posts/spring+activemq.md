---
title: Spring+activemq
layout: post
comments: true
tag: 
- java
- Spring
- activemq
category: java
---
## 1.新建项目 ##
```cmd
mvn archetype:generate -DgroupId=springboot -DartifactId=springboot-example -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```
<!-- more -->
## 2.pom.xml ##
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>spring-acticemq</groupId>
  <artifactId>spring-acticemq</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>spring-acticemq Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>

    <dependency>
      <!-- 使用junit4.0 -->
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>

    <!--Spring-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>

    <!--spring test-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>

    <!-- activemq -->
    <dependency>
      <groupId>org.apache.activemq</groupId>
      <artifactId>activemq-all</artifactId>
      <version>5.9.0</version>
    </dependency>
    <dependency>
      <groupId>org.apache.activemq</groupId>
      <artifactId>activemq-pool</artifactId>
      <version>5.9.0</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jms</artifactId>
      <version>4.1.7.RELEASE</version>
    </dependency>

  </dependencies>

  <build>
    <finalName>spring-acticemq</finalName>
  </build>
</project>
```
## 3.spring-activemq.xml ##
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:amq="http://activemq.apache.org/schema/core"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://activemq.apache.org/schema/core http://activemq.apache.org/schema/core/activemq-core-5.8.0.xsd">

    <!-- Activemq 连接工厂 -->
    <bean id="activeMQConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
        <constructor-arg value="system1" />
        <constructor-arg value="manager1" />
        <constructor-arg value="failover:(tcp://localhost:61616)?timeout=10000" />
    </bean>

    <!-- ConnectionFactory Definition -->
    <bean id="connectionFactory"
          class="org.springframework.jms.connection.CachingConnectionFactory">
        <constructor-arg ref="activeMQConnectionFactory" />
    </bean>

    <!-- Default Destination Queue Definition -->
    <!-- 测试配置多个Destination -->
    <!-- P2P START-->
    <bean id="queueDestination" class="org.apache.activemq.command.ActiveMQQueue">
        <!-- 设置消息队列的名字 -->
        <constructor-arg>
            <value>testSpringQueue</value>
        </constructor-arg>
    </bean>

    <!--  topic-->
    <bean id="topicDestination" class="org.apache.activemq.command.ActiveMQTopic">
        <!-- 设置消息队列的名字 -->
        <constructor-arg>
            <value>testSpringTopic</value>
        </constructor-arg>
    </bean>



    <!-- 配置JMS模板（Queue），Spring提供的JMS工具类，它发送、接收消息。 -->
    <bean id="jmsTemplateQueue" class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="defaultDestination" ref="queueDestination" />
        <property name="receiveTimeout" value="10000" />
    </bean>

    <!-- 配置JMS模板（Topic），Spring提供的JMS工具类，它发送、接收消息。 -->
    <bean id="jmsTemplateTopic" class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="defaultDestination" ref="topicDestination" />
        <property name="receiveTimeout" value="10000" />
    </bean>


    <!-- Message Sender Definition -->
    <!-- Queue-->
    <bean id="messageSender" class="com.lj.activemq.publisher.MessageSender">
        <constructor-arg index="0" ref="jmsTemplateQueue" />
        <constructor-arg index="1" ref="queueDestination" />
    </bean>

    <!-- Message Sender Definition -->
    <!-- Topic-->
    <bean id="messageSenderTopic" class="com.lj.activemq.publisher.MessageSenderTopic">
        <constructor-arg index="0" ref="jmsTemplateTopic" />
        <constructor-arg index="1" ref="topicDestination" />
    </bean>


    <!-- 配置消息消费监听者 -->
    <!-- Queue-->
    <bean id="consumerMessageListenerQueue" class="com.lj.activemq.consumer.MessageReceiver" />
    <!-- 消息监听容器，配置连接工厂,监听器是上面定义的监听器 -->
    <bean id="jmsContainerQueue" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="destination" ref="queueDestination" />
        <!--主题（Topic）和队列消息的主要差异体现在JmsTemplate中"pubSubDomain"是否设置为True。如果为True，则是Topic；如果是false或者默认，则是queue-->
        <property name="pubSubDomain" value="false" />
        <property name="messageListener" ref="consumerMessageListenerQueue" />
    </bean>

    <!-- 配置消息消费监听者 -->
    <!-- Topic-->
    <bean id="consumerMessageListenerTopic" class="com.lj.activemq.consumer.MessageReceiverTopic1" />
    <bean id="consumerMessageListenerTopic2" class="com.lj.activemq.consumer.MessageReceiverTopic2" />
    <!-- 消息监听容器，配置连接工厂,监听器是上面定义的监听器 -->
    <bean id="jmsContainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="destination" ref="topicDestination" />
        <!--主题（Topic）和队列消息的主要差异体现在JmsTemplate中"pubSubDomain"是否设置为True。如果为True，则是Topic；如果是false或者默认，则是queue-->
        <property name="pubSubDomain" value="true" />
        <property name="messageListener" ref="consumerMessageListenerTopic" />
    </bean>
    <bean id="jmsContainer2" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="destination" ref="topicDestination" />
        <!--主题（Topic）和队列消息的主要差异体现在JmsTemplate中"pubSubDomain"是否设置为True。如果为True，则是Topic；如果是false或者默认，则是queue-->
        <property name="pubSubDomain" value="true" />
        <property name="messageListener" ref="consumerMessageListenerTopic2" />
    </bean>
</beans>
```
## 4.publisher ##
```java
package com.lj.activemq.publisher;
import org.springframework.jms.core.JmsTemplate;

import javax.jms.Destination;
/**
 * Created by LJ on 2016/8/27.
 */



/**
 * Created by LJ on 2016/8/25.
 */
public class MessageSender {
    private final JmsTemplate jmsTemplate;
    private final Destination destination;

    public MessageSender(final JmsTemplate jmsTemplate, final Destination destination) {
        this.jmsTemplate = jmsTemplate;
        this.destination = destination;
    }

    public void send(final String text) {
        try {
            jmsTemplate.setDefaultDestination(destination);
            jmsTemplate.convertAndSend(text);
            System.out.println("发送消息 : " + text);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
## 5.consumer ##
```java
package com.lj.activemq.consumer;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.MessageListener;
import javax.jms.TextMessage;

/**
 * Created by LJ on 2016/8/27.
 */
public class MessageReceiver implements MessageListener{
    public void onMessage(Message message) {
        if (message instanceof TextMessage) {
            TextMessage textMessage = (TextMessage) message;
            try {
                String text = textMessage.getText();
                System.out.println("点对点消费者接收到消息: " + text);
            } catch (JMSException e) {
                e.printStackTrace();
            }
        }
    }
}
```
## 地址 ##
[地址](https://github.com/lj360560179/spring-activemq)  

