# 作用

ELK :
  日志收集
  监控
CI/CD:
  持续集成，持续构建    自动化上线
zabbix:
  监控 预警



# ELK+kafka+filebeat企业内部日志分析系统

### 1、组件介绍

#### 1、Elasticsearch：

  是一个基于Lucene的搜索服务器。提供搜集、分析、存储数据三大功能。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。

#### 2、Logstash:

  主要是用来日志的收集、分析、过滤日志的工具。用于管理日志和事件的工具，你可以用它去收集日志、转换日志、解析日志并将他们作为数据提供给其它模块调用，例如搜索、存储等。

#### 3、Kibana:

  是一个优秀的前端日志展示框架，它可以非常详细的将日志转化为各种图表，为用户提供强大的数据可视化支持,它能够搜索、展示存储在 Elasticsearch 中索引数据。使用它可以很方便的用图表、表格、地图展示和分析数据。

#### 4、Kafka：

​    数据缓冲队列。作为消息队列解耦了处理过程，同时提高了可扩展性。具有峰值处理能力，使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。

- 1.发布和订阅记录流，类似于消息队列或企业消息传递系统。
- 2.以容错持久的方式存储记录流。
- 3.处理记录发生的流。

#### 5、Filebeat: 

​    隶属于Beats,轻量级数据收集引擎。基于原先 Logstash-fowarder 的源码改造出来。换句话说：Filebeat就是新版的 Logstash-fowarder，也会是 ELK Stack 在 Agent 的第一选择,目前Beats包含四种工具：

- 1.Packetbeat（搜集网络流量数据）
- 2.Metricbeat（搜集系统、进程和文件系统级别的 CPU 和内存使用情况等数据。通过从操作系统和服务收集指标，帮助您监控服务器及其托管的服务。）
- 3.Filebeat（搜集文件数据）
- 4.Winlogbeat（搜集 Windows 事件日志数据）





# 1.logstash部署

部署logstash

logstash包下载位置  
wget https://artifacts.elastic.co/downloads/logstash/logstash-6.5.4.tar.gz
jdk版本 jdk-8u291 //要jdk8以上

### 1.1 部署jdk

过程略

```
java -version
java version "1.8.0_291"
Java(TM) SE Runtime Environment (build 1.8.0_291-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.291-b10, mixed mode)
```

### 1.2 解压logstash包并启动

```
tar xvzf logstash-6.5.4.tar.gz  -C /usr/local/
cd /usr/local/logstash-6.5.4/
```

### 1.3 logstash功能测试



#### 1.3.1 标准输入，标准输出

```
bin/logstash -e 'input { stdin{} } output {  stdout{codec => rubydebug} }'

输入
gz2101 

输出
{
       "message" => "gz2101",
      "@version" => "1",
    "@timestamp" => 2021-05-21T06:29:01.771Z,
          "host" => "es1"
}
```



#### 1.3.2 文件输入，标准输出

vim /usr/local/logstash-6.5.4/logstash.yml 

```
input {
 file {
    path => ["/var/log/nginx/access.log"]
    type => "nginx"
    start_position => "beginning"
  }
}

output {
 stdout{
    codec => rubydebug
  }
}

./bin/logstash -f  logstash.yml  --config.reload.automatic //日志执行命令

访问nginx产生日志

输出
{
          "type" => "nginx",
    "@timestamp" => 2021-05-21T06:35:34.478Z,
       "message" => "10.3.133.3 - - [21/May/2021:14:35:34 +0800] \"GET /img/html-background.png HTTP/1.1\" 304 0 \"http://10.3.133.211/\" \"Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like Gecko\" \"-\"",
      "@version" => "1",
          "path" => "/var/log/nginx/access.log",
          "host" => "es1"
}
```

#### 1.3.3 从端口接受日志

配置文件
vim /usr/local/logstash-6.5.4/logstash.yml 

```
input {
 tcp {
    port => 8888
  }
}

output {
 stdout{
    codec => rubydebug
  }
}

./bin/logstash -f  logstash.yml  --config.reload.automatic

输入:   //在另外一台服务器访问，没有nc命令记得yum -y install netcat
nc 10.3.133.211 8888
gz2101
gz21-gz22-gz23

输出
{
       "message" => "gz2101",
          "port" => 60576,
    "@timestamp" => 2021-05-21T06:39:32.221Z,
          "host" => "10.3.133.233",
      "@version" => "1"
}
{
       "message" => "gz21-gz22-gz23",
          "port" => 60576,
    "@timestamp" => 2021-05-21T06:40:06.684Z,
          "host" => "10.3.133.233",
      "@version" => "1"
}

```

#### 1.3.4 过滤器测试

配置文件
vim /usr/local/logstash-6.5.4/logstash.yml 

```
filter {
   mutate {
       split => ["message", "|"]
   }
}

输入
nc 10.3.133.211 8888
gz21|gz22|gz23|gz24

输出
{
       "message" => [
        [0] "gz21",
        [1] "gz22",
        [2] "gz23",
        [3] "gz24"
    ],
          "port" => 60584,
    "@timestamp" => 2021-05-21T06:42:31.911Z,
          "host" => "10.3.133.233",
      "@version" => "1"
}
```

#### 1.3.5 过滤器测试2

配置文件
vim /usr/local/logstash-6.5.4/logstash.yml 

```
filter {
   mutate {
       split => ["message", "|"]
   }
   mutate {
       join => ["message", "_?_"]
   }
}


输入
nc 10.3.133.211 8888
gz21|gz22|gz23|gz24

输出
{
       "message" => "gz21_?_gz22_?_gz23_?_gz24",
          "port" => 60592,
    "@timestamp" => 2021-05-21T06:45:34.818Z,
          "host" => "10.3.133.233",
      "@version" => "1"
}
```

#### 1.3.6  测试6--收集nginx日志并切割

配置文件
vim /usr/local/logstash-6.5.4/logstash.yml 

```
input {
​	 file {
​	    path => ["/var/log/nginx/access.log"]
​	    type => "nginx"
​	    start_position => "beginning"
​	  }
​	}
​	
​	filter {
​	    grok {
​	        match => { "message" => "%{COMBINEDAPACHELOG} %{QS:x_forwarded_for}"}    
​	    }    
​	    date {
​	        match => [ "timestamp" , "dd/MMM/YYYY:HH:mm:ss Z" ]    
​	    }    
​	#    geoip {
​	#        source => "clientip"    
​	#    }
​	}


​	
​	output {
​	 stdout{
​	    codec => rubydebug
​	  }
​	}


访问nginx产生日志

输出
{
           "clientip" => "10.3.133.65",
          "timestamp" => "21/May/2021:14:33:55 +0800",
    "x_forwarded_for" => "\"-\"",
         "@timestamp" => 2021-05-21T06:33:55.000Z,
           "referrer" => "\"http://10.3.133.211/\"",
           "@version" => "1",
              "ident" => "-",
               "host" => "es1",
        "httpversion" => "1.1",
              "bytes" => "82896",
               "type" => "nginx",
               "auth" => "-",
               "verb" => "GET",
            "message" => "10.3.133.65 - - [21/May/2021:14:33:55 +0800] \"GET /img/header-background.png HTTP/1.1\" 200 82896 \"http://10.3.133.211/\" \"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36\" \"-\"",
            "request" => "/img/header-background.png",
              "agent" => "\"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36\"",
               "path" => "/var/log/nginx/access.log",
           "response" => "200"
}



```

#### 1.3.7  测试7--输出到es

配置文件
vim /usr/local/logstash-6.5.4/logstash.yml 

```
input {
 file {
    path => ["/var/log/nginx/access.log"]
    type => "nginx"
    start_position => "beginning"
  }
}

filter {
    grok {
        match => { "message" => "%{COMBINEDAPACHELOG} %{QS:x_forwarded_for}"}    
    }    
    date {
        match => [ "timestamp" , "dd/MMM/YYYY:HH:mm:ss Z" ]    
    }    
}


output {
   elasticsearch {
      hosts => ["10.3.133.201","10.3.133.202","10.3.133.203"] //改成自己节点ip啊
      index => 'nginx-%{+YYYY-MM-dd}'
      }
}



输入:
访问nginx产生日志

输出:
输出到es  [通过es监控插件查看]

----------------------------------
logstash timestamp时间差8小时问题及解决

filter {  
    ruby { 
        code => "event.set('timestamp', event.get('@timestamp').time.localtime + 8*60*60)" 
    }
    ruby {
        code => "event.set('@timestamp',event.get('timestamp'))"
    }
    mutate {
        remove_field => ["timestamp"]
    }
}

```

启动

```
nohup ./bin/logstash -f  logstash.yml  --config.reload.automatic &
```





#### logstash timestamp时间差8小时问题及解决（假的，别用，懒删）

```
filter {  
    ruby { 
        code => "event.set('timestamp', event.get('@timestamp').time.localtime + 8*60*60)" 
    }
    ruby {
        code => "event.set('@timestamp',event.get('timestamp'))"
    }
    mutate {
        remove_field => ["timestamp"]
    }
}
```





# 2.Elasticsearch集群部署

ES集群搭建
ES插件管理             head【nginx代理】   ElasticHD
ES接受logstash日志
Kabana从es中获取数据



## ES部署

ES包下载
https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.5.4.tar.gz

es需要部署java环境

jdk部署

上传或者下载一个jdk的包

我是jdk改名为java的，所以路径才是java，按自己路径写

```
vim /etc/profile.d/java.sh
JAVA_HOME=/usr/local/java
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME PATH

source /etc/profile.d/java.sh
java -version
```



过程略

````
1.安装配置ES
创建运行ES的普通用户
useradd ela   //创建什么用户都可以，记得授权给此用户即可，还要用此用户开启es，不能直接用                   root用户启动es
echo '123456' | passwd --stdin ela   //给ela用户设置密码
````



wget ftp://10.3.133.50/soft2101/elasticsearch-6.5.4.tar.gz        //可直接换成上面的链接
tar xvzf elasticsearch-6.5.4.tar.gz  -C /usr/local/

vim /usr/local/elasticsearch-6.5.4/config/elasticsearch.yml

```
cluster.name: gz2101-es      //这个name其他节点要一致
node.name: elk01             //随便起名字，只要不重复即可
node.master: true
node.data: true
path.data: /data/elasticsearch/data
path.logs: /data/elasticsearch/logs
bootstrap.memory_lock: false            //这里要为false
bootstrap.system_call_filter: false
network.host: 0.0.0.0
http.port: 9200
discovery.zen.ping.unicast.hosts: ["10.3.133.212", "10.3.133.213"]  //填写其他节点ip,别把自己的当前节点ip也写上了
discovery.zen.minimum_master_nodes: 2
discovery.zen.ping_timeout: 150s
discovery.zen.fd.ping_retries: 10
client.transport.ping_timeout: 60s
http.cors.enabled: true
http.cors.allow-origin: "*"
```



vim /etc/sysctl.conf

```
vm.swappiness=0
vm.max_map_count=262144
```

写完用```sysctl -p```查看一下



vim /etc/security/limits.conf

```
* soft nofile 65536
* hard nofile 131072
* soft nproc 2048
* hard nproc 4096
```



设置JVM堆大小

```
sed -i 's/-Xms1g/-Xms2g/' /usr/local/elasticsearch-6.5.4/config/jvm.options
sed -i 's/-Xmx1g/-Xmx2g/' /usr/local/elasticsearch-6.5.4/config/jvm.options
```

注意：
确保堆内存最小值（Xms）与最大值（Xmx）的大小相同，防止程序在运行时改变堆内存大小。如果系统内存足够大，将堆内存最大和最小值设置为31G，因为有一个32G性能瓶颈问题。堆内存大小不要超过系统内存的50%



创建ES数据及日志存储目录

```
mkdir -p /data/elasticsearch/data
mkdir -p /data/elasticsearch/logs
```



修改安装目录及存储目录权限

```
chown -R ela:ela /data/elasticsearch
chown -R ela:ela /usr/local/elasticsearch-6.5.4
```



启动 

```
su - ela -c "cd /usr/local/elasticsearch-6.5.4 && nohup bin/elasticsearch &"
```



## 安装es监控插件 head     

(其他节点不需要部署，只在主机部署即可)

### 1.安装node

```
wget https://nodejs.org/dist/v10.15.0/node-v10.15.0-linux-x64.tar.xz
tar -xvf node-v10.15.0-linux-x64.tar.xz -C /usr/local/

vim /etc/profile.d/node.sh
NODE_HOME=/usr/local/node-v10.15.0-linux-x64
PATH=$NODE_HOME/bin:$PATH
export NODE_HOME PATH

source /etc/profile.d/node.sh
node --version   #检查node版本号
```



### 2.下载head插件

```
wget https://github.com/mobz/elasticsearch-head/archive/master.zip
unzip –d /usr/local master.zip   //注意master.zip前面是有空格的，直接复制可能会失败，建议手动
```



### 3.安装grunt

```
cd /usr/local/elasticsearch-head-master
npm install -g grunt-cli //不建议用这个，这个可能是官方的，下载慢，建议用下面的淘宝加速
grunt --version  #检查grunt版本号

npm install -g grunt-cli --registry=https://registry.npm.taobao.org  //淘宝npm加速 （别怀疑，上面说建议用下面的加速就是我）
```



### 4.修改head源码

vi /usr/local/elasticsearch-head-master/Gruntfile.js +99         //在后面+n可以直接到n行

```
     options: {
     port: 9100,
     base: '.',
     keepalive: true,   #注意添加,
     hostname: '*'      #不要用tab改用空格
              }
```

vi /usr/local/elasticsearch-head-master/_site/app.js   4374左右
原本是http://localhost:9200 ，如果head和ES不在同一个节点，注意修改成ES的IP地址,在就不用改了



### 5.下载head需要的包

```
wget https://github.com/Medium/phantomjs/releases/download/v2.1.1/phantomjs-2.1.1-linux-x86_64.tar.bz2

yum -y install bzip2
tar -jxf phantomjs-2.1.1-linux-x86_64.tar.bz2 -C /usr/local/

yum install fontconfig freetype libfreetype.so.6 libfontconfig.so.1 libstdc++.so.6

vim /etc/profile.d/phantomjs.sh
PH_HOME=/usr/local/phantomjs-2.1.1-linux-x86_64
PATH=$PATH:$PH_HOME/bin
export PH_HOME PATH

source /etc/profile.d/phantomjs.sh
```

如果出现 libstdc++-4.8.5-44包冲突问题，可以下载列表包并安装解决冲突
wget http://rpmfind.net/linux/centos/7.9.2009/os/x86_64/Packages/libstdc++-4.8.5-44.el7.x86_64.rpm

先查看是什么版本再下载，你需要的版本跟我的不一定相同。

yum localinstall -y libstdc++-4.8.5-44.el7.x86_64.rpm



### 6.运行head

```
cd /usr/local/elasticsearch-head-master/
npm install --registry=https://registry.npm.taobao.org 
nohup grunt server &
```

【如果第六步安装失败，则用nginx代理 /usr/local/elasticsearch-head/_site 作为nginx的根路径 ,注意 nginx 的启动用户改为 root】

```
user=root   //改为root
在server下面添加
server {
        listen       80;
        server_name  _;
        root  /usr/local/elasticsearch-head-master/_site; //添加我就行了，前面只是为了让你看清楚我的位置，别再加一个啊！！！如果locationroot有路径要清空root的路径，要不然寻找的有可能是location的位置。

```



### 7.logstash将数据输出到es中

vim  /usr/local/logstash-6.5.4/logstash.yml  (记得看nginx 的日志路径，别乱填，其他的看自己log部署时缺什么就加什么，嫌麻烦就直接dG,copy)

```
input {
   file {
       path => ["/var/log/nginx/access.log"]
       type => "nginx"
       start_position => "beginning"
   }
}

filter {
    grok {
        match => { "message" => "%{COMBINEDAPACHELOG} %{QS:x_forwarded_for}"}    
    }    
    ruby { 
        code => "event.set('timestamp', event.get('@timestamp').time.localtime + 8*60*60)" 
    }
    ruby {
        code => "event.set('@timestamp',event.get('timestamp'))"
    }
    mutate {
        remove_field => ["timestamp"]
    }
#    geoip {
#        source => "clientip"    
#    }
    date {
        match => [ "timestamp" , "dd/MMM/YYYY:HH:mm:ss Z" ]    
    }
}



output {
   elasticsearch {
      hosts => ["10.3.133.x","10.3.133.x","10.3.133.x"] //改为自己节点ip啊
      index => 'nginx-%{+YYYY-MM-dd}'
      }
}
```









## 安装 ElasticHD 监控es   

(这个是另一个插件，head比它更全面，这个软件页面好看。不喜欢可以不装，因为装了也没啥用，head和他二选一即可，喜欢也可以一起装)

```
 wget https://github.com/360EntSecGroup-Skylar/ElasticHD/releases/download/1.4/elasticHD_linux_amd64.zip
 chmod 777 ElasticHD
 nohup ./ElasticHD -p 10.3.133.211:9800  &
```







# 3.kibana部署

Kibana从es中获取数据
host 192.168.186.18    //部署的节点ip
需要：nginx  kibana-6.5.4(也可以是其他版本)

```
wget ftp://10.3.133.50/soft2101/kibana-6.5.4-linux-x86_64.tar.gz
tar xvzf kibana-6.5.4-linux-x86_64.tar.gz  -C /usr/local/
cd /usr/local/kibana-6.5.4-linux-x86_64/
```

 vim config/kibana.yml 

```
server.port: 5601
server.host: "192.168.186.18"   //部署kibanna的节点的ip
elasticsearch.url: "http://192.168.186.18:9200"   //这个改成随便一台部署es的ip，单机直接改为自己节点的ip即可
kibana.index: ".kibana"    //这个可以改为任何名字，ligoudan也行，只要你喜欢，记得在前面+个. 
```

启动

```
nohup ./bin/kibana &
```



















# 4.filebeat部署

fikebeat和kibana和 logstash（非集群，可随意布置，一个即可）

都可以随便布置在一台机器，但前提是要有nginx（没有应该也可以，但最好有），他们都只布置一个就可以了。

tar -xzvf filebeat-6.5.4-linux.tar.gz -C /usr/local

vim /usr/local/filebeat-6.5.4-linux-x86_64/filebeat.yml

```
filebeat.prospectors:
- input_type: log
  paths:
    - /var/log/nginx/access.log
  json.keys_under_root: true
  json.add_error_key: true
  json.message_key: log
  key: nginx-233                   //这个记得要在kafka创建，要不然找不到

output.kafka:
   hosts: [ "192.168.186.18:9092","192.168.186.16:9092" ]//这个是部署kafka的，有几                                                            个就写几个
   topic: 'nginx-233'
   when: key == 'nginx-233'
```



filebeat启动

```
nohup ./filebeat -e -c filebeat.yml &
```











# 5.kafka部署

节点为192.168.186.18，192.168.186.16

kafka集群需要借助与 zk[zookeeper]

kafka也需要jdk环境

wget ftp://10.3.133.50/soft2101/kafka_2.11-2.0.0.tgz
tar xzvf kafka_2.11-2.0.0.tgz -C /usr/local

cd /usr/local/kafka_2.11-2.0.0/

vim /usr/local/kafka_2.11-2.0.0/config/zookeeper.properties

```
dataDir=/opt/data/zookeeper/data 
dataLogDir=/opt/data/zookeeper/logs
clientPort=2181 
tickTime=2000 
initLimit=20 
syncLimit=10 
server.1=192.168.186.18:2888:3888
server.2=192.168.186.16:2888:3888
```

mkdir -p /opt/data/zookeeper/{data,logs}

echo 1 > /opt/data/zookeeper/data/myid   //数字不能相同，另一个节点选echo 2，其他也行，不相同即可



vim /usr/local/kafka_2.11-2.0.0/config/server.properties  

```
broker.id=1      //根据上面myid写的，不要相同
listeners=PLAINTEXT://192.168.186.18:9092    //当前节点ip，就是你现在用的节点ip
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
log.dirs=/opt/data/kafka/logs
num.partitions=6
num.recovery.threads.per.data.dir=1
offsets.topic.replication.factor=2
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1
log.retention.hours=168
log.segment.bytes=536870912
log.retention.check.interval.ms=300000
zookeeper.connect=192.168.186.18:2181,192.168.186.16:2181   //kafka集群
zookeeper.connection.timeout.ms=6000
group.initial.rebalance.delay.ms=0

```

创建kafka的log目录

```
mkdir -p /opt/data/kafka/logs
```



启动zk 

```
cd /usr/local/kafka_2.11-2.0.0/
nohup bin/zookeeper-server-start.sh config/zookeeper.properties &
```


验证zk配置文件

```
echo conf |nc 127.0.0.1 2181
echo stat|nc 127.0.0.1 2181
```

查看端口

```
lsof -i:2181 或者 ss -tunlp|grep 2181
```



启动Kafka

```
cd /usr/local/kafka_2.11-2.0.0
nohup bin/kafka-server-start.sh config/server.properties  &>./nohup1.output &
```



验证kafka

1.在192.168.186.18上创建topic

```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic testtopic
```

查询192.168.186.16上的topic

```
bin/kafka-topics.sh --zookeeper localhost:2181 --list
```



2.模拟消息生产和消费

发送消息到192.168.186.16

```
bin/kafka-console-producer.sh --broker-list 192.168.186.18:9092 --topic test
>Hello World!
//写自己的当前ip，因为集群能收到，别写locathost，会报错
```

192.168.186.16接收消息

```
bin/kafka-console-consumer.sh --bootstrap-server  192.168.186.16:9092 --topic test --from-beginning
//千万别写localhost，写自己当前ip，查看能不能收到，如果没收到可以等一会，机器配置差会慢点
```



配置日志收集

vim /usr/local/filebeat-6.5.4-linux-x86_64/filebeat.yml

```
filebeat.prospectors:
- input_type: log
  paths:
    - /var/log/nginx/access.log  //路径，如果要改这里也要修改哦
  json.keys_under_root: true
  json.add_error_key: true
  json.message_key: log
  key: nginx-233   //不一定要用nginx-233，用下面的test也行，也可以两个都写，根据自己需求的topic写即可。filebeat要注意格式（这是yaml语言写的）

output.kafka:    //不一定输出给kafka，也可以直接输出给logstash，但如果并发高极有可能宕机
   hosts: [ "192.168.186.18:9092","192.168.186.16:9092" ]
   topic: 'nginx-233'
   when: key == 'nginx-233'
```



logstash配置

```
input {
kafka {
    type => "nginx_log"
    codec => "json"
    topics => "nginx-233"
    decorate_events => true
    bootstrap_servers => "192.168.186.18:9092, 192.168.186.16:9092"
  }
kafka {    //这个不需要创建目录，只要上面创建topic的时候创建了test就可以
    type => "message_log"
    codec => "json"
    topics => "test"     
    decorate_events => true
    bootstrap_servers => "192.168.186.18:9092, 192.168.186.16:9092"
  }
}

filter {
   grok {
          match => { "message" => "%{COMBINEDAPACHELOG} %{QS:x_forwarded_for}"}    
      }    
   date {
          match => [ "timestamp" , "dd/MMM/YYYY:HH:mm:ss Z" ]    
      }    
}

output {
if [type] == 'nginx_log' {
   elasticsearch {
      hosts => ["192.168.186.18:9200"]
      index => '%{type}-%{+YYYY-MM-dd}'
      } }
if [type] == 'message_log' {
   elasticsearch {
      hosts => ["192.168.186.18:9200"]
      index => '%{type}-%{+YYYY-MM-dd}'
      } }
}

```

打开es看看有没有接收到数据，数据接收到后可以用kibana来展示。

es只需要模拟消息发送步骤即可，不需要再开一个用户来接收，直接在es上查看消息发送是否成功。

