# 一、HTTP简介

HTTP协议是Hyper Text Transfer Protocol（超文本传输协议）的缩写,是用于从万维网（WWW:World Wide Web ）服务器传输超文本到本地浏览器的传送协议。

HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件, 图片文件, 查询结果等）。

### 1.HTTP 工作原理

HTTP协议工作于客户端-服务端架构上。浏览器作为HTTP客户端通过URL向HTTP服务端即WEB服务器发送所有请求。

Web服务器有：Nginx，Apache服务器，IIS服务器（Internet Information Services）等。

Web服务器根据接收到的请求后，向客户端发送响应信息。

HTTP默认端口号为80，但是你也可以改为8080或者其他端口。



**HTTP三点注意事项：**

- HTTP是无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
- HTTP是媒体独立的：这意味着，只要客户端和服务器知道如何处理的数据内容，任何类型的数据都可以通过HTTP发送。客户端以及服务器指定使用适合的MIME-type内容类型。 
- HTTP是无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。



### 2.HTTP 消息结构

HTTP是基于客户端/服务端（C/S）的架构模型，通过一个可靠的链接来交换信息，是一个无状态的请求/响应协议。

一个HTTP"客户端"是一个应用程序（Web浏览器或其他任何客户端），通过连接到服务器达到向服务器发送一个或多个HTTP的请求的目的。

一个HTTP"服务器"同样也是一个应用程序（通常是一个Web服务，如Apache Web服务器或IIS服务器等），通过接收客户端的请求并向客户端发送HTTP响应数据。

HTTP使用统一资源标识符（Uniform Resource Identifiers, URI）来传输数据和建立连接。

一旦建立连接后，数据消息就通过类似Internet邮件所使用的格式[RFC5322]和多用途Internet邮件扩展（MIME）[RFC2045]来传送。



客户端请求消息
	客户端发送一个HTTP请求到服务器的请求消息包括以下格式：请求行（request line）、请求头部（header）、空行和请求数据

![](C:\Users\蘑菇仔\Desktop\work\小屁孩笔记\image\nginx\2012072810301161.png)



服务器响应消息
	HTTP响应也由四个部分组成，分别是：状态行、消息报头、空行和响应正文。

![](C:\Users\蘑菇仔\Desktop\work\小屁孩笔记\image\nginx\httpmessage.jpg)

实例

下面实例是一点典型的使用GET来传递数据的实例：

客户端请求：

```
GET /hello.txt HTTP/1.1
User-Agent: curl/7.16.3 libcurl/7.16.3 OpenSSL/0.9.7l zlib/1.2.3
Host: www.example.com
Accept-Language: en, mi
```

服务端响应:

```
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2009 12:28:53 GMT
Server: Apache
Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
ETag: "34aa387-d-1568eb00"
Accept-Ranges: bytes
Content-Length: 51
Vary: Accept-Encoding
Content-Type: text/plain
```

输出结果：

```
Hello World! My payload includes a trailing CRLF.
```





### 3.HTTP 请求方法

根据HTTP标准，HTTP请求可以使用多种请求方法。

HTTP1.0定义了三种请求方法： GET, POST 和 HEAD方法。

HTTP1.1新增了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和 CONNECT 方法。

| 序号 | 方法    | 描述                                                         |
| ---- | ------- | ------------------------------------------------------------ |
| 1    | GET     | 请求指定的页面信息，并返回实体主体。                         |
| 2    | HEAD    | 类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头 |
| 3    | POST    | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。 |
| 4    | PUT     | 从客户端向服务器传送的数据取代指定的文档的内容。             |
| 5    | DELETE  | 请求服务器删除指定的页面。                                   |
| 6    | CONNECT | HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。     |
| 7    | OPTIONS | 允许客户端查看服务器的性能。                                 |
| 8    | TRACE   | 回显服务器收到的请求，主要用于测试或诊断。                   |







### 4.HTTP 响应头信息

HTTP请求头提供了关于请求，响应或者其他的发送实体的信息。

在本章节中我们将具体来介绍HTTP响应头信息

| 应答头           | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| Allow            | 服务器支持哪些请求方法（如GET、POST等）。                    |
| Content-Encoding | 文档的编码（Encode）方法。只有在解码之后才可以得到Content-Type头指定的内容类型。利用gzip压缩文档能够显著地减少HTML文档的下载时间。Java的GZIPOutputStream可以很方便地进行gzip压缩，但只有Unix上的Netscape和Windows上的IE 4、IE 5才支持它。因此，Servlet应该通过查看Accept-Encoding头（即request.getHeader("Accept-Encoding")）检查浏览器是否支持gzip，为支持gzip的浏览器返回经gzip压缩的HTML页面，为其他浏览器返回普通页面。 |
| Content-Length   | 表示内容长度。只有当浏览器使用持久HTTP连接时才需要这个数据。如果你想要利用持久连接的优势，可以把输出文档写入 ByteArrayOutputStream，完成后查看其大小，然后把该值放入Content-Length头，最后通过byteArrayStream.writeTo(response.getOutputStream()发送内容。 |
| Content-Type     | 表示后面的文档属于什么MIME类型。Servlet默认为text/plain，但通常需要显式地指定为text/html。由于经常要设置Content-Type，因此HttpServletResponse提供了一个专用的方法setContentType。 |
| Date             | 当前的GMT时间。你可以用setDateHeader来设置这个头以避免转换时间格式的麻烦。 |
| Expires          | 应该在什么时候认为文档已经过期，从而不再缓存它？             |
| Last-Modified    | 文档的最后改动时间。客户可以通过If-Modified-Since请求头提供一个日期，该请求将被视为一个条件GET，只有改动时间迟于指定时间的文档才会返回，否则返回一个304（Not Modified）状态。Last-Modified也可用setDateHeader方法来设置。 |
| Location         | 表示客户应当到哪里去提取文档。Location通常不是直接设置的，而是通过HttpServletResponse的sendRedirect方法，该方法同时设置状态代码为302。 |
| Refresh          | 表示浏览器应该在多少时间之后刷新文档，以秒计。除了刷新当前文档之外，你还可以通过setHeader("Refresh", "5; URL=http://host/path")让浏览器读取指定的页面。  注意这种功能通常是通过设置HTML页面HEAD区的＜META HTTP-EQUIV="Refresh" CONTENT="5;URL=http://host/path"＞实现，这是因为，自动刷新或重定向对于那些不能使用CGI或Servlet的HTML编写者十分重要。但是，对于Servlet来说，直接设置Refresh头更加方便。   注意Refresh的意义是"N秒之后刷新本页面或访问指定页面"，而不是"每隔N秒刷新本页面或访问指定页面"。因此，连续刷新要求每次都发送一个Refresh头，而发送204状态代码则可以阻止浏览器继续刷新，不管是使用Refresh头还是＜META HTTP-EQUIV="Refresh" ...＞。   注意Refresh头不属于HTTP 1.1正式规范的一部分，而是一个扩展，但Netscape和IE都支持它。 |
| Server           | 服务器名字。Servlet一般不设置这个值，而是由Web服务器自己设置。 |
| Set-Cookie       | 设置和页面关联的Cookie。Servlet不应使用response.setHeader("Set-Cookie", ...)，而是应使用HttpServletResponse提供的专用方法addCookie。参见下文有关Cookie设置的讨论。 |
| WWW-Authenticate | 客户应该在Authorization头中提供什么类型的授权信息？在包含401（Unauthorized）状态行的应答中这个头是必需的。例如，response.setHeader("WWW-Authenticate", "BASIC realm=＼"executives＼"")。  注意Servlet一般不进行这方面的处理，而是让Web服务器的专门机制来控制受密码保护页面的访问（例如.htaccess）。 |









### 5.HTTP 状态码

当浏览者访问一个网页时，浏览者的浏览器会向网页所在服务器发出请求。当浏览器接收并显示网页前，此网页所在的服务器会返回一个包含HTTP状态码的信息头（server header）用以响应浏览器的请求。

HTTP状态码的英文为HTTP Status Code。

下面是常见的HTTP状态码：

- 200 - 请求成功
- 301 - 资源（网页等）被永久转移到其它URL
- 404 - 请求的资源（网页等）不存在
- 500 - 内部服务器错误

**HTTP状态码分类**

HTTP状态码由三个十进制数字组成，第一个十进制数字定义了状态码的类型，后两个数字没有分类的作用。HTTP状态码共分为5种类型：

| 分类 | 分类描述                                       |
| ---- | ---------------------------------------------- |
| 1**  | 信息，服务器收到请求，需要请求者继续执行操作   |
| 2**  | 成功，操作被成功接收并处理                     |
| 3**  | 重定向，需要进一步的操作以完成请求             |
| 4**  | 客户端错误，请求包含语法错误或无法完成请求     |
| 5**  | 服务器错误，服务器在处理请求的过程中发生了错误 |

HTTP状态码列表:

| 状态码 | 状态码英文名称                  | 中文描述                                                     |
| ------ | ------------------------------- | ------------------------------------------------------------ |
| 100    | Continue                        | 继续。客户端应继续其请求                                     |
| 101    | Switching Protocols             | 切换协议。服务器根据客户端的请求切换协议。只能切换到更高级的协议，例如，切换到HTTP的新版本协议 |
|        |                                 |                                                              |
| 200    | OK                              | 请求成功。一般用于GET与POST请求                              |
| 201    | Created                         | 已创建。成功请求并创建了新的资源                             |
| 202    | Accepted                        | 已接受。已经接受请求，但未处理完成                           |
| 203    | Non-Authoritative Information   | 非授权信息。请求成功。但返回的meta信息不在原始的服务器，而是一个副本 |
| 204    | No Content                      | 无内容。服务器成功处理，但未返回内容。在未更新网页的情况下，可确保浏览器继续显示当前文档 |
| 205    | Reset Content                   | 重置内容。服务器处理成功，用户终端（例如：浏览器）应重置文档视图。可通过此返回码清除浏览器的表单域 |
| 206    | Partial Content                 | 部分内容。服务器成功处理了部分GET请求                        |
|        |                                 |                                                              |
| 300    | Multiple Choices                | 多种选择。请求的资源可包括多个位置，相应可返回一个资源特征与地址的列表用于用户终端（例如：浏览器）选择 |
| 301    | Moved Permanently               | 永久移动。请求的资源已被永久的移动到新URI，返回信息会包括新的URI，浏览器会自动定向到新URI。今后任何新的请求都应使用新的URI代替 |
| 302    | Found                           | 临时移动。与301类似。但资源只是临时被移动。客户端应继续使用原有URI |
| 303    | See Other                       | 查看其它地址。与301类似。使用GET和POST请求查看               |
| 304    | Not Modified                    | 未修改。所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。客户端通常会缓存访问过的资源，通过提供一个头信息指出客户端希望只返回在指定日期之后修改的资源 |
| 305    | Use Proxy                       | 使用代理。所请求的资源必须通过代理访问                       |
| 306    | Unused                          | 已经被废弃的HTTP状态码                                       |
| 307    | Temporary Redirect              | 临时重定向。与302类似。使用GET请求重定向                     |
|        |                                 |                                                              |
| 400    | Bad Request                     | 客户端请求的语法错误，服务器无法理解                         |
| 401    | Unauthorized                    | 请求要求用户的身份认证                                       |
| 402    | Payment Required                | 保留，将来使用                                               |
| 403    | Forbidden                       | 服务器理解请求客户端的请求，但是拒绝执行此请求               |
| 404    | Not Found                       | 服务器无法根据客户端的请求找到资源（网页）。通过此代码，网站设计人员可设置"您所请求的资源无法找到"的个性页面 |
| 405    | Method Not Allowed              | 客户端请求中的方法被禁止                                     |
| 406    | Not Acceptable                  | 服务器无法根据客户端请求的内容特性完成请求                   |
| 407    | Proxy Authentication Required   | 请求要求代理的身份认证，与401类似，但请求者应当使用代理进行授权 |
| 408    | Request Time-out                | 服务器等待客户端发送的请求时间过长，超时                     |
| 409    | Conflict                        | 服务器完成客户端的PUT请求是可能返回此代码，服务器处理请求时发生了冲突 |
| 410    | Gone                            | 客户端请求的资源已经不存在。410不同于404，如果资源以前有现在被永久删除了可使用410代码，网站设计人员可通过301代码指定资源的新位置 |
| 411    | Length Required                 | 服务器无法处理客户端发送的不带Content-Length的请求信息       |
| 412    | Precondition Failed             | 客户端请求信息的先决条件错误                                 |
| 413    | Request Entity Too Large        | 由于请求的实体过大，服务器无法处理，因此拒绝请求。为防止客户端的连续请求，服务器可能会关闭连接。如果只是服务器暂时无法处理，则会包含一个Retry-After的响应信息 |
| 414    | Request-URI Too Large           | 请求的URI过长（URI通常为网址），服务器无法处理               |
| 415    | Unsupported Media Type          | 服务器无法处理请求附带的媒体格式                             |
| 416    | Requested range not satisfiable | 客户端请求的范围无效                                         |
| 417    | Expectation Failed              | 服务器无法满足Expect的请求头信息                             |
|        |                                 |                                                              |
| 500    | Internal Server Error           | 服务器内部错误，无法完成请求                                 |
| 501    | Not Implemented                 | 服务器不支持请求的功能，无法完成请求                         |
| 502    | Bad Gateway                     | 作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个无效的响应 |
| 503    | Service Unavailable             | 由于超载或系统维护，服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的Retry-After头信息中 |
| 504    | Gateway Time-out                | 充当网关或代理的服务器，未及时从远端服务器获取请求           |
| 505    | HTTP Version not supported      | 服务器不支持请求的HTTP协议的版本，无法完成处理               |







# 二、nginx服务

### nginx 介绍

 *Nginx* (engine x) 是一个高性能的 HTTP 和 反向代理 服务，也是一个IMAP/POP3/SMTP服务。Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。

### 为什么选用nginx（重点）

1. 作为web服务器: 使用更少的资源，支持更多的并发连接，体现更高的效率。能够支持高达 50,000 个并发连接数的响应。
2. 作为负载均衡服务器：也可以支持作为 HTTP代理服务器 对外进行服务。
3. 作为邮件代理服务器:  同时也是一个非常优秀的邮件代理服务器。
4. Nginx 安装非常的简单，配置文件 非常简洁，Bugs非常少的服务器。



**IO多路复用**
	select poll epoll 三种模型
	nginx特性 异步、非阻塞
	

**nginx 的内部技术架构** 
	进程分为两个类型

1.master进程
	加载配置
	管理work进程
	非停机更新[热更新]

2.work进程
	接受请求



简要说明几点：

1）nginx启动时，会生成两种类型的进程，一个是主进程（Master），一个（windows版本的目前只有一个）或多个工作进程（Worker）。主进程并不处理网络请求，主要负责调度工作进程，也就是这三项：加载配置、启动工作进程及非停机升级。所以，nginx启动以后，查看操作系统的进程列表，我们就能看到至少有两个nginx进程。

2）服务器实际处理网络请求及响应的是工作进程（worker），在类unix系统上，nginx可以配置多个worker，而每个worker进程都可以同时处理数以千计的网络请求。

3）模块化设计。nginx的worker，包括核心和功能性模块，核心模块负责维持一个运行循环（run-loop），执行网络请求处理的不同阶段的模块功能，如网络读写、存储读写、内容传输、外出过滤，以及将请求发往上游服务器等。而其代码的模块化设计，也使得我们可以根据需要对功能模块进行适当的选择和修改，编译成具有特定功能的服务器。

4）事件驱动、异步及非阻塞，可以说是nginx得以获得高并发、高性能的关键因素，同时也得益于对Linux、Solaris及类BSD等操作系统内核中事件通知及I/O性能增强功能的采用，如kqueue、epoll及event ports。

5）代理（proxy）设计，可以说是nginx深入骨髓的设计，无论是对于HTTP，还是对于FastCGI、memcache、Redis等的网络请求或响应，本质上都采用了代理机制。所以，nginx天生就是高性能的代理服务器。



### nginx 安装部署和配置管理

#### 1、nginx 部署-Yum

访问nginx官方网站：http://www.nginx.org

Nginx版本类型

Mainline version：   主线版，即开发版

Stable version：       最新稳定版，生产环境上建议使用的版本

Legacy versions：    遗留的老版本的稳定版

![](C:\Users\蘑菇仔\Desktop\work\小屁孩笔记\image\nginx\nginxweb.PNG)



安装

```
yum -y install nginx     安装 
systemctl start nginx    启动
systemctl status nginx   查看状态
nginx -v    查看nginx版本
nginx -V    查看安装参数
```



访问:
http://ip





#### 2.Nginx编译安装

	1、安装编译环境
	yum -y install gcc gcc-c++
		
	2、安装pcre软件包（使nginx支持http rewrite模块）
	yum install -y pcre pcre-devel
	
	3、安装openssl-devel（使nginx支持ssl）
	yum install -y openssl openssl-devel 
	
	4、安装zlib
	yum install -y zlib zlib-devel
	
	5、创建用户nginx
	useradd nginx 
	passwd nginx
	
	6、安装nginx
	wget http://nginx.org/download/nginx-1.14.2.tar.gz
	tar xvzf nginx-1.14.2.tar.gz -C /usr/local
	cd /usr/local/nginx-1.14.2
	useradd nginx 
	./configure  --group=nginx --user=nginx --prefix=/usr/local/nginx --sbin-path=/usr/sbin/nginx  --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --http-client-body-temp-path=/tmp/nginx/client_body  --http-proxy-temp-path=/tmp/nginx/proxy  --http-fastcgi-temp-path=/tmp/nginx/fastcgi --pid-path=/var/run/nginx.pid --lock-path=/var/lock/nginx  --with-http_stub_status_module --with-http_ssl_module   --with-http_gzip_static_module --with-pcre
	make && make install
**nginx模块**

核心模块
官方模块     [可装可不装，取决于config参数]   --with..-module
第三方模块   [需要先下载模块包，解压]         --add-module=path



**Nginx配置文件**

```
# 全局参数设置 
worker_processes  1;          #设置nginx启动进程的数量，一般设置成与逻辑cpu数量相同 
error_log  logs/error.log;    #指定错误日志 
worker_rlimit_nofile 102400;  #设置一个nginx进程能打开的最大文件数 
pid        /var/run/nginx.pid; 
events { 
    worker_connections  1024; #设置一个进程的最大并发连接数 
} 
# http 服务相关设置 
http { 
    include      mime.types; 
    default_type  application/octet-stream; 
    log_format  main  'remote_addr - remote_user [time_local] "request" '
                      'status body_bytes_sent "$http_referer" '
                      '"http_user_agent" "http_x_forwarded_for"'; 
    access_log  /var/log/nginx/access.log  main;    #设置访问日志的位置和格式 
    sendfile          on; #是否调用sendfile函数输出文件，一般设置为on，若nginx是用来进行磁盘IO负载应用时，可以设置为off，降低系统负载 
    gzip              on;      #是否开启gzip压缩 
    keepalive_timeout  65;     #设置长连接的超时时间 
# 虚拟服务器的相关设置 
    server { 
        listen      80;        #设置监听的端口 
        server_name  localhost;        #设置绑定的主机名、域名或ip地址 
        charset koi8-r;        # 设置编码字符 
        location / { 
            root  /var/www/nginx;           #设置服务器默认网站的根目录位置 
            index  index.html index.htm;    #设置默认打开的文档 
            } 
        error_page  500 502 503 504  /50x.html; #设置错误信息返回页面 
        location = /50x.html { 
            root  html;        #这里的绝对位置是/var/www/nginx/html 
        } 
    } 
 }
```

nginx.conf  [就近原则]

```
全局配置

http {                      [只可能存在一个]

    http相关配置

  server {                  [可以存在多个，但都包含再http内]
     server相关配置
        location / {        [可以存在多个，但都包含再server内]
	        location相关配置
	    }
        location / {        [可以存在多个，但都包含再server内]
	        location相关配置
	    } 
  }

  server {                  [可以存在多个，但都包含再http内]
     server相关配置
        location / {        [可以存在多个，但都包含再server内]
	        location相关配置
	    }
	    } 
  }

}
```



**通过 nginx 命令控制 nginx 服务**

```
nginx -c /path/to/nginx.conf  	 # 以特定目录下的配置文件启动nginx:
nginx -s reload            	 	 # 修改配置后重新加载生效
nginx -s reopen   			 	 # 重新打开日志文件
nginx -s stop  				 	 # 快速停止nginx
nginx -s quit  				  	 # 完整有序的停止nginx
nginx -t    					 # 测试当前配置文件是否正确
nginx -t -c /path/to/nginx.conf  # 测试特定的nginx配置文件是否正确
nginx                            # 启动nginx
```

##### **实现nginx开机自启**

添加启动脚本  vim /etc/init.d/nginx

```
#!/bin/sh 
# 
# nginx - this script starts and stops the nginx daemon 
# 
# chkconfig:  - 85 15  
# description:  Nginx is an HTTP(S) server, HTTP(S) reverse \ 
#              proxy and IMAP/POP3 proxy server 
# processname: nginx 
# config:      /etc/nginx/nginx.conf 
# config:      /etc/sysconfig/nginx 
# pidfile:    /var/run/nginx.pid 
  
# Source function library. 
. /etc/rc.d/init.d/functions
  
# Source networking configuration. 
. /etc/sysconfig/network
  
# Check that networking is up. 
[ "$NETWORKING" = "no" ] && exit 0 
  
nginx="/usr/sbin/nginx"
prog=$(basename $nginx) 
  
NGINX_CONF_FILE="/etc/nginx/nginx.conf"
  
[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx
  
lockfile=/var/lock/subsys/nginx
  
make_dirs() { 
  # make required directories 
  user=`nginx -V 2>&1 | grep "configure arguments:" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -` 
  options=`$nginx -V 2>&1 | grep 'configure arguments:'` 
  for opt in $options; do
      if [ `echo $opt | grep '.*-temp-path'` ]; then
          value=`echo $opt | cut -d "=" -f 2` 
          if [ ! -d "$value" ]; then
              # echo "creating" $value 
              mkdir -p $value && chown -R $user $value 
          fi
      fi
  done
} 
  
start() { 
    [ -x $nginx ] || exit 5 
    [ -f $NGINX_CONF_FILE ] || exit 6 
    make_dirs 
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE 
    retval=$? 
    echo
    [ $retval -eq 0 ] && touch $lockfile 
    return $retval 
} 
  
stop() { 
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT 
    retval=$? 
    echo
    [ $retval -eq 0 ] && rm -f $lockfile 
    return $retval 
} 
  
restart() { 
    configtest || return $? 
    stop 
    sleep 1 
    start 
} 
  
reload() { 
    configtest || return $? 
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP 
    RETVAL=$? 
    echo
} 
  
force_reload() { 
    restart 
} 
  
configtest() { 
  $nginx -t -c $NGINX_CONF_FILE 
} 
  
rh_status() { 
    status $prog 
} 
  
rh_status_q() { 
    rh_status >/dev/null 2>&1 
} 
  
case "$1" in
    start) 
        rh_status_q && exit 0 
        $1 
        ;; 
    stop) 
        rh_status_q || exit 0 
        $1 
        ;; 
    restart|configtest) 
        $1 
        ;; 
    reload) 
        rh_status_q || exit 7 
        $1 
        ;; 
    force-reload) 
        force_reload 
        ;; 
    status) 
        rh_status 
        ;; 
    condrestart|try-restart) 
        rh_status_q || exit 0 
            ;; 
    *) 
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2 
esac
```

添加权限

```
chmod +x /etc/init.d/nginx
```

重载系统启动文件

```
systemctl daemon-reload
```

设置开机自启

```
systemctl start nginx
```



#### 3.nginx 日志文件详解

​    nginx 日志文件分为 **log_format** 和 **access_log** 两部分

​    log_format 定义记录的格式，其语法格式为

​    log_format        样式名称        样式详情

​    配置文件中默认有

```
log_format  main  'remote_addr - remote_user [time_local] "request" '
                  'status body_bytes_sent "$http_referer" '
                  '"http_user_agent" "http_x_forwarded_for"';
```

| 变量                                | 说明                                                        |
| ----------------------------------- | ----------------------------------------------------------- |
| $remote_addr和$http_x_forwarded_for | 客户端的ip                                                  |
| $remote_user                        | 客户端的名称                                                |
| $time_local                         | 访问时的本地时间                                            |
| $request                            | 请求的URL和http协议                                         |
| $status                             | 访问的状态码                                                |
| $body_bytes_sent                    | 发送给客户端的主体内容大小                                  |
| $http_referer                       | 记录客户端是从哪个页面链接访问过来的，若没有链接，则访问‘-’ |
| $http_user_agent                    | 记录客户端使用的浏览器的相关信息                            |

##### root 和alias

**配置root**

```
        location / {
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }
```


C端  curl http://10.3.134.200/abc/index.html

S端  配置种          root /usr/local/nginx/html      uri abc/index.html
最终返回资源    /usr/local/nginx/html/abc/index.html

---
        location /abc {
            root  /usr/local/nginx/html;
            index index.html index.htm;
         }

C端  curl http://10.3.134.200/abc/

S端  配置种          root /usr/local/nginx/html      uri abc/index.html
最终返回资源    /usr/local/nginx/html/abc/index.html



---

使用 alias 实现虚拟目录

        location /a1 {
           alias /usr/local/nginx/html1;
           index index.html index.htm;
       }

C端： http://10.3.134.200/a1/index.html

S端           root   /usr/local/nginx/html1         uri  a1/index.html
            /usr/local/nginx/html1/a1/index.html

S端           alias  /usr/local/nginx/html1
             /usr/local/nginx/html1/a1/ 替换为  /usr/local/nginx/html1
		   
/usr/local/nginx/html1/index.html



```
location /ww  {
     root /usr/local/nginx/html;
	 index index.html index.html;
}

location /ww  {
     alias /usr/local/nginx/html;
	 index index.html index.html;
}

location / {
    root   /usr/local/nginx/html;
    index  index.html index.htm;
}


http://10.3.134.200/ww/a/a1.html                           uri=/ww/a/a1.html

root  /usr/local/nginx/html/ww/a/a1.html                    root-location + uri
      /usr/local/nginx/html/ww/a/a1.html

alias /usr/local/nginx/html/a/a1.html              

http://10.3.134.200/a/a1.html

/usr/local/nginx/html/a/a1.html

elinks http://10.3.134.200/ww/a/a1.html
```



####     4.通过 stub_status 模块监控 nginx 的工作状态

1、通过 nginx  -V 命令查看是否已安装 stub_status 模块

2、编辑 /etc/nginx/nginx.conf 配置文件

```
#添加以下内容～～ 
location /nginx-status { 
      stub_status on; 
      access_log    /var/log/nginx/nginxstatus.log;    #设置日志文件的位置 
      auth_basic    "nginx-status";    #指定认证机制（与location后面的内容相同即可） 
      auth_basic_user_file    /etc/nginx/htpasswd;     #指定认证的密码文件 
      }    
```

3、创建认证口令文件并添加用户 tiger和 zsgg，密码用md5加密

```
htpasswd -c -m /etc/nginx/htpasswd tiger             # -c 创建解密文件，-m MD5加密
htpasswd -m /etc/nginx/htpasswd zsgg

htpasswd 是开源 http 服务器 apache httpd 的一个命令工具，用于生成 http 基本认证的密码文件。
安装htpasswd: yum -y install httpd-tools
```

4、重启服务

5、客户端访问 http://x.x.x.x/nginx-status 即可





#### 5.使用 limit_rate 限制客户端传输数据的速度

1、编辑/etc/nginx/nginx.conf

```
 location / { 
    root    /var/www/nginx; 
    index    index.html; 
    limit_rate    2k;        #对每个连接的限速为2k/s
```

2、重启服务

**注意要点：**

- ​    配置文件中的每个语句要以 ; 结尾

- ​    使用 htpasswd 命令需要先安装 httpd



#### 6.虚拟主机

虚拟主机是一种特殊的软硬件技术，它可以将网络上的每一台计算机分成多个虚拟主机，每个虚拟主机可以独立对外提供www服务，这样就可以实现一台主机对外提供多个web服务，每个虚拟主机之间是独立的，互不影响。

虚拟主机分类
    基于域名的虚拟主机
	基于端口的虚拟主机
	基于ip的虚拟主机

##### 1、 基于域名的虚拟主机

1、配置通过域名区分的虚拟机

```
vim /etc/nginx/nginx.conf 
 server {
        listen       80;
        server_name  www.qf.com;

        location / {
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }
    }

    server {
        listen    80;
        server_name www.gz2101.com;

       location / {
          root /usr/local/nginx/html1;
          index index.html;
       }
     }


mkdir /usr/local/nginx/html1
echo www.gz2101.com >/usr/local/nginx/html1/index.html
echo www.qf.com     >/usr/local/nginx/html/index.html

nginx -t 
nginx -s reload 

测试
客户端做本地解析
vim /etc/hosts
10.3.134.200 www.qf.com
10.3.134.200 www.gz2101.com

[root@server ~]# curl http://www.qf.com
www.qf.com
[root@server ~]# curl http://www.gz2101.com
www.gz2101.com
```



##### 2、 基于ip的虚拟主机

1.给网卡添加一个新的ip
ifconfig ens33:1 10.3.134.241

```
vim /etc/nginx/nginx.conf
server {
        listen   10.3.134.200:80;
        server_name _;

       location / {
          root /usr/local/nginx/html;
          index index.html;
       }
     }


    server {
        listen      10.3.134.241:80;
        server_name  _;

        location / {
            root   /usr/local/nginx/html1;
            index  index.html index.htm;
        }

    }

mkdir /usr/local/nginx/html1
echo 241     >/usr/local/nginx/html1/index.html
echo 200     >/usr/local/nginx/html/index.html

nginx -t 
nginx -s quit
nginx 

测试
curl http://10.3.134.200
200

curl http://10.3.134.241
241
```



##### 3、 基于端口的虚拟主机

```
1. 修改配置文件
vim /etc/nginx/nginx.conf
    server {
        listen   80;
        server_name _;

       location / {
          root /usr/local/nginx/html;
          index index.html;
       }
     }

    server {
        listen   90;
        server_name _;

       location / {
          root /usr/local/nginx/html1;
          index index.html;
       }
     }
	 
mkdir /usr/local/nginx/html1
echo 80     >/usr/local/nginx/html1/index.html
echo 90     >/usr/local/nginx/html/index.html

nginx -t 
nginx -s reopen	 
nginx	 
​```
测试
​```
curl http://10.3.134.200
80

curl http://10.3.134.200:90
90
```



nginx可以实现虚拟主机的配置，nginx支持三种类型的虚拟主机配置。
1、基于域名的虚拟主机 （server_name来区分虚拟主机——应用：外部网站）
2、基于ip的虚拟主机， （一块主机绑定多个ip地址）
3、基于端口的虚拟主机 （端口来区分虚拟主机——应用：公司内部网站，外部网站的管理后台）











#### 7.nginx Proxy 代理

##### **1、代理原理**   

- 反向代理产生的背景：

  在计算机世界里，由于单个服务器的处理客户端（用户）请求能力有一个极限，当用户的接入请求蜂拥而入时，会造成服务器忙不过来的局面，可以使用多个服务器来共同分担成千上万的用户请求，这些服务器提供相同的服务，对于用户来说，根本感觉不到任何差别。

- 反向代理服务的实现：

  需要有一个负载均衡设备（即反向代理服务器）来分发用户请求，将用户请求分发到空闲的服务器上。

  服务器返回自己的服务到负载均衡设备。

  负载均衡设备将服务器的服务返回用户。



##### **2.正/反向代理的区别**

正向代理：

正向代理的过程隐藏了真实的请求客户端，服务器不知道真实的客户端是谁，客户端请求的服务都被代理服务器代替请求。我们常说的代理也就是正向代理，正向代理代理的是请求方，也就是客户端；比如我们要访问youtube，可是不能访问，只能先安装个FQ软件代你去访问，通过FQ软件才能访问，FQ软件就叫作正向代理。



反向代理：

反向代理的过程隐藏了真实的服务器，客户不知道真正提供服务的人是谁，客户端请求的服务都被代理服务器处理。反向代理代理的是响应方，也就是服务端；我们请求www.baidu.com时这www.baidu.com就是反向代理服务器，真实提供服务的服务器有很多台，反向代理服务器会把我们的请求分转发到真实提供服务的各台服务器。Nginx就是性能非常好的反向代理服务器，用来做负载均衡。



##### **3、知识扩展1**

1. 没有使用LVS时，客户端请求直接到反向代理Nginx，Nginx分发到各个服务器，服务端响应再由Ngnix返回给客户端，这样请求和响应都经过Ngnix的模式使其性能降低，这时用LVS+Nginx解决。
2. LVS+Nginx，客户端请求先由LVS接收，分发给Nginx，再由Nginx转发给服务器，LVS有四种方式：NAT模式（Network Address Translation）网络地址转换，DR模式（直接路由模式），IP隧道模式，路由方式使服务器响应不经过LVS,由Nginx直接返回给客户端。



##### **4、知识扩展2**

1. HTTP Server和Application Server的区别和联系

   Apache/nignx是静态服务器（HTTP Server）：

   Nginx优点：负载均衡、反向代理、处理静态文件优势。nginx处理静态请求的速度高于apache；

   Apache优点：相对于Tomcat服务器来说处理静态文件是它的优势，速度快。Apache是静态解析，适合静态HTML、图片等。

   HTTP Server 关心的是 HTTP 协议层面的传输和访问控制，所以在 Apache/Nginx 上你可以看到代理、负载均衡等功能

   HTTP Server（Nginx/Apache）常用做静态内容服务和代理服务器，将外来请求转发给后面的应用服务（tomcat，jboss,jetty等）。

   应用服务器(tomcat/jboss/jetty)是动态服务器（Application Server）：

   应用服务器Application Server，则是一个应用执行的容器。它首先需要支持开发语言的 Runtime（对于 Tomcat 来说，就是 Java，若是Ruby/Python 等其他语言开发的应用也无法直接运行在 Tomcat 上）。

2. 但是事无绝对，为了方便，应用服务器(如tomcat)往往也会集成 HTTP Server 的功能，nginx也可以通过模块开发来提供应用功能，只是不如专业的 HTTP Server 那么强大，所以应用服务器往往是运行在 HTTP Server 的背后，执行应用，将动态的内容转化为静态的内容之后，通过 HTTP Server 分发到客户端。

3. 常用开源集群软件有：lvs，keepalived，haproxy，nginx，apache，heartbeat

   常用商业集群硬件有：F5, Netscaler，Radware，A10等



##### 5、nginx Proxy 配置

###### 1、代理模块

```
ngx_http_proxy_module
```

###### 2、代理配置

```
代理
Syntax: 	proxy_pass URL;				   #代理的后端服务器URL
Default: 	—
Context: 	location, if in location, limit_except

缓冲区
Syntax:     proxy_buffering on | off;
Default:    proxy_buffering on;			   #缓冲开关
Context: 	http, server, location
proxy_buffering开启的情况下，nignx会把后端返回的内容先放到缓冲区当中，然后再返回给客户端
（边收边传，不是全部接收完再传给客户端)。

Syntax:   	proxy_buffer_size size;
Default: 	proxy_buffer_size 4k|8k;	   #缓冲区大小
Context: 	http, server, location

Syntax: 	proxy_buffers number size;
Default: 	proxy_buffers 8 4k|8k;		   #缓冲区数量
Context: 	http, server, location

Syntax:    	proxy_busy_buffers_size size;
Default: 	proxy_busy_buffers_size 8k|16k;#忙碌的缓冲区大小控制同时传递给客户端的buffer数量
Context: 	http, server, location

头信息
Syntax: 	proxy_set_header field value;
Default: 	proxy_set_header Host $proxy_host;		#设置真实客户端地址
            proxy_set_header Connection close;
Context: 	http, server, location

超时
Syntax: 	proxy_connect_timeout time;
Default: 	proxy_connect_timeout 60s;				#链接超时
Context: 	http, server, location

Syntax: 	proxy_read_timeout time;
Default: 	proxy_read_timeout 60s;
Context: 	http, server, location

Syntax: 	proxy_send_timeout time; #nginx进程向fastcgi进程发送request的整个过程的超时时间
Default: 	proxy_send_timeout 60s;
Context: 	http, server, location

#buffer 工作原理
1. 所有的proxy buffer参数是作用到每一个请求的。每一个请求会安按照参数的配置获得自己的buffer。proxy buffer不是global而是per request的。

2. proxy_buffering 是为了开启response buffering of the proxied server，开启后proxy_buffers和proxy_busy_buffers_size参数才会起作用。

3. 无论proxy_buffering是否开启，proxy_buffer_size（main buffer）都是工作的，proxy_buffer_size所设置的buffer_size的作用是用来存储upstream端response的header。

4. 在proxy_buffering 开启的情况下，Nginx将会尽可能的读取所有的upstream端传输的数据到buffer，直到proxy_buffers设置的所有buffer们 被写满或者数据被读取完(EOF)。此时nginx开始向客户端传输数据，会同时传输这一整串buffer们。同时如果response的内容很大的 话，Nginx会接收并把他们写入到temp_file里去。大小由proxy_max_temp_file_size控制。如果busy的buffer 传输完了会从temp_file里面接着读数据，直到传输完毕。

5. 一旦proxy_buffers设置的buffer被写入，直到buffer里面的数据被完整的传输完（传输到客户端），这个buffer将会一直处 在busy状态，我们不能对这个buffer进行任何别的操作。所有处在busy状态的buffer size加起来不能超过proxy_busy_buffers_size，所以proxy_busy_buffers_size是用来控制同时传输到客户 端的buffer数量的。
```

###### 3、启用 nginx proxy 代理

环境两台nginx真实服务器

proxy    10.3.134.240
backend  10.3.134.200

a、nginx-1 启动网站(内容)

```
nginx-1的IP：10.3.134.200
yum install -y nginx
systemctl start nginx
```

b、nginx-2 启动代理程序

```
nginx-2的IP：10.3.134.240
yum install -y nginx
systemctl start nginx

# nginx 配置文件添加 proxy 配置
vim /etc/nginx/conf.d/default.conf
location / {
.....
proxy_pass http://10.3.134.200;
proxy_redirect default;

proxy_set_header Host http_host;
proxy_set_header X-Real-IP remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

proxy_connect_timeout 30;
proxy_send_timeout 60;
proxy_read_timeout 60;

proxy_buffering on;
proxy_buffer_size 32k;
proxy_buffers 4 128k;
proxy_busy_buffers_size 256k;
proxy_max_temp_file_size 256k;
    }
```

 c、nginx proxy 具体配置详解

```
proxy_pass ：真实服务器
proxy_redirect ：如果真实服务器使用的是的真实IP:非默认端口。则改成IP：默认端口。
proxy_set_header：重新定义或者添加发往后端服务器的请求头
proxy_set_header X-Real-IP ：启用客户端真实地址（否则日志中显示的是代理在访问网站）
proxy_set_header X-Forwarded-For：记录代理地址

proxy_connect_timeout：:后端服务器连接的超时时间发起三次握手等候响应超时时间
proxy_send_timeout：后端服务器数据回传时间就是在规定时间之内后端服务器必须传完所有的数据
proxy_read_timeout ：nginx接收upstream（上游/真实） server数据超时, 默认60s, 如果连续的60s内没有收到1个字节, 连接关闭。像长连接

proxy_buffering on;开启缓存
proxy_buffer_size：proxy_buffer_size只是响应头的缓冲区
proxy_buffers 4 128k; 内容缓冲区域大小
proxy_busy_buffers_size 256k; 从proxy_buffers划出一部分缓冲区来专门向客户端传送数据的地方
proxy_max_temp_file_size 256k;超大的响应头存储成文件。
```

d、 测试
curl http://10.3.134.240
200

配置了 real-ip后在 backend上看到的日志
10.3.134.240 - - {28/Apr/2021:10:02:24 +0800} "GET /favicon.ico HTTP/1.0" 404 571 "http://10.3.134.240/" 
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.85 Sa
fari/537.36 Edg/90.0.818.46" "GZgz2101.." "10.3.134.26"



##### 6.Nginx负载均衡

proxy 10.3.134.240
backend1： 10.3.134.200
backend2： 10.3.134.233

1. 在backbend上部署服务并启动和测试[最好用proxy测试]
```curl http://10.3.134.200```
200
```curl http://10.3.134.233```
233

2. 配置代理服务器
```
upstream myweb { 
      server 10.3.134.200;
      server 10.3.134.233;
    }
 server {
        ....
        location / {         
           proxy_pass  http://myweb;  #请求转向 myweb 定义的服务器列表         
        } 

nginx -s reload 
```

3. 测试
```curl http://10.3.134.240```
多次访问后发现，后端机器接受到的请求是平均的



###### 1、负载均衡算法

```
upstream 支持4种负载均衡调度算法:
A、`轮询(默认)`:每个请求按时间顺序逐一分配到不同的后端服务器;
B、`ip_hash`:每个请求按访问IP的hash结果分配，同一个IP客户端固定访问一个后端服务器。可以保证来自同一ip的请求被打到固定的机器上，
    可以解决session问题。
C、`url_hash`:按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器。后台服务器为缓存的时候效率。
D、`fair`:这是比上面两个更加智能的负载均衡算法。此种算法可以依据页面大小和加载时间长短智能地进行负载均衡，也就是根据后端服务器
   的响应时间来分配请求，响应时间短的优先分配。`Nginx`本身是不支持 `fair`的，如果需要使用这种调度算法，必须下载Nginx的 
   `upstream_fair`模块。
```

###### 2、各种负载均衡的优缺点

```
轮询[rr]: 
	优点: 简单易处理
	缺点: 无法考虑后端服务器 的真实情况，导致某台机器有过多的长连接在，而其他机器无连接
ip_hash:
    优点: 可以解决session问题
    缺点: 无法解决局域网大量机器同一出口ip的负载均衡	
url_hash:
    优点: 后台服务器为缓存的时候效率
    缺点: 某一热点资源的并发无法处理，容易负载失衡
fair:
    优点: 更加智能
	缺点: 需要特别加载模块
```

实例测试1

```
1. 热备:热备：如果你有2台服务器，当一台服务器发生事故时，才启用第二台服务器给提供服务。
     服务器处理请求的顺序：AAAAAA突然A挂啦，BBBBBBBBBBBBBB.....****
upstream myweb { 
      server 10.3.134.200;
      server 10.3.134.233 backup;  #热备  
    }

2. 轮询：nginx默认就是轮询其权重都默认为1，服务器处理请求的顺序：ABABABABAB....
upstream myweb { 
      server 10.3.134.200;
      server 10.3.134.233;
    }

3. 加权轮询：跟据配置的权重的大小而分发给不同服务器不同数量的请求。
  如果不设置，则默认为1。下面服务器的请求顺序为：ABBABBABBABBABB....
upstream myweb { 
      server 10.3.134.200  weight=1;
      server 10.3.134.233  weight=2;
    }


4. ip_hash:nginx会让相同的客户端ip请求相同的服务器。
upstream myweb { 
      server 10.3.134.200;
      server 10.3.134.233;
	  ip_hash;
    }

5. nginx负载均衡配置状态参数
	- down，表示当前的server暂时不参与负载均衡。
	- backup，预留的备份机器。当其他所有的非backup机器出现故障或者忙的时候，才会请求backup机器，因此这台机器的压力最轻。
	- max_fails，允许请求失败的次数，默认为1。当超过最大次数时，返回proxy_next_upstream 模块定义的错误。
	- fail_timeout，在经历了max_fails次失败后，暂停服务的时间。max_fails可以和fail_timeout一起使用。

	upstream myweb { 
		server 10.3.134.200 weight=2 max_fails=2 fail_timeout=2;
		server 10.3.134.233 weight=2 max_fails=2 fail_timeout=2;
		}
```



###### 3、nginx配置7层协议及4层协议方法（扩展）

举例讲解下什么是7层协议，什么是4层协议。

（1）7层协议

OSI（Open System Interconnection）是一个开放性的通行系统互连参考模型，他是一个定义的非常好的协议规范，共包含七层协议。直接上图，这样更直观些：

![](C:\Users\蘑菇仔\Desktop\work\小屁孩笔记\image\nginx\20181109183229773.png)



详情不进行仔细讲解，可以自行[百度](https://www.baidu.com/s?wd=%E7%99%BE%E5%BA%A6&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)！

（2）4层协议

TCP/IP协议
之所以说TCP/IP是一个协议族，是因为TCP/IP协议包括TCP、IP、UDP、ICMP、RIP、TELNETFTP、SMTP、ARP、TFTP等许多协议，这些协议一起称为TCP/IP协议。

从协议分层模型方面来讲，TCP/IP由四个层次组成：网络接口层、网络层、传输层、应用层。

![](C:\Users\蘑菇仔\Desktop\work\小屁孩笔记\image\nginx\20181109183459393.png)



（3）协议配置

这里我们举例，在nginx做负载均衡，负载多个服务，部分服务是需要7层的，部分服务是需要4层的，也就是说7层和4层配置在同一个配置文件中。

vim nginx.conf

```
worker_processes  8;

events {
        worker_connections  1024;
}
#7层http负载
http {
        include       mime.types;
        default_type  application/octet-stream;
        sendfile        on;
        keepalive_timeout  65;
        gzip  on;

        #app
        upstream  app.com {
                ip_hash;
                server 172.17.14.2:8080;
                server 172.17.14.3:8080;
        }

        server {
                listen       80;
                server_name  app;
                charset utf-8;
                location / {
                        proxy_pass http://app.com;
                        proxy_set_header Host $host:$server_port;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                }
                error_page   500 502 503 504  /50x.html;
                location = /50x.html {
                        root   html;
                }
        }

        #web
        upstream  web.com {
                ip_hash;
        server 172.17.14.2:8090;
        server 172.17.14.3:8090;
        }
        server {
                listen       81;
                server_name  web;
                charset utf-8;
                location / {
                        proxy_pass http://web.com;
                        proxy_set_header Host $host:$server_port;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                }
                error_page   500 502 503 504  /50x.html;
                location = /50x.html {
                        root   html;
                }
        }
}
```

nginx在1.9.0的时候，增加了一个 stream 模块，用来实现四层协议（网络层和传输层）的转发、代理、负载均衡等。stream模块的用法跟http的用法类似，允许我们配置一组TCP或者UDP等协议的监听，然后通过proxy_pass来转发我们的请求，通过upstream添加多个后端服务，实现负载均衡。

```
#4层tcp负载 
stream {
			upstream myweb {
                hash $remote_addr consistent;
                server 172.17.14.2:8080;
                server 172.17.14.3:8080;
        }
        server {
            listen 82;
            proxy_connect_timeout 10s;
            proxy_timeout 30s;
            proxy_pass myweb;
        }
}
```



#### 8、nginx 会话保持

##### **1、ip_hash**

ip_hash使用源地址哈希算法，将同一客户端的请求总是发往同一个后端服务器，除非该服务器不可用。

ip_hash语法：

```
upstream backend {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com down;
}
```

ip_hash简单易用，但有如下问题：
当后端服务器宕机后，session会丢失；
来自同一局域网的客户端会被转发到同一个后端服务器，可能导致负载失衡；
不适用于CDN网络，不适用于前段还有代理的情况。



##### 2、sticky_cookie_insert

使用sticky_cookie_insert启用会话亲缘关系，这会导致来自同一客户端的请求被传递到一组服务器的同一台服务器。与ip_hash不同之处在于，它不是基于IP来判断客户端的，而是基于cookie来判断。因此可以避免上述ip_hash中来自同一局域网的客户端和前段代理导致负载失衡的情况。(需要引入第三方模块才能实现)
语法：

```
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    sticky_cookie_insert srv_id expires=1h domain=3evip.cn path=/;
}

说明：
expires：设置浏览器中保持cookie的时间
domain：定义cookie的域
path：为cookie定义路径
```



**3、jvm_route方式** 

​       jvm_route是通过session_cookie这种方式来实现session粘性。将特定会话附属到特定tomcat上，从而解决session不同步问题，但是无法解决宕机后会话转移问题。如果在cookie和url中并没有session，则这只是个简单的round-robin负载均衡。

　　jvm_route的原理

- 一开始请求过来，没有带session的信息，jvm_route就根据round robin的方法，发到一台Tomcat上面
- Tomcat添加上session信息，并返回给客户
- 用户再次请求，jvm_route看到session中有后端服务器的名称，他就把请求转到对应的服务器上

　　暂时jvm_route模块还不支持fair的模式。jvm_route的工作模式和fair是冲突的。对于某个特定用户，当一直为他服务的Tomcat宕机后，默认情况下它会重试max_fails的次数，如果还是失败，就重新启用round robin的方式，而这种情况下就会导致用户的session丢失。



4、使用后端服务器自身通过相关机制保持session同步，如：使用数据库、redis、memcached 等做session复制





### 9.nginx 实现动静分离

	http server、application server 
	为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度。
	降低原来单个服务器的压力。 在动静分离的tomcat的时候比较明显，因为tomcat解析静态很慢，
	其实这些原理的话都很好理解，简单来说，就是使用正则表达式匹配过滤，然后交个不同的服务器。
**准备环境**

配置php环境

```
10.3.134.233安装了php nginx
wget https://www.php.net/distributions/php-5.6.33.tar.gz

yum -y install gcc automake autoconf libtool make gcc-c++ glibc libmcrypt-devel mhash-devel libxslt-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel

tar zvxf php-5.6.33.tar.gz
cd php-5.6.33

./configure --prefix=/usr/local/php  --enable-fpm --with-mcrypt  \
--enable-mbstring --disable-pdo --with-curl --disable-debug  --disable-rpath \
--enable-inline-optimization --with-bz2  --with-zlib --enable-sockets \
--enable-sysvsem --enable-sysvshm --enable-pcntl --enable-mbregex \
--with-mhash --enable-zip --with-pcre-regex --with-mysql --with-mysqli \
--with-gd --with-jpeg-dir
 
make && make install

安装后内容放在/usr/local/php目录下  下面是对php-fpm运行用户进行设置

cd /usr/local/php
cp etc/php-fpm.conf.default etc/php-fpm.conf
vi etc/php-fpm.conf

修改
user = www-data
group = www-data
listen = 0.0.0.0:9000

如果www-data用户不存在，那么先添加www-data用户
groupadd www-data
useradd -g www-data www-data

启动php-fpm和nginx

/usr/local/php/sbin/php-fpm 
创建测试php文件

在/usr/local/nginx/html下创建index.php文件，输入如下内容 [简单登录测试页面 https://www.jb51.net/article/172542.htm]

<?php
    echo phpinfo();
?>

```

```
10.3.134.240

        location ~ .*\.(html|gif|jpg|png|bmp|swf|jpeg)$ {
                proxy_pass http://10.3.134.200:80;
        }
        location ~ \.php$ {
                root /usr/share/nginx/html;
                fastcgi_pass 10.3.134.233:9000;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
```

配置项说明

location /  的作用
定义了请求代理的时候nginx去/var/www/html/upload  下寻找index.php 当他找到index.php的时候匹配了下面的正则  location ~ \.php$。

location ~ \.php$   的作用
以php结尾的都以代理的方式转发给web1（10.3.134.233）,http1 去处理，这里http1要去看自己的配置文件 在自己的配置文件中定义网站根目录 /var/www/html/upload  找.index.php  然后处理解析返回给nginx 。

location ~ .*\.(html|gif|jpg|png|bmp|swf|jpeg)$  的作用
以html等等的静态页面都交给web2（10.3.134.200）来处理 ，web2 去找自己的网站目录 然后返回给nginx 。
两个 web 放的肯定是一样的目录，只不过每个服务器的任务不一样。
代理本身要有网站的目录，因为最上面的 location / 先生效   如果没有目录 会直接提示找不到目录 不会再往下匹配。









### 10.nginx 防盗链问题

##### 1、nginx 防止网站资源被盗用模块

```ngx_http_referer_module```

**如何区分哪些是不正常的用户？**

​    HTTP Referer是Header的一部分，当浏览器向Web服务器发送请求的时候，一般会带上Referer，

告诉服务器我是从哪个页面链接过来的，服务器借此可以获得一些信息用于处理，例如防止未经允许

的网站盗链图片、文件等。因此HTTP Referer头信息是可以通过程序来伪装生成的，所以通过Referer

信息防盗链并非100%可靠，但是，它能够限制大部分的盗链情况。



```
三种策略
- none : 允许没有http_refer的请求访问资源；
- blocked : 允许不是http://开头的，不带协议的请求访问资源；
- server_names : 只允许指定ip/域名来的请求访问资源（白名单）；
```



#####  2.防盗链配置

**配置要点**

```
# 日志格式添加"$http_referer"
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_x_forwarded_for"';
# valid_referers 使用方式                         
Syntax: 	valid_referers none | blocked | server_names | string ...;
Default: 	—
Context: server, location
```

```
[root@localhost ~]# vim /etc/nginx/conf.d/nginx.conf
        location / {
                root /usr/share/nginx/html;
                index index.html index.htm;

                valid_referers  *.qf.com 192.168.19.135;
                if ($invalid_referer) {
                   return 403;
                }
        }


[root@localhost ~]# vim /etc/nginx/conf.d/nginx.conf
        location ~ .*\.(gif|jpg|png|jpeg)$ {
                root  /usr/share/nginx/html;                  
                valid_referers none blocked qf.com 192.168.19.135;
                if ($invalid_referer) {
                    return 403;
                }
         }

```

##### 3.其他配置

**完全防盗**

```
location ~ .*\.(gif|jpg|png|jpeg)$ {
    valid_referers 192.168.95.134 *.baidu.com *.google.com;
    if ($invalid_referer) {
        rewrite ^/ http://192.168.95.134/fdl.jpg
        #return 403;
    }
    root  /var/www/html/images;
}
```

**直接访问图片链接可以下载**

```
location ~* \.(gif|jpg|png|bmp)$ {
       valid_referers none blocked  *.qf.com  ~tianyun  ~\.google\./ ~\.baidu\./;
       if ($invalid_referer) {
           return 403;
           #rewrite .* http://qf.com/403.jpg;
    }
}
```



额外知识

```
CDN  
CDN的全称是Content Delivery Network，即内容分发网络

国内cdn厂商、七牛云、chinacache、阿里、百度、腾讯、亿速云
```





### 11.nginx 地址重写 rewrite

##### 1.什么是Rewrite

​	Rewrite对称URL Rewrite，即URL重写，就是把传入Web的请求重定向到其他URL的过程。

- URL Rewrite最常见的应用是URL伪静态化，是将动态页面显示为静态页面方式的一种技术。比如
  http://www.123.com/news/index.php?id=123 使用URLRewrite 转换后可以显示为 http://www.123
  .com/news/123.html 对于追求完美主义的网站设计师，就算是网页的地址也希望看起来尽量简洁明快。
  理论上，搜索引擎更喜欢静态页面形式的网页，搜索引擎对静态页面的评分一般要高于动态页面。所
  以，UrlRewrite可以让我们网站的网页更容易被搜索引擎所收录。

- 从安全角度上讲，如果在URL中暴露太多的参数，无疑会造成一定量的信息泄漏，可能会被一些黑客
  利用，对你的系统造成一定的破坏，所以静态化的URL地址可以给我们带来更高的安全性。

- 实现网站地址跳转，例如用户访问360buy.com，将其跳转到jd.com。例如当用户访问tianyun.com的
  80端口时，将其跳转到443端口。

#####  2、Rewrite 相关指令

- **Nginx Rewrite 相关指令有 if、rewrite、set、return**

###### 2.1、if 语句

- 应用环境

```
server，location
```

- 语法

```
if (condition) { … }
if 可以支持如下条件判断匹配符号
~ 					正则匹配 (区分大小写)
~* 				    正则匹配 (不区分大小写)
!~                  正则不匹配 (区分大小写)
!~*		            正则不匹配  (不区分大小写)
-f 和!-f 		    用来判断是否存在文件
-d 和!-d 		    用来判断是否存在目录
-e 和!-e 		    用来判断是否存在文件或目录
-x 和!-x 		    用来判断文件是否可执行

在匹配过程中可以引用一些Nginx的全局变量
$args				请求中的参数;
$document_root	    针对当前请求的根路径设置值;
$host				请求信息中的"Host"，如果请求中没有Host行，则等于设置的服务器名;
$limit_rate			对连接速率的限制;
$request_method		请求的方法，比如"GET"、"POST"等;
$remote_addr		客户端地址;
$remote_port		客户端端口号;
$remote_user		客户端用户名，认证用;
$request_filename   当前请求的文件路径名（带网站的主目录/usr/local/nginx/html/images /a.jpg）
$request_uri		当前请求的文件路径名（不带网站的主目录/images/a.jpg）
$query_string		与$args相同;
$scheme				用的协议，比如http或者是https
$server_protocol	请求的协议版本，"HTTP/1.0"或"HTTP/1.1";
$server_addr 		服务器地址，如果没有用listen指明服务器地址，使用这个变量将发起一次系统调用以取得地址(造成资源浪费);
$server_name		请求到达的服务器名;
$document_uri 		与$uri一样，URI地址;
$server_port 		请求到达的服务器端口号;
```

###### 2.2、Rewrite flag

**rewrite**  指令根据表达式来重定向URI，或者修改字符串。可以应用于**server,location, if**环境下每行rewrite指令最后跟一个flag标记，支持的flag标记有：

```
last 			    相当于Apache里的[L]标记，表示完成rewrite
break 				本条规则匹配完成后，终止匹配，不再匹配后面的规则
redirect 			返回302临时重定向，浏览器地址会显示跳转后的URL地址
permanent 		    返回301永久重定向，浏览器地址会显示跳转后URL地址
```

###### 2.3、Rewrite匹配参考示例

```
示例1：
# http://www.tianyun.com/a/1.html ==> http://www.tianyun.com/b/2.html
location /a {
    rewrite .* /b/2.html permanent;
    #return 301 /b/2.html;
}

例2：
# http://www.tianyun.com/2019/a/1.html ==> http://www.tianyun.com/2018/a/1.html
# http://www.tianyun.com/2019/a/2.html ==> http://www.tianyun.com/2018/a/2.html
# http://www.tianyun.com/2019/a/3.html ==> http://www.tianyun.com/2018/a/3.html
location /2019 {
     rewrite ^/2019/(.*)$ /2018/$1 permanent;
}


例3：
# http://www.qf.com/a/1.html ==> http://jd.com
if ( $host ~* qf.com ) {
	rewrite .*	http://jd.com permanent;
}

例4：
# http://www.qf.com/a/1.html ==> http://jd.com/a/1.html
if ( $host ~* qf.com ) {
	rewrite  .*	 http://jd.com$request_uri permanent;
}

例5: 在访问目录后添加/  (如果目录后已有/，则不加/)
# http://www.tianyun.com/test/
# $1: /a/b
# $2: c
# http://$host$1$2/
if (-d $request_filename) {
    rewrite ^(.*)([^/])$ http://$host$1$2/ permanent;
}

www.tianyun.com/abc   root /usr/loca/nginx/html  
request_uri /abc 
request_filename        /usr/loca/nginx/html/abc 

/ab  c   http://www.tianyun.com/abc/ 


例6：
# http://www.tianyun.com/login/tianyun.html ==>  http://www.tianyun.com/reg/login.php?user=tianyun

location /login {
           rewrite ^/login/(.*)\.html$ /reg/login.php?user=$1;
        }

例7：
#http://www.tianyun.com/qf/11-22-33/1.html  ==>  http://www.tianyun.com/qf/11/22/33/1.html
location /qf {
            rewrite ^/qf/([0-9]+)-([0-9]+)-([0-9]+)(.*)$ /qf/$1/$2/$3$4 permanent;
        }
```

###### 2.4、set 指令

set 指令是用于定义一个变量，并且赋值

**应用环境：**

```
server,location,if
```

**应用示例**

```
例8：
#http://alice.tianyun.com ==> http://www.tianyun.com/alice
#http://jack.tianyun.com ==> http://www.tianyun.com/jack

[root@localhost html]# mkdir jack alice
[root@localhost html]# echo jack.... > jack/index.html
[root@localhost html]# echo alice... > alice/index.html

a. DNS实现泛解析
*   		IN      A			    网站IP

b. nginx Rewrite
if ($host ~* "^www.tianyun.com$" ) {
      break;
  }

if ($host ~* "^(.*)\.tianyun\.com$" ) {
      set $user $1;
      rewrite .* http://www.tianyun.com/$user permanent;
  }
```

###### 2.5、return 指令

return 指令用于返回状态码给客户端

- **应用环境：**

```
server，location，if
```

- **应用示例：**

```
例9：如果访问的.sh结尾的文件则返回403操作拒绝错误
location ~* \.sh$ {
	return 403;
	#return 301 http://www.tianyun.com;
}

例10：80 ======> 443
server {
        listen      80;
        server_name  www.tianyun.com tianyun.com;
        return     301  https://www.tianyun.com$request_uri;
        }
server {
        listen      443 ssl;
        server_name  www.tianyun.com;
        ssl  on;
        ssl_certificate      /usr/local/nginx/conf/cert.pem;
        ssl_certificate_key  /usr/local/nginx/conf/cert.key;
        location / {
            root html;
            index index.html index.php;
        }
    }

[root@localhost html]# curl -I http://www.tianyun.com
HTTP/1.1 301 Moved Permanently
Server: nginx/1.10.1
Date: Tue, 26 Jul 2016 15:07:50 GMT
Content-Type: text/html
Content-Length: 185
Connection: keep-alive
Location: https://www.tianyun.com/
```



---

不同用户访问同一网站，看到的界面不同
1. 用来做线上灰度测试
2. 针对区域性业务
3. 黑白名单用户

```
server {
    listen   80;
    server_name _;

   location / {
    set $PASS 10.3.134.240;                       # PASS=10.3.134.240
    if ( $remote_addr ~ 10.3.134.23 ) {
    set $PASS 10.3.134.233;                       # PASS=10.3.134.233
   }

   proxy_pass http://$PASS;
 }
}
```



---



##### 3、last,break详解

![](C:\Users\蘑菇仔\Desktop\work\小屁孩笔记\image\nginx\last.png)

```
   location / {
    root /usr/share/nginx/html;
    index index.html;
}
   location /break/ {
    root /usr/share/nginx/html;
    rewrite .* /test/break.html break;
}

  location /last/ {
   root /usr/share/nginx/html;
   rewrite .* /test/last.html last;
}

  location /test/ {
   root /usr/share/nginx/html;
   rewrite  .* /test/test.html break ;

}
！！！注意上一步实验 https缓存影响


[root@localhost html]# mkdir test
[root@localhost html]# echo 'break' > test/break.html
[root@localhost html]# echo 'last' > test/last.html
[root@localhost html]# echo 'test...' > test/test.html

http://192.168.10.33/break/break.html
http://192.168.10.33/last/last.html
```

**注意：**

- last 标记在本条 rewrite 规则执行完后，会对其所在的 server { … } 标签重新发起请求;

- break 标记则在本条规则匹配完成后，停止匹配，不再做后续的匹配；

- 使用 alias 指令时，必须使用 last；

- 使用 proxy_pass 指令时,则必须使用break。



















### 12.nginx location 指令详解

Nginx 的 HTTP 配置主要包括三个区块，结构如下：

```
http { 						# 这个是协议级别
　　include mime.types;
　　default_type application/octet-stream;
　　keepalive_timeout 65;
　　gzip on;
　　　　server {			 # 这个是服务器级别
　　　　　　listen 80;
　　　　　　server_name localhost;
　　　　　　　　location / {  # 这个是请求级别
　　　　　　　　　　root html;
　　　　　　　　　　index index.html index.htm;
　　　　　　　　}
　　　　　　}
}
```

##### 1、**location 区段**

- location 是在 server 块中配置，根据不同的 URI 使用不同的配置，来处理不同的请求。
- location 是有顺序的，会被第一个匹配的location 处理。
- 基本语法如下：

```
location [=|~|~*|^~|@] pattern{……}
```

##### 2、**location 前缀含义**

```
=    表示精确匹配，优先级也是最高的 
^~   表示uri以某个常规字符串开头,理解为匹配url路径即可 
~    表示区分大小写的正则匹配  
~*   表示不区分大小写的正则匹配
!~   表示区分大小写不匹配的正则
!~*  表示不区分大小写不匹配的正则
/    通用匹配，任何请求都会匹配到
@    内部服务跳转
```

##### 3、location 配置示例

1、没有修饰符 表示：必须以指定模式开始

```
server {
　　server_name qf.com;
　　location /abc {
　　　　……
　　}
}

那么，如下是对的：
http://qf.com/abc
http://qf.com/abc?p1
http://qf.com/abc/
http://qf.com/abcde 
```

2、=表示：必须与指定的模式精确匹配

```
server {
server_name qf.com
　　location = /abc {
　　　　……
　　}
}
那么，如下是对的：
http://qf.com/abc
http://qf.com/abc?p1
如下是错的：
http://qf.com/abc/
http://qf.com/abcde
```

3、~ 表示：指定的正则表达式要区分大小写

```
server {
server_name qf.com;
　　location ~ ^/abc$ {
　　　　……
　　}
}
那么，如下是对的：
http://qf.com/abc
http://qf.com/abc?p1=11&p2=22
如下是错的：
http://qf.com/ABC
http://qf.com/abc/
http://qf.com/abcde
```

4、~* 表示：指定的正则表达式不区分大小写

```
server {
server_name qf.com;
location ~* ^/abc$ {
　　　　……
　　}
}
那么，如下是对的：
http://qf.com/abc
http://qf..com/ABC
http://qf..com/abc?p1=11&p2=22
如下是错的：
http://qf..com/abc/
http://qf..com/abcde
```

5、^~ ：类似于无修饰符的行为，也是以指定模式开始，不同的是，如果模式匹配，那么就停止搜索其他模式了。
6、@ ：定义命名 location 区段，这些区段客户段不能访问，只可以由内部产生的请求来访问，如try_files或error_page等

**查找顺序和优先级**

**1：带有“=“的精确匹配优先**

**2：没有修饰符的精确匹配**

**3：正则表达式按照他们在配置文件中定义的顺序**

**4：带有“^~”修饰符的，开头匹配**

**5：带有“~” 或“~\*” 修饰符的，如果正则表达式与URI匹配**

**6：没有修饰符的，如果指定字符串与URI开头匹配**

```
location 区段匹配示例

location = / {
　　# 只匹配 / 的查询.
　　[ configuration A ]
}
location / {
　　# 匹配任何以 / 开始的查询，但是正则表达式与一些较长的字符串将被首先匹配。
　　[ configuration B ]
}
location ^~ /images/ {
　　# 匹配任何以 /images/ 开始的查询并且停止搜索，不检查正则表达式。
　　[ configuration C ]
}
location ~* \.(gif|jpg|jpeg)$ {
　　# 匹配任何以gif, jpg, or jpeg结尾的文件，但是所有 /images/ 目录的请求将在Configuration C中处理。
　　[ configuration D ]
} 
各请求的处理如下例：
	/ → configuration A
	/documents/document.html → configuration B
	/images/1.gif → configuration C
	/documents/1.jpg → configuration D
```

优先级测试

```
    server {
        listen       80 default_server;
        server_name  _;


        location / {
        root     /usr/share/nginx/html;
        index index.html;
        }

        location = / {
        root /usr/share/nginx/html1;
        index index.html;
        }

       location ^~ /image/ {
        root /usr/share/nginx/html/img;
        index index.html;
        }

       location ~* \.(gif|jpg|jpeg)$ {
        root /usr/share/nginx/html/gz80;
        index index.html;
       }

    }
```



root 、alias 指令区别

```
location /img/ {
    alias /var/www/image/;
}
#若按照上述配置的话，则访问/img/目录里面的文件时，ningx会自动去/var/www/image/目录找文件
location /img/ {
    root /var/www/image;
}
#若按照这种配置的话，则访问/img/目录下的文件时，nginx会去/var/www/image/img/目录下找文件。] 
```
	alias 是一个目录别名的定义，
	root 则是最上层目录的定义。
	还有一个重要的区别是alias后面必须要用“/”结束，否则会找不到文件的,而root则可有可无。






### 13、nginx 日志配置

##### 1、nginx 日志介绍

`nginx` 有一个非常灵活的日志记录模式,每个级别的配置可以有各自独立的访问日志, 所需日志模块 `ngx_http_log_module` 的支持，日志格式通过 `log_format` 命令来定义，日志对于统计和排错是非常有利的，下面总结了 `nginx` 日志相关的配置 包括 `access_log`、`log_format`、`open_log_file_cache`、`log_not_found`、`log_subrequest`、`rewrite_log`、`error_log`。

##### 2、access_log 指令

访问日志主要记录客户端的请求。客户端向 Nginx 服务器发起的每一次请求都记录在这里。客户端IP，浏览器信息，referer，请求处理时间，请求URL等都可以在访问日志中得到。当然具体要记录哪些信息，你可以通过`log_format`指令定义。

**语法：**

```
# 设置访问日志
access_log path [format [buffer=size] [gzip[=level]] [flush=time] [if=condition]];
# 关闭访问日志
access_log off; 
```

- **path** 指定日志的存放位置。
- **format** 指定日志的格式。默认使用预定义的`combined`。
- **buffer** 用来指定日志写入时的缓存大小。默认是64k。
- **gzip** 日志写入前先进行压缩。压缩率可以指定，从1到9数值越大压缩比越高，同时压缩的速度也越慢。默认是1。
- **flush** 设置缓存的有效时间。如果超过flush指定的时间，缓存中的内容将被清空。
- **if** 条件判断。如果指定的条件计算为0或空字符串，那么该请求不会写入日志。

**作用域：**

可以应用`access_log`指令的作用域分别有`http`，`server`，`location`，`limit_except`。也就是说，在这几个作用域外使用该指令，Nginx会报错。

**基本用法：**

```
access_log /var/logs/nginx-access.log
```

该例子指定日志的写入路径为`/var/logs/nginx-access.log`，日志格式使用默认的`combined`。

```
access_log /var/logs/nginx-access.log buffer=32k gzip flush=1m
```

该例子指定日志的写入路径为`/var/logs/nginx-access.log`，日志格式使用默认的`combined`，指定日志的缓存大小为 32k，日志写入前启用 gzip 进行压缩，压缩比使用默认值 1，缓存数据有效时间为1分钟。

##### 3、log_format 指令

Nginx 预定义了名为 `combined` 日志格式，如果没有明确指定日志格式默认使用该格式：

```
log_format combined '$remote_addr - $remote_user [$time_local] '
                    '"$request" $status $body_bytes_sent '
                    '"$http_referer" "$http_user_agent"';
```

如果不想使用Nginx预定义的格式，可以通过`log_format`指令来自定义。

语法

```
log_format name [escape=default|json] string ...;
```

- **name** 格式名称。在 access_log 指令中引用。
- **escape** 设置变量中的字符编码方式是`json`还是`default`，默认是`default`。
- **string** 要定义的日志格式内容。该参数可以有多个。参数中可以使用Nginx变量。

`log_format` 指令中常用的一些变量：

| 变量                  | 含义                                                         |
| --------------------- | ------------------------------------------------------------ |
| $bytes_sent           | 发送给客户端的总字节数                                       |
| $body_bytes_sent      | 发送给客户端的字节数，不包括响应头的大小                     |
| $connection           | 连接序列号                                                   |
| $connection_requests  | 当前通过连接发出的请求数量                                   |
| $msec                 | 日志写入时间，单位为秒，精度是毫秒                           |
| $pipe                 | 如果请求是通过http流水线发送，则其值为"p"，否则为“."         |
| $request_length       | 请求长度（包括请求行，请求头和请求体）                       |
| $request_time         | 请求处理时长，单位为秒，精度为毫秒，从读入客户端的第一个字节开始，直到把最后一个字符发送张客户端进行日志写入为止 |
| $status               | 响应状态码                                                   |
| $time_iso8601         | 标准格式的本地时间,形如“2017-05-24T18:31:27+08:00”           |
| $time_local           | 通用日志格式下的本地时间，如"24/May/2017:18:31:27 +0800"     |
| $http_referer         | 请求的referer地址。                                          |
| $http_user_agent      | 客户端浏览器信息。                                           |
| $remote_addr          | 客户端IP                                                     |
| $http_x_forwarded_for | 当前端有代理服务器时，设置web节点记录客户端地址的配置，此参数生效的前提是代理服务器也要进行相关的x_forwarded_for设置。 |
| $request              | 完整的原始请求行，如 "GET / HTTP/1.1"                        |
| $remote_user          | 客户端用户名称，针对启用了用户认证的请求                     |
| $request_uri          | 完整的请求地址，如 "https://qf.com/"                         |

自定义日志格式的使用：

```
access_log /var/logs/nginx-access.log main

log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';
```

使用`log_format`指令定义了一个`main`的格式，并在`access_log`指令中引用了它。假如客户端有发起请求：`https://qf.com/`，我们看一下我截取的一个请求的日志记录:

```
192.168.95.134 - - [20/Feb/2019:12:12:14 +0800] "GET / HTTP/1.1" 200 190 "-" "Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Mobile Safari/537.36" "-"
```

我们看到最终的日志记录中`$remote_user`、`$http_referer`、`$http_x_forwarded_for`都对应了一个`-`，这是因为这几个变量为空。

##### 5、error_log 指令

错误日志在Nginx中是通过`error_log`指令实现的。该指令记录服务器和请求处理过程中的错误信息。

**语法**

配置错误日志文件的路径和日志级别。

```
error_log file [level];
Default:	
error_log logs/error.log error;
```

`file` 参数指定日志的写入位置。

`level` 参数指定日志的级别。level可以是`debug`, `info`, `notice`, `warn`, `error`, `crit`, `alert`,`emerg`中的任意值。可以看到其取值范围是按紧急程度从低到高排列的。只有日志的错误级别等于或高于level指定的值才会写入错误日志中。默认值是`error`。

**基本用法**

```
error_log /var/logs/nginx/nginx-error.log
```

配置段：`main`， `http`,  `mail`,  `stream`,  `server`, `location`作用域。

例子中指定了错误日志的路径为：`/var/logs/nginx/nginx-error.log`，日志级别使用默认的 `error`。

##### 6、open_log_file_cache 指令

每一条日志记录的写入都是先打开文件再写入记录，然后关闭日志文件。如果你的日志文件路径中使用了变量，如  `access_log /var/logs/$host/nginx-access.log`，为提高性能，可以使用`open_log_file_cache`指令设置日志文件描述符的缓存。

**语法**

```
open_log_file_cache max=N [inactive=time] [min_uses=N] [valid=time];

默认值: 
open_log_file_cache off;
```

- **max** 设置缓存中最多容纳的文件描述符数量，如果被占满，采用LRU算法将描述符关闭。
- **inactive** 设置缓存存活时间，默认是10s。
- **min_uses** 在**inactive**时间段内，日志文件最少使用几次，该日志文件描述符记入缓存，默认是1次。
- **valid**：设置多久对日志文件名进行检查，看是否发生变化，默认是60s。
- **off**：不使用缓存。默认为off。

**基本用法**

```
open_log_file_cache max=1000 inactive=20s valid=1m min_uses=2;
```

配置段:`http`、`server`、`location`作用域中。

例子中，设置缓存最多缓存1000个日志文件描述符，20s内如果缓存中的日志文件描述符至少被被访问2次，才不会被缓存关闭。每隔1分钟检查缓存中的文件描述符的文件名是否还存在。

##### 7、log_not_found 指令

是否在`error_log`中记录不存在的错误。默认是

**基本语法:** 

```
log_not_found on | off;
默认值: 
log_not_found on;
```

配置段: `http`, `server`, `location`作用域。

##### 8、log_subrequest 指令

是否在`access_log`中记录子请求的访问日志。默认不记录

**基本语法:** 

```
log_subrequest on | off;

默认值: 
log_subrequest off;
```

配置段:  `http`, ` server`, `location`作用域。

##### 9、rewrite_log 指令

由`ngx_http_rewrite_module`模块提供的。用来记录重写日志的。对于调试重写规则建议开启，启用时将在`error log`中记录`notice`级别的重写日志。
**基本语法:** 

```
rewrite_log on | off;

默认值: 
rewrite_log off;
```

配置段:  `http`,  `server`, `location`,  `if`作用域。

##### 10、nginx 日志配置总结

Nginx中通过`access_log`和`error_log`指令配置访问日志和错误日志，通过`log_format`我们可以自定义日志格式。如果日志文件路径中使用了变量，我们可以通过`open_log_file_cache` 指令来设置缓存，提升性能。其他的根据自己的使用场景定义。





### 14、nginx 错误页面配置

nginx错误页面包括404 403 500 502 503 504等页面，只需要在server中增加以下配置即可：

                error_page  404 403 500 502 503 504  /404.html;
                location = /404.html {
                        root   /usr/local/nginx/html;
                }
图片转码base64的
http://tool.chinaz.com/tools/imgtobase

**注意：**

/usr/local/nginx/html/ 路径下必须有404.html这个文件！！！

404.html上如果引用其他文件的png或css就会有问题，显示不出来，因为其他文件的访问也要做配置；
 为了简单，可以将css嵌入文件中，图片用base编码嵌入；如下：

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
        <title>404</title>
        <style>
            .layout-table{display:table;height:100%;width:100%;vertical-align: middle;margin-top:150px}
            .layout-table-cell{display: table-cell;vertical-align: middle;text-align:center}
            .layout-tip{font-size:28px;color:#373737;margin: 0 auto;margin-top:16px;border-bottom: 1px solid #eee;padding-bottom: 20px;width: 360px;}
            #tips{font-size:18px;color:#666666;margin-top:16px;}
        </style>
    </head>
    <body class="layui-layout-body">
        <div class="layui-layout layui-layout-admin">
            
            <div class="layui-body">
                <div class="layout-table">
                    <div class="layout-table-cell">
                        <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAV4AAACMCAYAAAA0qsGKAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyBpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuMC1jMDYwIDYxLjEzNDc3NywgMjAxMC8wMi8xMi0xNzozMjowMCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNSBXaW5kb3dzIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOkIwRDQ0ODRFMzMyODExRThBQ0Q5Q0UyNkNCMkE4MDk0IiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOkIwRDQ0ODRGMzMyODExRThBQ0Q5Q0UyNkNCMkE4MDk0Ij4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6QjBENDQ4NEMzMzI4MTFFOEFDRDlDRTI2Q0IyQTgwOTQiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6QjBENDQ4NEQzMzI4MTFFOEFDRDlDRTI2Q0IyQTgwOTQiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz4Qz6opAAAU5klEQVR42uxd3XXjthKe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" class="layout-img">
                        <p class="layout-tip">哎呀，找不到该页面啦！</p>
                        <p id="tips">请检查您的网络连接是否正常或者输入的网址是否正确</p>
                    </div>
                </div>
            </div>
        </div>
        
    </body>
</html>
```





### 15、nginx 流量控制

**流量限制** (rate-limiting)，是Nginx中一个非常实用，却经常被错误理解和错误配置的功能。我们可以用来限制用户在给定时间内HTTP请求的数量。请求，可以是一个简单网站首页的GET请求，也可以是登录表单的 POST 请求。流量限制可以用作安全目的，比如可以减慢暴力密码破解的速率。通过将传入请求的速率限制为真实用户的典型值，并标识目标URL地址(通过日志)，还可以用来抵御 DDOS 攻击。更常见的情况，该功能被用来保护上游应用服务器不被同时太多用户请求所压垮。

以下将会介绍Nginx的 **流量限制** 的基础知识和高级配置，”流量限制”在Nginx Plus中也适用。

##### 1、Nginx如何限流

Nginx的”流量限制”使用漏桶算法(leaky bucket algorithm)，该算法在通讯和分组交换计算机网络中广泛使用，用以处理带宽有限时的突发情况。就好比，一个桶口在倒水，桶底在漏水的水桶。如果桶口倒水的速率大于桶底的漏水速率，桶里面的水将会溢出；同样，在请求处理方面，水代表来自客户端的请求，水桶代表根据”先进先出调度算法”(FIFO)等待被处理的请求队列，桶底漏出的水代表离开缓冲区被服务器处理的请求，桶口溢出的水代表被丢弃和不被处理的请求。

![](https://user-gold-cdn.xitu.io/2018/10/31/166c9a2acf4d2b63?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



##### 2、配置基本的限流

“流量限制”配置两个主要的指令，`limit_req_zone`和`limit_req`，如下所示：

```
limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;
server {
	location /login/ {
		limit_req zone=mylimit;

		proxy_pass http://my_upstream;
	}
}
```

`limit_req_zone`指令定义了流量限制相关的参数，而`limit_req`指令在出现的上下文中启用流量限制(示例中，对于”/login/”的所有请求)。

`limit_req_zone`指令通常在HTTP块中定义，使其可在多个上下文中使用，它需要以下三个参数：

- **Key** - 定义应用限制的请求特性。示例中的 Nginx 变量`$binary_remote_addr`，保存客户端IP地址的二进制形式。这意味着，我们可以将每个不同的IP地址限制到，通过第三个参数设置的请求速率。(使用该变量是因为比字符串形式的客户端IP地址`$remote_addr`，占用更少的空间)
- **Zone** - 定义用于存储每个IP地址状态以及被限制请求URL访问频率的共享内存区域。保存在内存共享区域的信息，意味着可以在Nginx的worker进程之间共享。定义分为两个部分：通过`zone=keyword`标识区域的名字，以及冒号后面跟区域大小。16000个IP地址的状态信息，大约需要1MB，所以示例中区域可以存储160000个IP地址。
- **Rate** - 定义最大请求速率。在示例中，速率不能超过每秒10个请求。Nginx实际上以毫秒的粒度来跟踪请求，所以速率限制相当于每100毫秒1个请求。因为不允许”突发情况”(见下一章节)，这意味着在前一个请求100毫秒内到达的请求将被拒绝。

> 当Nginx需要添加新条目时存储空间不足，将会删除旧条目。如果释放的空间仍不够容纳新记录，Nginx将会返回 **503状态码**(Service Temporarily Unavailable)。另外，为了防止内存被耗尽，Nginx每次创建新条目时，最多删除两条60秒内未使用的条目。

`limit_req_zone`指令设置流量限制和共享内存区域的参数，但实际上并不限制请求速率。所以需要通过添加

`limit_req`指令，将流量限制应用在特定的`location`或者`server`块。在上面示例中，我们对`/login/`请求进行流量限制。

现在每个IP地址被限制为每秒只能请求10次`/login/`，更准确地说，在前一个请求的100毫秒内不能请求该URL。

##### 3、处理突发

如果我们在100毫秒内接收到2个请求，怎么办？对于第二个请求，Nginx将给客户端返回状态码503。这可能并不是我们想要的结果，因为应用本质上趋向于突发性。相反地，我们希望缓冲任何超额的请求，然后及时地处理它们。我们更新下配置，在`limit_req`中使用`burst`参数：

```
location /login/ {
	limit_req zone=mylimit burst=20;
	proxy_pass http://my_upstream;
}
```

`burst`参数定义了超出zone指定速率的情况下(示例中的`mylimit`区域，速率限制在每秒10个请求，或每100毫秒一个请求)，客户端还能发起多少请求。上一个请求100毫秒内到达的请求将会被放入队列，我们将队列大小设置为20。

这意味着，如果从一个给定IP地址发送21个请求，Nginx会立即将第一个请求发送到上游服务器群，然后将余下20个请求放在队列中。然后每100毫秒转发一个排队的请求，只有当传入请求使队列中排队的请求数超过20时，Nginx才会向客户端返回503。

##### 4、无延迟的排队

配置`burst`参数将会使通讯更流畅，但是可能会不太实用，因为该配置会使站点看起来很慢。在上面的示例中，队列中的第20个包需要等待2秒才能被转发，此时返回给客户端的响应可能不再有用。要解决这个情况，可以在`burst`参数后添加`nodelay`参数：

```
location /login/ {
	limit_req zone=mylimit burst=20 nodelay;

	proxy_pass http://my_upstream;
}
```

使用`nodelay`参数，Nginx仍将根据`burst`参数分配队列中的位置，并应用已配置的速率限制，而不是清理队列中等待转发的请求。相反地，当一个请求到达“太早”时，只要在队列中能分配位置，Nginx将立即转发这个请求。将队列中的该位置标记为”taken”(占据)，并且不会被释放以供另一个请求使用，直到一段时间后才会被释放(在这个示例中是，100毫秒后)。

假设如前所述，队列中有20个空位，从给定的IP地址发出的21个请求同时到达。Nginx会立即转发这个21个请求，并且标记队列中占据的20个位置，然后每100毫秒释放一个位置。如果是25个请求同时到达，Nginx将会立即转发其中的21个请求，标记队列中占据的20个位置，并且返回503状态码来拒绝剩下的4个请求。

现在假设，第一组请求被转发后101毫秒，另20个请求同时到达。队列中只会有一个位置被释放，所以Nginx转发一个请求并返回503状态码来拒绝其他19个请求。如果在20个新请求到达之前已经过去了501毫秒，5个位置被释放，所以Nginx立即转发5个请求并拒绝另外15个。

效果相当于每秒10个请求的“流量限制”。如果希望不限制两个请求间允许间隔的情况下实施“流量限制”，`nodelay`参数是很实用的。

**注意：** 对于大部分部署，我们建议使用`burst`和`nodelay`参数来配置`limit_req`指令。

##### 5、高级配置示例

通过将基本的“流量限制”与其他Nginx功能配合使用，我们可以实现更细粒度的流量限制。

###### 1、白名单

下面这个例子将展示，如何对任何不在白名单内的请求强制执行“流量限制”：

```shell
geo $limit {
	default 		1;
	10.0.0.0/8 		0;
	192.168.0.0/24 	0;
}

map $limit $limit_key {
	0 "";
	1 $binary_remote_addr;
}

limit_req_zone $limit_key zone=req_zone:10m rate=5r/s;

server {
	location / {
		limit_req zone=req_zone burst=10 nodelay;

		# ...
	}
}
```

这个例子同时使用了`geo`和`map`指令。`geo`块将给在白名单中的IP地址对应的`$limit`变量分配一个值0，给其它不在白名单中的分配一个值1。然后我们使用一个映射将这些值转为key，如下：

- 如果`$limit`变量的值是0，`$limit_key`变量将被赋值为空字符串
- 如果`$limit`变量的值是1，`$limit_key`变量将被赋值为客户端二进制形式的IP地址

两个指令配合使用，白名单内IP地址的`$limit_key`变量被赋值为空字符串，不在白名单内的被赋值为客户端的IP地址。当`limit_req_zone`后的第一个参数是空字符串时，不会应用“流量限制”，所以白名单内的IP地址(10.0.0.0/8和192.168.0.0/24 网段内)不会被限制。其它所有IP地址都会被限制到每秒5个请求。

`limit_req`指令将限制应用到**/**的location块，允许在配置的限制上最多超过10个数据包的突发，并且不会延迟转发。

###### 2、location 包含多`limit_req`指令

我们可以在一个location块中配置多个`limit_req`指令。符合给定请求的所有限制都被应用时，意味着将采用最严格的那个限制。例如，多个指令都制定了延迟，将采用最长的那个延迟。同样，请求受部分指令影响被拒绝，即使其他指令允许通过也无济于事。

扩展前面将“流量限制”应用到白名单内IP地址的例子：

```shell
http {
	# ...

	limit_req_zone $limit_key zone=req_zone:10m rate=5r/s;
	limit_req_zone $binary_remote_addr zone=req_zone_wl:10m rate=15r/s;

	server {
		# ...
		location / {
			limit_req zone=req_zone burst=10 nodelay;
			limit_req zone=req_zone_wl burst=20 nodelay;
			# ...
		}
	}
}
```

白名单内的IP地址不会匹配到第一个“流量限制”，而是会匹配到第二个`req_zone_wl`，并且被限制到每秒15个请求。不在白名单内的IP地址两个限制能匹配到，所以应用限制更强的那个：每秒5个请求。

##### 6、配置流量控制相关功能

###### 1、配置日志记录

默认情况下，Nginx会在日志中记录由于流量限制而延迟或丢弃的请求，如下所示：

```shell
2019/02/13 04:20:00 [error] 120315#0: *32086 limiting requests, excess: 1.000 by zone "mylimit", client: 192.168.1.2, server: nginx.com, request: "GET / HTTP/1.0", host: "nginx.com"
```

日志条目中包含的字段：

- limiting requests - 表明日志条目记录的是被“流量限制”请求
- excess - 每毫秒超过对应“流量限制”配置的请求数量
- zone - 定义实施“流量限制”的区域
- client - 发起请求的客户端IP地址
- server - 服务器IP地址或主机名
- request - 客户端发起的实际HTTP请求
- host - HTTP报头中host的值

默认情况下，Nginx以`error`级别来记录被拒绝的请求，如上面示例中的`[error]`所示(Ngin以较低级别记录延时请求，一般是`info`级别)。如要更改Nginx的日志记录级别，需要使用`limit_req_log_level`指令。这里，我们将被拒绝请求的日志记录级别设置为`warn`：

```shell
location /login/ {
	limit_req zone=mylimit burst=20 nodelay;
	limit_req_log_level warn;
	
	proxy_pass http://my_upstream;
} 
```

###### 2、发送到客户端的错误代码

一般情况下，客户端超过配置的流量限制时，Nginx响应状态码为**503(Service Temporarily Unavailable)**。可以使用`limit_req_status`指令来设置为其它状态码(例如下面的**444**状态码):

```shell
location /login/ {
	limit_req zone=mylimit burst=20 nodelay;
	limit_req_status 444;
}
```

###### 3、指定`location`拒绝所有请求

如果你想拒绝某个指定URL地址的所有请求，而不是仅仅对其限速，只需要在`location`块中配置`deny` **all**指令：

```shell
location /foo.php {
	deny all;
}
```

##### 7、nginx 流量控制总结

以上已经涵盖了Nginx和Nginx Plus提供的“流量限制”的很多功能，包括为HTTP请求的不同loation设置请求速率，给“流量限制”配置`burst`和`nodelay`参数。还涵盖了针对客户端IP地址的白名单和黑名单应用不同“流量限制”的高级配置，阐述了如何去日志记录被拒绝和延时的请求。







### 16.nginx访问控制

##### 1、nginx 访问控制模块

（1）基于IP的访问控制：http_access_module
（2）基于用户的信任登录：http_auth_basic_module

##### 2、基于IP的访问控制

###### 1、配置语法

```shell
Syntax：allow address | CIDR | unix: | all;
default：默认无
Context：http，server，location，limit_except

Syntax：deny address | CIDR | unix: | all;
default：默认无
Context：http，server，location，limit_except
```

###### 2、配置测试

修改`/etc/nginx/conf.d/access_mod.conf`内容如下：

```shell
    location ~ ^/admin.html {
        root   /opt/app/code;
		deny 192.168.174.1;
		allow all;
        index  index.html index.htm;
    }
```

虚拟机宿主机IP为`192.168.174.1`，虚拟机IP为`192.168.174.132`，故这里禁止宿主机访问，允许其他所有IP访问。
宿主机访问`http://192.168.174.132/admin.html`，显示`403 Forbidden`。
当然也可以反向配置，同时也可以使用IP网段的配置方式，如`allow 192.168.174.0/24;`，表示满足此网段的IP都可以访问。

###### 3、局限性

`remote_addr`只能记录上一层与服务器直接建立连接的IP地址，若中间有代理，则记录的是代理的IP地址。
`http_x_forwarded_for`可以记录每一层级的IP。
![在这里插入图片描述](D:/a千峰/课件/第三阶段/1-10天-大型网站高并发架构及自动化运维/assets/20181004205405831.jpg)

###### 4、解决方法

（1）采用别的HTTP头信息控制访问，如HTTP_X_FORWARD_FOR（无法避免被改写）
（2）结合geo模块
（3）通过HTTP自定义变量传递

##### 3、基于用户的信任登录

###### 1、配置语法

```shell
Syntax：auth_basic string | off;
default：auth_basic off;
Context：http，server，location，limit_except

Syntax：auth_basic_user_file file;
default：默认无
Context：http，server，location，limit_except
file：存储用户名密码信息的文件。
```

###### 2、配置示例

改名`access_mod.conf`为`auth_mod.conf`，内容如下：

```shell
    location ~ ^/admin.html {
        root   /opt/app/code;
        auth_basic "Auth access test!";
        auth_basic_user_file /etc/nginx/auth_conf;
        index  index.html index.htm;
    }
```

`auth_basic`不为`off`，开启登录验证功能，`auth_basic_user_file`加载账号密码文件。

###### 3、建立口令文件

```shell
[root@web ~]# htpasswd -cm /etc/nginx/auth_conf user10
[root@web ~]# htpasswd -m /etc/nginx/auth_conf user20
[root@web ~]# cat /etc/nginx/auth_conf 
user10:$apr1$Cw6eF/..$MNBh6rvkvsfH9gDZ/kEhg/
user20:$apr1$tb6B8...$y28sfvudhfb4V8xPlvvi//
```

###### 4、访问测试

![在这里插入图片描述](D:/a千峰/课件/第三阶段/1-10天-大型网站高并发架构及自动化运维/assets/20181005105818783.jpg)

###### 5、局限性

（1）用户信息依赖文件方式
（2）操作管理机械，效率低下

###### 6、解决方法

（1）Nginx结合LUA实现高效验证
（2）Nginx和LDAP打通，利用nginx-auth-ldap模块
（3）Nginx只做中间代理，具体认证交给应用