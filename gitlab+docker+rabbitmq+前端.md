192.168.43.109
192.168.43.110

### gitlab 部署（单独部署192.168.43.109）

1.关闭防火墙和selinux

```
systemctl stop iptables firewalld
systemctl disable iptables firewalld
setenforce 0
```

2.安装gitlab依赖包

```
yum install -y curl openssh-server openssh-clients postfix cronie policycoreutils-python
```

3.上传gitlab包并安装

```
gitlab-ce-13.6.2-ce.0.el7.x86_64.rpm
rz
yum localinstall -y gitlab-ce-13.6.2-ce.0.el7.x86_64.rpm
```

4.查看Gitlab版本

```
head -1 /opt/gitlab/version-manifest.txt
```

5.初始化 Gitlab

```
gitlab-ctl reconfigure 
```

6.启动gitlab

```
gitlab-ctl start
```





### rabbitmq部署+前端  （192.168.43.110）

因为rabbitmq是用docker部署的，需要配置好docker环境并安装docker

##### 部署docker

```
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum makecache fast
yum -y install docker-ce
mkdir -p /etc/docker
```

vim  /etc/docker/daemon.json

```
{
  "registry-mirrors":["https://10lhoa45.mirror.aliyuncs.com"]
}
```

systemctl restart docker







##### 部署rabbitmq

```
mkdir /data/rabbitmq
docker pull rabbitmq:3.7.14
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 -v /data/rabbitmq:/var/lib/rabbitmq --hostname myRabbit -e RABBITMQ_DEFAULT_VHOST=my_vhost  -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin rabbitmq:3.7.14 
docker exec -it rabbitmq /bin/bash
rabbitmq-plugins enable rabbitmq_management
```

访问：http://192.168.43.110:15672
rabbitmq账号密码：admin   admin

进页面后
users --> Add a user:添加用户 mall，权限为administrator

virtual host --> Add a new virtual host:/mall





前端部署
node环境

```
cd /usr/local/src/
wget https://npm.taobao.org/mirrors/node/v14.15.1/node-v14.15.1-linux-x64.tar.xz
tar xvf node-v14.15.1-linux-x64.tar.xz -C /usr/local/
```



配置环境变量
vim /etc/profile.d/npm.sh

```
NODE_HOME=/usr/local/node-v14.15.1-linux-x64
PATH=$NODE_HOME/bin:$PATH
export NODE_HOME PATH
```

source /etc/profile.d/npm.sh

检查node版本号

```
node --version
npm --version
```

安装webpackage

```
npm install webpack -g --registry=https://registry.npm.taobao.org
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install -g @vue/cli-service
```

上传maill

```
mall-admin-web.zip
rz
unzip -d /usr/local/ mall-admin-web.zip
cd /usr/local/mall-admin-web/
```

修改 mall-admin-web/config 下的dev.env.js 和 prod.env.js

 vim config/dev.env.js 

```
'use strict'
const merge = require('webpack-merge')
const prodEnv = require('./prod.env')

module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',
  BASE_API: '"http://192.168.43.110/mall-admin"'
})

```



vim config/prod.env.js 

```
'use strict'
module.exports = {
  NODE_ENV: '"production"',
  BASE_API: '"http://192.168.43.110/mall-admin"'
}

```

配置node 和 npm环境打包项目

```
cnpm install    //下载项目依赖
cnpm run build    //打包dev环境的包
ls dist/
cp -r dist/* /usr/share/nginx/html/  (把代码放到nginx，前提是要有nginx,还要看下nginx的路径)
```



##### Nginx跨域

需要安装nginx

```
rpm -q nginx
没有的话yum安装
yum -y install nginx
```

cp /etc/nginx/nginx.conf  /etc/nginx/nginx.conf.bak

vim /etc/nginx/nginx.conf

```
user root;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

   server {
      listen 80;
      server_name 192.168.43.110;   //本机ip
     
     location /
    {

        root /usr/share/nginx/html;
        index index.html index.htm;
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT, X-Mx-ReqToken, Keep-Alive, User-Agent, X-Requested-With, If-Modified-Since, Cache-Control, Content-Type, Authorization';
        if ($request_method = 'OPTIONS') {
           return 204;
        }
    }



    location /mall-admin {
      proxy_pass http://192.168.43.167:8201/mall-admin;   //后端网关ip
      proxy_redirect off;
      proxy_set_header Host $http_host;
      proxy_set_header X-Forward-For $remote_addr;
      proxy_set_header X-Real-IP $remote_addr;
      add_header Access-Control-Allow-Methods *;
      add_header Access-Control-Max-Age 3600;
      add_header Access-Control-Allow-Credentials true;
      add_header Access-Control-Allow-Origin $http_origin;
      add_header Access-Control-Allow-Headers $http_access_control_request_headers;
      if ($request_method = OPTIONS ) {
        return 200;
      }



   }


  }


}

```

访问192.168.43.110

账号：admin
密码：macro123



为了日志收集，要安装logstash，而logstash需要java环境

部署java

```
jdk-8u271-linux-x64.tar.gz
rz
tar -xvf jdk-8u271-linux-x64.tar.gz -C /usr/local
```

vim /etc/profile.d/java.sh

```
JAVA_HOME=/usr/local/java
PATH=$PATH:$JAVA_HOME/bin
export PATH JAVA_HOME                    
```

source /etc/profile.d/java.sh



##### 部署logstash

```
logstash-7.6.2.zip
rz
unzip -d /usr/local logstash-7.6.2.zip
cd /usr/local/logstash-7.6.2

```

vim logstash.yml

```
input {
 file {
    path => ["/var/log/nginx/access.log"]
    type => "nginx-110"
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
      hosts => ["192.168.43.90"]
      index => 'nginx-110-%{+YYYY-MM-dd}'
      }
}

```



启动

```
nohup bin/logstash -f logstash.yml  --config.reload.automatic &

tail -f nohup.out  //查看状态
```







##### 安装zabbix-agent 端

因为需要被监控，要安装zabbix-agent 端

```
cd /usr/local/src/
wget https://repo.zabbix.com/zabbix/4.2/rhel/7/x86_64/zabbix-release-4.2-1.el7.noarch.rpm
rpm -ivh zabbix-release-4.2-1.el7.noarch.rpm
yum -y install epel-release.noarch
yum install zabbix-agent zabbix-sender -y
vim /etc/zabbix/zabbix_agentd.conf 
systemctl start zabbix-agent
```

对配置文件做备份，修改配置文件

```
cd /etc/zabbix
cp zabbix_agentd.conf zabbix_agentd.conf.bak
```

vim zabbix_agentd.conf

```
Server=192.168.43.167   //指明服务端的监控ip
ListenPort=10050        //监听的端口
ListenIP=0.0.0.0        //监听的地址，0.0.0.0表示本机所有地址
StartAgents=3           //优化时使用的
ServerActive=192.168.43.167   //主动监控时的服务器
Hostname=192v168v43v110     //修改监控的主机名，不需要把机器的主机名也改
```

