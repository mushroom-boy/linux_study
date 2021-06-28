# nacos 注册中心搭建

下载地址：https://github.com/alibaba/nacos/releases/download/1.4.0/nacos-server-1.4.0.tar.gz，不行就rz

```
nacos-server-1.4.0.tar.gz
rz

tar xzvf nacos-server-1.4.0.tar.gz  -C /usr/local
cd /usr/local/nacos/conf

//把nacos-mysql.sql 导入到Mysql
mysql -uroot -p'root' nacos < nacos-mysql.sql 

导入成功后，修改 application.properties 配置文件
server.contextPath=/nacos
server.servlet.contextPath=/nacos
server.port=8848

db.num=1

db.url.0=jdbc:mysql://mysql_ip:3306/nacos? characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true   [改IP]

db.user=root       //mysql账号
db.password=123    //mysql密码
```

```
cd ../bin/
sh startup.sh -m standalone
查看日志 /usr/local/nacos/logs/start.out
启动后访问
http://192.168.190.128:8848/nacos/index.html
```

