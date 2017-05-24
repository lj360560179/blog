---
title: 【转】常用Linux命令
layout: post
comments: true
tag: 
- linux
---

[【转】Java开发必会的Linux命令](http://blog.csdn.net/u013970991/article/details/52036304)

### 1.查找文件


```sh
find / -name filename.txt 根据名称查找/目录下的filename.txt文件。

find . -name "*.xml" 递归查找所有的xml文件

find . -name "*.xml" |xargs grep "hello world" 递归查找所有文件内容中包含hello world的xml文件

grep -H 'spring' *.xml 查找所以有的包含spring的xml文件

find ./ -size 0 | xargs rm -f &amp; 删除文件大小为零的文件

ls -l | grep '.jar' 查找当前目录中的所有jar文件

grep 'test' d* 显示所有以d开头的文件中包含test的行。

grep 'test' aa bb cc 显示在aa，bb，cc文件中匹配test的行。

grep '[a-z]\{5\}' aa 显示所有包含每个字符串至少有5个连续小写字符的字符串的行。

```

### 2.查看一个程序是否运行

```sh
ps –ef|grep tomcat 查看所有有关tomcat的进程

```
### 3.终止线程

```sh
kill -9 19979 终止线程号位19979的进程

```
### 4.查看文件，包含隐藏文件

```sh
ls -al

```
### 5.当前工作目录

```sh
pwd

```
### 6.复制文件

```sh
cp source dest 复制文件

cp -r sourceFolder targetFolder 递归复制整个文件夹

scp sourecFile romoteUserName@remoteIp:remoteAddr 远程拷贝

```
### 7.创建目录

```sh
mkdir newfolder

```
### 8.删除目录

```sh
rmdir deleteEmptyFolder 删除空目录 

rm -rf deleteFile 递归删除目录中所有内容

```
### 9.移动文件

```sh
mv /temp/movefile /targetFolder

```
### 10.重命令

```sh
mv oldNameFile newNameFile
```
### 11.切换用户

```sh
su -username
```
### 12.修改文件权限

```sh
chmod 777 file.java //file.java的权限-rwxrwxrwx，r表示读、w表示写、x表示可执行

```
### 13.压缩文件

```sh
tar -czf test.tar.gz /test1 /test2

```
### 14.列出压缩文件列表

```sh
tar -tzf test.tar.gz

```
### 15.解压文件表

```sh
tar -xvzf test.tar.gz

```
### 16.查看文件头10行

```sh
head -n 10 example.txt

```
### 17.查看文件尾10行

```sh
tail -n 10 example.txt

```
### 18.查看日志类型文件

```sh
tail -f exmaple.log //这个命令会自动显示新增内容，屏幕只显示10行内容的（可设置）。
```
### 19.使用超级管理员身份执行命令

```sh
sudo rm a.txt 使用管理员身份删除文件

```
### 20.查看端口占用情况

```sh
netstat -tln | grep 8080 查看端口8080的使用情况

```
### 21.查看端口属于哪个程序

```sh
lsof -i :8080

```
### 22.查看进程

```sh

ps aux|grep java 查看java进程

ps aux 查看所有进程

```
### 23. 文件下载

```sh
wget http://file.tgz

curl http://file.tgz

```




