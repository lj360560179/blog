---
title: Spring Boot + Spring-data-jpa简单例子
layout: post
comments: true
tag: 
- java
- Spring Boot
- Spring-data-jpa
category: java
---
### Spring Boot + Spring-data-jpa
这两个框架都没用过，平时上网查资料听的也比较多，有时间就自己整合一下，关于Spring Boot，[这里](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples)有很多跟其他框架整合的例子。
<!-- more -->
##### 1.SpringBoot基础项目
先把Spring boot的基础项目跑起来。参考两个大神的博客[这里1](http://www.bysocket.com/?p=1124)和[这里2](http://blog.didispace.com/tag/spring-boot/page/4/),第二个大神的博客里面有很多关于springboot的文章，包括[Spring Boot + Spring-data-jpa](http://blog.didispace.com/springbootdata2/)  
首先新建一个maven基础项目，可以通过idea也可以通过命令新建
```cmd
mvn archetype:generate -DgroupId=springboot -DartifactId=springboot-example -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```
##### 2.SpringBoot pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>springboot-example</groupId>
  <artifactId>springboot-example</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>springboot-example Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <!-- Inherit defaults from Spring Boot -->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.3.3.RELEASE</version>
  </parent>
  <dependencies>
    <!-- Spring Boot web依赖 -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- 单元测试 -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <finalName>springboot-example</finalName>
  </build>
</project>
```
##### 3.新建一个实体entity 
```java
package com.lj.entity;
/**
 * Created by LJ on 2016/8/2.
 *
 */
public class User {
    private Long id;
    private String name;
    private Integer age;

    public User(){}

    public User(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```
##### 4.controller
```java
package com.lj.controller;
import com.lj.entity.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.List;

/**
 * Created by LJ on 2016/8/2.
 */
@RestController
@RequestMapping("/user")
public class UserController {

    /**
     * 测试添加用户
     * @param id
     * @return
     */
    @RequestMapping("/{id}")
    public User view(@PathVariable("id") Long id) {
        Integer i = new Integer(Long.toString(id));
        User user = new User();
        user.setName("zhang");
        user.setAge(i);
        userRepository.save(user);
        return user;
    }
}
```
##### 5.启动应用类Application.java
```java
package com.lj;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

/**
 * Created by LJ on 2016/8/2.
 */

@Configuration
@ComponentScan
@EnableAutoConfiguration
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class);
    }
}
```
此时 右键运行Application，在浏览器中输入localhost:8080/user/1就可以看到返回的user。
##### 6.加入Spring-data-jpa
修改pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>springboot-example</groupId>
  <artifactId>springboot-example</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>springboot-example Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <!-- Inherit defaults from Spring Boot -->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.3.3.RELEASE</version>
  </parent>

  <dependencies>
    <!-- Spring Boot web依赖 -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>


    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>


    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.21</version>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>

  </dependencies>
  <build>
    <finalName>springboot-example</finalName>
  </build>
</project>
```
修改user.java。添加注解
```java
package com.lj.entity;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

/**
 * Created by LJ on 2016/8/2.
 *
 */
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private Integer age;

    public User(){}

    public User(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

}
```
修改controller方便测试。
```java
package com.lj.controller;
import com.lj.entity.User;
import com.lj.entity.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.List;

/**
 * Created by LJ on 2016/8/2.
 */
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserRepository userRepository;

    /**
     * 测试添加用户
     * @param id
     * @return
     */
    @RequestMapping("/{id}")
    public User view(@PathVariable("id") Long id) {
        Integer i = new Integer(Long.toString(id));
        User user = new User();
        user.setName("zhang");
        user.setAge(i);
        userRepository.save(user);
        return user;
    }

    /**
     * 测试查找所有用户
     * @return
     */
    @RequestMapping("/list")
    public List<User> list() {
        List<User> list = new ArrayList<User>();
        list = userRepository.findAll();
        return list;
    }

    /**
     * 测试通过名字查找用户
     * @param name
     * @return
     */
    @RequestMapping("/name/{name}")
    public User name(@PathVariable("name") String name){
        User user = userRepository.findByName(name);
        return user;
    }

}
```
##### 7.resources下新建配置文件application.properties
```xml
spring.datasource.url=jdbc:mysql://localhost:3306/springboot
spring.datasource.username=root
spring.datasource.password=110110
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

spring.jpa.properties.hibernate.hbm2ddl.auto=create-drop

```
根据自己情况修改帐号密码  spring.jpa.properties.hibernate.hbm2ddl.auto=create-drop是hibernate的配置参数  
create：表示启动的时候先drop，再create  
create-drop: 也表示创建，只不过再系统关闭前执行一下drop  
update: 这个操作启动的时候会去检查schema是否一致，如果不一致会做scheme更新  
更多详细的可以去网上查找资料
##### 8.JpaRepository
```java
package com.lj.entity;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

/**
 * Created by LJ on 2016/8/3.
 */
public interface UserRepository extends JpaRepository<User, Long>{
    User findByName(String name);
    
    User findByNameAndAge(String name, Integer age);
    
    @Query("from User u where u.name=:name")
    User findUser(@Param("name") String name);

}
```
这样就完成了，启动Application，打开controller相应的连接就能看到返回结果。
![](http://ni484sha.com/images/springboot1.png) 
![](http://ni484sha.com/images/springboot2.png)
![](http://ni484sha.com/images/springboot3.png)  
[最后放上源码地址](https://github.com/lj360560179/springboot-example)







