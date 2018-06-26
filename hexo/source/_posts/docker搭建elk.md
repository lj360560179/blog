---
title: Docker搭建ELK
layout: post
comments: true
tag: 
- docker
date: 2018.3.25 15:44:14
---

ELK由Elasticsearch、Logstash和Kibana三部分组件组成。 

<!-- more -->
### 安装docker

[安装docker](https://yq.aliyun.com/articles/110806?spm=5176.8351553.0.0.eea61991hgICTU)

### elk安装前提
- Docker至少得分配3GB的内存；
- Elasticsearch至少需要单独2G的内存；
- 防火墙开放相关端口，执行以下命令开放相关端口；
```sh
firewall-cmd --zone=public --add-port=5601/tcp --permanent;firewall-cmd --reload;
firewall-cmd --zone=public --add-port=9200/tcp --permanent;firewall-cmd --reload;
firewall-cmd --zone=public --add-port=5044/tcp --permanent;firewall-cmd --reload;
```
- vm.max_map_count至少需要262144，附永久修改vm.max_map_count操作以下命令更改配置：
```sh
cd /etc
vi sysctl.conf
   增加以下属性后退出保存：
   vm.max_map_count=262144
```

### pull elk镜像
```sh  
docker pull sebp/elk
```
该镜像包含了elk三个部分，比较方便，也可以单独拉起每个部分的镜像分开部署

### 启动 elk
```sh  
docker run -d -p 5601:5601 -p 9200:9200 -p 5044:5044  -it --name elk sebp/elk
```
启动要一会,使用命令查看容器是否正常运行
```sh
docker ps
```
![](http://ni484sha.com/images/elk1.png)
可以看到容器已经正常启动，打开浏览器，输入：http://你的ip:5601，看到如下界面说明安装成功，只是还没有数据
![](http://ni484sha.com/images/elk2.png)

### 配置

使用命令以下进入容器内，并执行命令
```sh
docker exec -it elk /bin/bash 

#测试
/opt/logstash/bin/logstash --path.data /tmp/logstash/data  -e 'input { stdin { } } output { elasticsearch { hosts => ["localhost"] } }'

```
运行后等一会，出现下图则说明成功按配置启动logstash,测试命令的意思是配置 以命令行输入logstash,然后传输到elasticsearch,然后随便输入一下测试数据  
![](http://ni484sha.com/images/elk3.png)  
再次打开Kibana可以看到已经有数据，然后让你配置，按提示配置后去Discover就能看到刚才的数据  
![](http://ni484sha.com/images/elk4.png)  
如果看到这样的报错信息 Logstash could not be started because there is already another instance using the configured data directory. If you wish to run multiple instances, you must change the “path.data” setting. 请执行命令：service logstash stop 然后在执行就可以了。  


### 使用filebeat收集日志
Filebeat是一个日志文件托运工具，在你的服务器上安装客户端后，filebeat会监控日志目录或者指定的日志文件，追踪读取这些文件（追踪文件的变化，不停的读），并且转发这些信息到elasticsearch或者logstarsh中存放。  
因为项目是跑在docker中，可能不再一个虚拟机上，在一台虚拟机也不再同一个容器中，所以需要通过docker volumes将docker中的日志文件挂载到虚拟机上的一个目录，然后每台虚拟机使用filebeat来收集日志然后发送到logstash，当然logstash还有其他方式传输日志，比如redis，kafka等。如下图  
![](http://ni484sha.com/images/elk5.png)

#### 配置logstash
使用filebeat需要修改logstash配置文件，默认的就是使用beat输入
```sh
cd /etc/logstash/conf.d
```
配置文件在容器中的conf.d目录下，修改目录下的02-beats-input.conf文件 
```sh
input {
  beats {
    port => 5044
    client_inactivity_timeout => 36000
  }
}
```
client_inactivity_timeout为客户端连接超时时间,将证书的配置删除，如果你要配置证书，在filebeat也要配置，logstash配置还能做很多处理，比如
[grok解析](http://grokdebug.herokuapp.com/patterns#)  
需要的可以去官网查看详细信息，修改成后重启docker 容器

```sh
docker restart elk
```

#### 安装filebeat
下载filebeat镜像
```sh
docker pull prima/filebeat 
```
启动前要配置filebeat.yml
```sh
###################### Filebeat Configuration Example #########################
# You can find the full configuration reference here:
# https://www.elastic.co/guide/en/beats/filebeat/index.html
filebeat.prospectors:
- type: log
  enabled: true
  #配置filebeat要读取的log文件路径，有多个的话可以使用通配符或者多个paths节点配置
  paths:
    - /var/lib/docker/volumes/home/logs/service/*.log
  tags: ["service"]
  multiline:
      pattern: ^\d{4}
      negate: true
      match: after
  fields:
    doc_type: service 

- type: log 
  enabled: true
  paths:
    - /var/lib/docker/volumes/home/logs/app/*.log
  tags: ["app"]
  multiline:
      pattern: ^\d{4}
      negate: true
      match: after
  fields:
    doc_type: app  

- type: log 
  enabled: true
  paths:
    - /var/lib/docker/volumes/home/logs/center/*.log
  tags: ["center"]
  multiline:
      pattern: ^\d{4}
      negate: true
      match: after
  fields:
    doc_type: center    

setup.kibana:
output.logstash:
  hosts: ["127.0.0.1:5044"]
  logging.metrics.enabled: false
```
上面是一个简单的列子，可以去[官网](https://www.elastic.co/guide/en/beats/filebeat/index.html)看详细配置

然后启动filebeat
```sh
docker run -d --name filebeat -v /home/filebeat/filebeat.yml:/filebeat.yml -v /var/lib/docker/volumes/:/var/lib/docker/volumes/ -v /home/filebeat/module:/module --net=host  prima/filebeat

```
启动成功后配置文件中的监视目录下的日志文件就会传输到logstash中

