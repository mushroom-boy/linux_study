lvs_master:192.168.186.17

lvs_backup:192.168.186.16   vip:192.168.186. 67

nginx proxy:192.168.186.19   

server:192.168.186.18



首先配置lvs

lvs master + lvs backup：
yum -y install keepalived

```
! Configuration File for keepalived

global_defs {
   router_id lvs-master    //lvs_backuup改为baukup 
}

vrrp_instance VI_1 {
    state MASTER
    nopreempt
    interface ens33   //根据自己网卡配置
    mcast src ip 192.168.186.17   //改为自己当前地址ip，对，没有错，就是你操                                     作这台服务器的ip
    virtual_router_id 81
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 11111111
    }
    virtual_ipaddress {
        192.168.186.70   //虚拟ip
    }
}

virtual_server 192.168.186.70 80 {
    delay_loop 6
    lb_algo rr
    lb_kind DR
    persistence_timeout 20
    nat_mask 255.255.255.0
    protocol TCP
    sorry_server 192.168.186.98 80
  
    real_server 192.168.186.19 80 {
        weight 1
        inhibit_on_failure
        HTTP_GET {
            url {
              path /index.html
              digest 5170bfb7f3c2a5770368bb8a9837e8eb 
            }
            connect_port 80
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 2
        }
    }

}


```

systemctl restart keepalived



server:

首先需要配置好java环境,因为tomcat是基于java环境的，没有java无法启动。

```
tar xvzf jdk-8u291-linux-x64.tar.gz -C /usr/local/
mv /usr/local/java-8-291   /usr/local/java 
配置环境变量
vim /etc/profile.d/java.sh
JAVA_HOME=/usr/local/java
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME PATH
加载配置
source /etc/profile.d/java.sh
记得用java -version查看一下
```



tomcat 部署

```
下载地址: https://mirrors.ocf.berkeley.edu/apache/tomcat/tomcat-8/v8.5.65/bin/apache-tomcat-8.5.65.tar.gz

tar xvzf apache-tomcat-8.5.65.tar.gz -C /usr/local/
mv /usr/local/apache-tomcat-8.5.65 /usr/local/tomcat
```



部署好tomcat后

```
cp /usr/local/tomcat /usr/local/tomcat1
cp /usr/local/tomcat /usr/local/tomcat2
```

还要在配置文件中修改端口，要不然会端口冲突

vim /usr/local/tomcat/conf/server.xml   //根据当前的tomcat路径更改

tomcat1改为//修改 8006 8081

tomcat1改为//修改 8007 8082

配置nginx,开三个端口，分别为80,81,82

vim /etc/nginx/nginx.conf

```
    server {
        listen       80;       //80端口
        server_name  _;

        location / {
            root   html;
            index  index.html index.htm;
        }


        error_page  404 403 427 500 502 503 504  /404.html;
        location = /404.html {
            root   html;
        }

    }


    server {
        listen       81;    //81端口
        server_name  _;

location / {
        root   /usr/local/nginx/html1;
        index  index.html index.htm;

}
}

   server {
        listen       82;   //82端口
        server_name  _;

location / {
        root   /usr/local/nginx/html2;
        index  index.html index.htm;
}
}

```







nginx proxy:

添加vip
ip addr add dev lo 192.168.186.70/32
修改配置 
echo 1 > /proc/sys/net/ipv4/conf/all/arp_ignore
echo 2 > /proc/sys/net/ipv4/conf/all/arp_announce

vim /etc/nginx/nginx.conf

```
 配置负载均衡器
 upstream web {
 //权重为1：1:3，访问失败超过两次, 停止服务五秒
 server 192.168.186.18:8080 weight=1 max_fails=2 fail_timeout=5;
 server 192.168.186.18:8081 weight=1 max_fails=2 fail_timeout=5;
 server 192.168.186.18:8082 weight=3 max_fails=2 fail_timeout=5;
}

limit_req_zone $binary_remote_addr zone=mylimit:10m rate=1r/s;
//访问频率为 单客户端1次/s

       
        
         server {
        charset utf-8;
        listen       80;
        server_name  www.gz2101.com;
        root         /usr/share/nginx/html;

 location /tomcat {
                root /usr/share/nginx/html;
                index index.html;
                limit_req zone=mylimit; 引用流量限制
                proxy_pass http://web/; 
                limit_req_status 404;// 超过改频次跳转到自定义 404页面
        }

  location /web {
            proxy_pass http://192.168.186.18:80/;
    //访问 http://gz2101.com/web 跳转到后端nginx服务器的80虚拟主机
}

  location /web1 {
            proxy_pass http://192.168.186.18:81/;
   //访问 http://gz2101.com/web1 跳转到后端nginx服务器的81虚拟主机
}

  location /web2 {
            proxy_pass http://192.168.186.18:82/;
   //访问 http://gz2101.com/web2 跳转到后端nginx服务器的82虚拟主机
}

}



```

在window上的hosts文件做域名解析

用火绒直接打开hosts文件

```
192.168.186.19  www.gz2101.com
```

