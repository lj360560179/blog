---
title: github 上传
layout: post
comments: true
tag: 
- github
category: git
---

# SSH key

### 1.建立git仓库 
cd到你的本地项目根目录下，执行git命令
```sh
git init
```
### 2.将项目的所有文件添加到仓库中
```sh
git add .
```
如果想添加某个特定的文件，只需把.换成特定的文件名即可
### 3、将add的文件commit到仓库
```sh
git commit -m "注释语句"
```
### 4、去github上创建自己的Repository： 
去github上创建自己的Repository,拿到项目url
### 5、将本地的仓库关联到github上
```sh
git remote add origin https://github.com/lj360560179/xxRepository
```
### 6.上传github之前，要先pull一下，执行如下命令：
```sh
git pull origin master
```
### 7.也就是最后一步，上传代码到github远程仓库
```sh
git push -u origin master
```
执行完后，如果没有异常，等待执行完就上传成功了，中间可能会让你输入Username和Password，你只要输入github的账号和密码就行了

