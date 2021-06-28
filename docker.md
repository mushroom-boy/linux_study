docker-ce与docker-ee

docker-ce的社区版，开源的，docker-ee是商业版







# docker安装

删除已安装的Docker,如果没有安装过docker的可以skip

```
    # yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

配置阿里云Docker Yum源

```
    # yum install -y yum-utils device-mapper-persistent-data lvm2 git
    # yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
    # yum clean all && yum makecache fast
```



查看Docker可选安装版本： //看看而已，不执行也行

```
# yum list docker-ce --showduplicates
```



安装Docker

```
# yum install docker-ce -y  
# systemctl start docker
```

docker info

```
如果只有client没有server可能是没启动docker，systemctl start docker一下
Client和Server都要有，如果只有其中一个，就是缺少另一个
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)
  buildx: Build with BuildKit (Docker Inc., v0.5.1-docker)
  scan: Docker Scan (Docker Inc., v0.8.0)

Server:
 Containers: 1
  Running: 0
  Paused: 0
  Stopped: 1
 Images: 1

```

查看docker版本状态： 

```
    # docker -v
    Docker version 1.13.1, build 8633870/1.13.1  
    # docekr version 
```

报错

```
报错1：
    docker info的时候报如下错误
    bridge-nf-call-iptables is disabled

解决1：
    追加如下配置,然后重启系统
    # vim /etc/sysctl.conf   
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    net.bridge.bridge-nf-call-arptables = 1
    
    # sysctl -p
    # docker info   //这时再用docker info查看就会正常了
```

配置镜像加速

```
# mkdir -p /etc/docker
# vim  /etc/docker/daemon.json
{
  "registry-mirrors":["https://10lhoa45.mirror.aliyuncs.com"]
}

# systemctl daemon-reload
# systemctl restart docker
```







# 镜像管理

```
# docker search nginx       //查找镜像
# docker images[image ls]   //查看本地镜像
# docker ps[container ls]   //查看本地都启动的那些容器
# docker pull nginx:tag     //如果没有带tag默认就是latest
```

```
# docker run -itd --name mynginx -p 90:80 -v /tmp/nginx:/usr/share/nginx/html nginx:latest   //创建容器

# docker exec -it container-id/container-name /bin/bash
```

```
搜索基于 centos 操作系统的镜像
    # docker search centos

    按星级搜索镜像：        
    查找 star 数至少为 100 的镜像，默认不加 s 选项找出所有相关 ubuntu 镜像：         
    # docker search  centos -f stars=100    
```

```
查看镜像详情：
# docker image inspect 镜像id

查看本地镜像：     类似 ip addr list --->ip addr ls --> ip addr ---> ip a
		# docker image list
		# docker image ls
		# docker images
		# docker images -q    //仅查看镜像id
```

```
# docker rm ： 删除容器
# docker rmi :  删除镜像
删除所有镜像：
# docker rmi $(docker images -q)
```

```
查看镜像制作的过程：
    相当于dockfile
    # docker history daocloud.io/ubuntu
    
	# dive下载路径curl -OL https://github.com/wagoodman/dive/releases/download/v0.9.2/dive_0.9.2_linux_amd64.rpm
    # rpm -i dive_0.9.2_linux_amd64.rpm
    # dive docker_image_id/docker_image_name
```







# 容器管理

```
# docker run -itd --name container-name  image-name/image-id  [参数]
# docker run -it --restart=always centos:7 /bin/bash
    -i   
        捕获标准输入输出
    -t   
        分配一个终端或控制台
	-d
		放后台
    --restart=always   
        容器随docker engine自启动，因为在重启docker的时候默认容器都会被关闭   
        也适用于create选项
	--rm
        默认情况下，每个容器在退出时，它的文件系统也会保存下来，这样一方面调试会方便些，因为你可以通过查看日志等方式来确定最终状态。另一方面，也可以保存容器所产生的数据。
        但是当你仅仅需要短暂的运行一个容器，并且这些数据不需要保存，你可能就希望Docker能在容器结束时自动清理其所产生的数据。这个时候就需要--rm参数了。注意：--rm 和 -d不能共用
	--name
	     定义容器名字

创建新容器但不启动：
# docker create -it daocloud.io/library/centos:5 /bin/bash
只创建不运行

运行容器
docker run container-id/name

停止容器
docker stop/kill container-id/name
        -15/-9
        
重启容器
docker restart container_id/name

查看容器
docker ps
docker ps -q   //仅查看容器id
docker ps -l   //查看最近启动的
docker ps -l -f status=exited    //查看最近启动异常退出的容器
docker ps -l -f status=exited -q //查看最近启动异常退出的容器id

docker inspect container—id/name   //查看容器详细信息

列出容器绑定的ip:
# docker inspect --format='{{.NetworkSettings.IPAddress}}'  容器id
列出所有绑定的端口:
# docker inspect --format='{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' 容器id
查看容器的所有机器名
# docker inspect --format 'Hostname:{{ .Config.Hostname }}  Name:{{.Name}} IP:{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -q)

容器改名
docker rename oldname/id newname

docker stats container-name/id     //动态显示容器的资源消耗情况，包括：CPU、内存、网络I/O 
        
docker port container-name/id  //输出容器端口与宿主机端口的映射情况    
80/tcp -> 0.0.0.0:80

```

```
连接容器：    
方法1.attach
# docker attach 容器id   //前提是容器创建时必须指定了交互shell,列如/bin/bash

方法2.exec      
    通过exec命令可以创建两种任务：后台型任务和交互型任务
    交互型任务：
        # docker exec -it  容器id  /bin/bash
        root@68656158eb8e:/# ls     
    
    后台型任务：
        # docker exec 容器id touch /testfile
        
# vim /usr/local/bin/de   //不一定叫de，不冲突就行，这个只有有交互的shell——>/bin/bash才可以用
docker exec -it $1 /bin/bash
# chmod a+x /usr/local/bin/de
# de container-name/id
```

```
监控容器的运行：
可以使用logs、top、events、wait这些子命令
    logs:
        使用logs命令查看守护式容器
        可以通过使用docker logs命令来查看容器的运行日志，其中--tail选项可以指定查看最后几条日志，而-t选项则可以对日志条目附加时间戳。使用-f选项可以跟踪日志的输出，直到手动停止。
        # docker logs name/id   //不同终端操作
        # docker logs -f name/id 

    top:
        显示一个运行的容器里面的进程信息
        # docker top name/id

    events:    
        实时输出Docker服务器端的事件，包括容器的创建，启动，关闭等。

        # docker start loving_meninsky
        loving_meninsky

        # docker events  //不同终端操作
        2017-07-08T16:39:23.177664994+08:00 network connect  
        df15746d60ffaad2d15db0854a696d6e49fdfcedc7cfd8504a8aac51a43de6d4 
        (container=50a0449d7729f94046baf0fe5a1ce2119742261bb3ce8c3c98f35c80458e3e7a, 
        name=bridge, type=bridge)
        2017-07-08T16:39:23.356162529+08:00 container start 
        50a0449d7729f94046baf0fe5a1ce2119742261bb3ce8c3c98f35c80458e3e7a (image=ubuntu, 
        name=loving_meninsky)

    wait:      
        --捕捉容器停止时的退出码
        执行此命令后，该命令会"hang"在当前终端，直到容器停止，此时，会打印出容器的退出码
        # docker wait name/id  //不同终端操作
           137

    diff: 查看容器内发生改变的文件，以elated_lovelace容器为例
         A add新增  C change 发生改变 D delete 文件被删除

        用diff查看：
        包括文件的创建、删除和文件内容的改变都能看到 
        [root@master ~]# docker diff  容器名称
        A /c.txt

        C对应的文件内容的改变，A对应的均是文件或者目录的创建删除
        [root@docker ~]# docker diff 7287
         C /var
	     C /var/cache
	     C /var/cache/nginx
	     A /var/cache/nginx/client_temp
	     A /var/cache/nginx/fastcgi_temp
	     A /var/cache/nginx/proxy_temp		
```

```
宿主机和容器之间相互COPY文件
    cp的用法如下：
    Usage:    docker cp [OPTIONS] CONTAINER:PATH LOCALPATH
                   docker cp [OPTIONS] LOCALPATH CONTAINER:PATH

    如：容器mysql中/usr/local/bin/存在docker-entrypoint.sh文件，可如下方式copy到宿主机
    #  docker cp mysql:/usr/local/bin/docker-entrypoint.sh   /root

    修改完毕后，将该文件重新copy回容器
    # docker cp /root/docker-entrypoint.sh mysql:/usr/local/bin/     
```





# 容器打包

```
容器打包           逐渐被dockerfile的形式所替代
export打包
		Export a container's filesystem as a tar archive

		有两种方式（elated_lovelace为容器名）：
		第一种：
			[root@master ~]# docker export -o elated_lovelace.tar elated_lovelace
		
		第二种：
			[root@master ~]# docker export 容器名称 > 镜像.tar
	import导入
		Import the contents from a tarball to create a filesystem image
		# docker import elated_lovelace.tar  elated_lovelace:v1    
		导入后跟平常创建进入容器有点出入（可能是bug也可能本来就是这样）：
		# docker run -it image-name:tag /bin/bash
```









# 镜像迁移

```
保存一台宿主机上的镜像为tar文件，然后可以导入到其他的宿主机上：
save      
    Save an image(s) to a tar archive
    将镜像打包，与下面的load命令相对应
    #docker save -o nginx.tar nginx:tag

load      
    Load an image from a tar archive or STDIN
    与上面的save命令相对应，将上面sava命令打包的镜像通过load命令导入
    #docker load < nginx.tar
    

国外[速度慢，无法下载，国内没有]----> 买一台香港服务器[下载改镜像，镜像打包]--->拷贝到国内--->导入镜像[导入到私有镜像仓库]
```









# Dockerfile构建镜像

```
ockerfile构建镜像

docker build -t repo:tags   dockerfile-path
docker build -t repo:tags  .


Dockerfile分为四个部分: 基础镜像信息、维护者信息、镜像操作指令和容器启动指令。 即FROM、MAINTAINER、RUN、CMD四个部分

指令说明
FROM         指定所创建镜像的基础镜像
MAINTAINER   制定维护者信息
RUN          运行命令
CMD          容器启动时默认执行的命令
LABEL        指定生成镜像的元数据标签信息
EXPOSE       声明镜像内服务所监听的端口
ENV          指定环境变量
ADD          复制指定src路径的内容到容器的dest路径下，如果src为tar文件，则自动解压到dest路径下
copy         复制指定src路径的内容到镜像的dest路径下
ENTERPOINT   指定镜像的默认入口
VOLUME       创建数据卷挂载点
USER         指定运行容器是的用户名或UID
WORKDIR      配置工作目录
ARG          指定镜像内使用的参数
ONBUILD      配置当所创建的镜像作为其他镜像的基础镜像时，所执行创建操作指令
STOPSIGAL    容器退出信号值
HEALTHCHECK  如何进行健康检查
SHELL        指定使用shell的默认shell类型


1. 制作一个带自己代码的nginx镜像
# mkdir nginx
# cd nginx 
# vim Dockerfile
FROM nginx:latest
MAINTAINER gz2101 gz2101@aliyun.com
LABEL gz2101 nginx

ADD index.html /usr/share/nginx/html

ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:
EXPOSE 80 443

CMD /bin/sh -c 'nginx -g "daemon off;"'
 
# echo 'gz2101 nginx from dockerfile' >index.html

# docker build -t mynginx:v1 .

2. 用自己制作的镜像启动容器
docker run  --name nginx123 -itd mynginx:v1 

3. 查看IP
docker inspect nginx123 | grep IPAddress

4. 访问
curl http://ip
gz2101 nginx from dockerfile

-------------

1. 制作一个带自己代码的tomcat镜像
# mkdir tomcat
# cd tomcat
# vim  Dockerfile
FROM tomcat:8

ADD ROOT.tar /usr/local/tomcat/webapps/                  # RUN mkdir /usr/local/tomcat/ROOT   #ADD index.jsp /usr/local/tomcat/ROOT
ADD gz2101.tar /usr/local/tomcat/webapps/
WORKDIR /usr/local/tomcat
EXPOSE 8080  8443
ENTRYPOINT ["/usr/local/tomcat/bin/catalina.sh","run"]


# mkdir ROOT
# echo "root index.jsp from dockerfile" >ROOT/index.jsp
# tar cf ROOT.tar ROOT 

# docker build -t mytomcat:v1  .


2. 启动 
docker run -itd --name tomcat123 mytomcat:v1

3. 查看IP
docker inspect tomcat123 |grep IPAddress

4. 访问
curl http://ip:8080
curl http://ip:8080/gz2101/index.jsp

推送到自己的私有仓库

怎么减少镜像的大小
1. 减少分层
2. 使用尽量小的基础镜像
3. 及时清理每一层的缓存[在缓存产生层清除，否则清除是无效的]
4. 不添加多余文件

在容器层怎么删除镜像层的文件的？
其实在容器层是无法删除镜像层的文件，但是可以标记改文件已经被删除，从联合文件系统角度看，某个文件被删除了，但是文件标记也是需要占空间的.
```





# docker的web管理界面

```
Docker web UI
	下载并运行容器：
	#docker pull uifd/ui-for-docker 
	#docker run -it -d --name docker-web -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock docker.io/uifd/ui-for-docker 
	
	浏览器访问测试：
		ip:9000
		
Portainer  
	[部署前先关闭 Dokcer web ui 并且删除容器]
	
	创建数据卷挂载点
	docker volume create gz2003

	docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always \
	-v /var/run/docker.sock:/var/run/docker.sock -v gz2003:/data  portainer/portainer

	浏览器访问测试：
		ip:9000


区别:
	Docker web UI仅能管理本机,如果想管理多台需要每台机器都安装一个，而且现在没怎么更新了
	Portainer 可以管理其他机器 [缺点是: 1. 需要agent   2.通信协议swarm现在已经不常用了]
```



# 容器

```
docker的三大核心
	镜像 容器 仓库
=======================
容器的技术底层:namespace、cgroup、chroot

容器是什么？	
容器是经过chroot目录禁锢、namespace隔离以及cgroup资源限制的进程
容器是进程,或者说容器的本质是进程
容器与进程：容器技术的核心功能，就是通过约束和修改进程的动态表现，从而为其创造出一个"边界"。
```

```
验证一:
# docker run -itd --name nginx1 nginx 
92703ad6198bbebbb2f28e96c110c6f9f9c56e2f5d49e929298a7b7a5ff8c366

# docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                                       NAMES
92703ad6198b   nginx                    "/docker-entrypoint.…"   40 seconds ago   Up 39 seconds   80/tcp                                      nginx1

# docker inspect nginx1 |grep Pid
            "Pid": 25114,

# kill -9 25114

# docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED              STATUS                       PORTS                                       NAMES
92703ad6198b   nginx        "/docker-entrypoint.…"   About a minute ago   Exited (137) 6 seconds ago                                               nginx1

验证二:
# docker inspect nginx1 |grep Pid
            "Pid": 25417,
            "PidMode": "",
            "PidsLimit": null,

# pstree -pl			
systemd(1)─┬─NetworkManager(8673)─┬─{NetworkManager}(8712)
            ....
           ├─containerd-shim(25394)─┬─nginx(25417)─┬─nginx(25471)
           │                        │              ├─nginx(25472)
           │                        │              ├─nginx(25473)
           │                        │              └─nginx(25474)
           │                        ├─{containerd-shim}(25397)
           │                        ├─{containerd-shim}(25398)
           │                        ├─{containerd-shim}(25399)
           ...

综上:
容器与进程：容器技术的核心功能，就是通过约束和修改进程的动态表现，从而为其创造出一个"边界"。
```



容器技术的核心

### namespace

```
namespace
	Linux 容器中用来实现"隔离"的技术手段：Namespace。Namespace 技术实际上修改了应用进程看待整个计算机"视图"，即它的"视线"被操作系统做了限制，只能"看到"某些指定的内容。但对于宿主机来说，这些被"隔离"了的进程跟其他进程并没有太大区别。
	
namespace技术，实现了进程间的 名称、pid、网络、挂载、用户等隔离。 ->记到这里差不多了
	
在 Linux 系统中创建线程的系统调用是 clone()，比如：
int pid = clone(main_function, stack_size, SIGCHLD, NULL); 
这个系统调用就会为我们创建一个新的进程，并且返回它的进程号 pid。
	
当用 clone() 系统调用创建一个新进程时，就可以在参数中指定 CLONE_NEWPID 参数，比如：
int pid = clone(main_function, stack_size, CLONE_NEWPID | SIGCHLD, NULL); 
这时，新创建的这个进程将会"看到"一个全新的进程空间，在这空间里，它的 PID 是 1。之所以说"看到"，是因为这只是一个"障眼法"，在宿主机真实的进程空间里，这个进程的 PID 还是真实的数值，比如 100。
```

```
机器ubuntu16.0   go1.13.6

实验1 
UTS Namespace
UTS Namespace主要用来隔离 nodename和domainname两个系统标识.在UTS Namespace里面,每个Namespace允许有自己的hostname

​```
package main

import (
	"os/exec"
	"syscall"
	"os"
	"log"
)



func main() {
	cmd := exec.Command("sh")
	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags: syscall.CLONE_NEWUTS,
		}

	cmd.Stdin = os.Stdin
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	if err := cmd.Run(); err != nil {
		log.Fatal(err)
		}
	}

​```
执行 go run uts_namespace.go命令,在交互式环境,使用pstree -pl查看系统进程间关系:
└─sshd(22330)───bash(22332)───go(22477)─┬─uts_namespace(22531)─┬─sh(22536)──pstree(30131)

然后.输出以下当前PID
echo $$
22536

验证父进程和子进程是否在同一个UTS Namespace中
# readlink /proc/22531/ns/uts
uts:[4026532437]
# readlink /proc/22536/ns/uts
uts:[4026531838]

修改当前hostname然后打印
# hostname -b tiger
# hostname

查看宿主机上运行hostname, 查看效果:
# hostname
docker1

-----------------------
实验二:

IPC Namespace
IPC Namespace用来隔离System V IPC和POSIX message queues. 每一个IPC Namespace都有自己的System V IPC和POSIX message.

package main

import (
	"os/exec"
	"syscall"
	"os"
	"log"
)



func main() {
	cmd := exec.Command("sh")
	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags: syscall.CLONE_NEWUTS | syscall.CLONE_NEWIPC,
		                              }

	cmd.Stdin = os.Stdin
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	if err := cmd.Run(); err != nil {
		log.Fatal(err)
		                        }
	    }
新的代码仅增加 syscall.CLONE_NEWIPC,代表我们希望创建IPC Namespace.下面需要打开两个shell演示

查看宿主机现有的ipc message queues
[root@docker1 code]# ipcs -q

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages    
       

[root@docker1 code]# ipcmk -Q
Message queue id: 32768
[root@docker1 code]# ipcs -q

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages    
0xb0b189d8 32768      root       644        0            0  



[root@docker1 code]# go run ipc_namespace.go 
sh-4.2# ipcs -q

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages    

------------------------------
实验三:
PID Namespace
ID Namespace 是用来隔离进程ID的, 同样的一个进程在不同的PID Namespace里可以拥有不用的PID.

package main

import (
	"os/exec"
	"syscall"
	"os"
	"log"
)



func main() {
	cmd := exec.Command("sh")
	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags: syscall.CLONE_NEWUTS | syscall.CLONE_NEWIPC |syscall.CLONE_NEWPID,
		                              }

	cmd.Stdin = os.Stdin
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	if err := cmd.Run(); err != nil {
		log.Fatal(err)
		                        }
	    }
[root@docker1 code]# pstree -pl
─sshd(6418)─┬─sshd(22244)───bash(22246)───pstree(22692)
           │            └─sshd(22330)───bash(22332)───go(22626)─┬─pid_namespace(22684)─┬─sh(22689)
           │                                                    │                      ├─{pid_namespace}(22685)
           │                                                    │                      ├─{pid_namespace}(22686)
           │                                                    │                      ├─{pid_namespace}(22687)
           │                                                    │                      └─{pid_namespace
           

可以看到go main函数运行的PID为22684,查看另一个shell的PID
[root@docker1 code]# go run pid_namespace.go 
sh-4.2# echo $$
1


--------------------------
实验四：
Mount Namespace
Mount Namespace 用来隔离各个进程看到的挂载点视图. 在不同的Namespace里看到的文件系统层次不一样的.

package main

import (
	"os/exec"
	"syscall"
	"os"
	"log"
)



func main() {
	cmd := exec.Command("sh")
	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags: syscall.CLONE_NEWUTS | syscall.CLONE_NEWIPC |syscall.CLONE_NEWPID |syscall.CLONE_NEWNS,
		                              }

	cmd.Stdin = os.Stdin
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	if err := cmd.Run(); err != nil {
		log.Fatal(err)
		                        }
	    }

运行代码,查看/proc 的文件内容. proc是一个文件系统,提供额外的机制,可以通过内核和内核模块金将信息发送给进程
[root@docker1 code]# go run mount_namespace.go 
sh-4.2# ls /proc
1      20742  26    3081  45	59    6503  980		 fs	     modules	    sysvipc
10     21     28    3082  46	6163  6678  acpi	 interrupts  mounts	    timer_list
11     22     29    3083  47	6166  7     asound	 iomem	     mpt	    timer_stats
110    22231  2974  3084  48	6167  715   buddyinfo	 ioports     mtrr	    tty
12     22244  3     3085  483	6168  718   bus		 irq	     net	    uptime
13     22246  30    31	  495	6170  725   cgroups	 kallsyms    pagetypeinfo   version
14     22330  3022  3154  5	6194  78    cmdline	 kcore	     partitions     vmallocinfo
16     22332  3025  3177  5190	6198  8     consoles	 keys	     sched_debug    vmstat
17     22540  3038  3181  5196	62    8358  cpuinfo	 key-users   schedstat	    zoneinfo
1766   22621  3041  32	  5203	6202  857   crypto	 kmsg	     scsi
18     22694  3060  33	  5211	6244  863   devices	 kpagecount  self
19     22711  3061  34	  5213	6336  9     diskstats	 kpageflags  slabinfo
2      22770  3067  35	  5222	64    939   dma		 loadavg     softirqs
20     22775  3072  36	  5232	6418  941   driver	 locks	     stat
20205  22776  3076  37	  5247	6419  948   execdomains  mdstat      swaps
20330  23     3079  38	  56	6420  954   fb		 meminfo     sys
20576  24     3080  39	  58	6501  963   filesystems  misc	     sysrq-trigger

因为这里的/proc还是宿主机的, 所以看到里面会比较乱. 将/proc mount到我们自己的Namespace:
sh-4.2# mount -t proc proc /proc
sh-4.2# ls /proc
1	   consoles	fb	     kcore	 mdstat   pagetypeinfo	stat	       uptime
4	   cpuinfo	filesystems  keys	 meminfo  partitions	swaps	       version
acpi	   crypto	fs	     key-users	 misc	  sched_debug	sys	       vmallocinfo
asound	   devices	interrupts   kmsg	 modules  schedstat	sysrq-trigger  vmstat
buddyinfo  diskstats	iomem	     kpagecount  mounts   scsi		sysvipc        zoneinfo
bus	   dma		ioports      kpageflags  mpt	  self		timer_list
cgroups    driver	irq	     loadavg	 mtrr	  slabinfo	timer_stats
cmdline    execdomains	kallsyms     locks	 net	  softirqs	tty

可以看到,瞬间少了好多文件,下面就可以使用ps来查看系统的进程了
sh-4.2# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 10:04 pts/1    00:00:00 sh
root          6      1  0 10:11 pts/1    00:00:00 ps -ef

可以看到,在当前Namespace中, sh进程是PID为1的进程. 这就说明, 当前Mount Namespace的mount和外部空间是隔离的,
 mount操作并没有影响到外界. docker volume 就是利用了这个特性


注意，退出进程的时候 卸载 /proc

=================
实验五:
User Namespace
User Namespace主要是隔离用户的用户组ID. 也就是说, 一个进程的User ID和Group ID在User Namespace 内外可以是不同的. 比较常见的是, 在宿主机上以一个非root用户运行创建一个User Namespace,然后在User Namespace里面却映射成root用户.

package main

import (
	"log"
	"os"
	"os/exec"
	"syscall"
)

func main() {
	cmd := exec.Command("sh")
	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags: syscall.CLONE_NEWUTS | syscall.CLONE_NEWIPC | syscall.CLONE_NEWPID |
			syscall.CLONE_NEWNS | syscall.CLONE_NEWUSER,
		UidMappings: []syscall.SysProcIDMap{
			{
				ContainerID: 2333,
				HostID:      0,
				Size:        1,
			},
		},
		GidMappings: []syscall.SysProcIDMap{
			{
				ContainerID: 2233,
				HostID:      0,
				Size:        1,
			},
		},
	}

	cmd.Stdin = os.Stdin
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	if err := cmd.Run(); err != nil {
		log.Fatal(err)
	}
	os.Exit(-1)
}
此处需要升级内核为4.4版本的,本次实验为4.4.209-1,否者3.10版本报错fork/exec /usr/bin/sh: invalid argument

在原来基础上增加syscall.CLONE_NEWUSER.
[root@docker1 code]# id
uid=0(root) gid=0(root) groups=0(root)
[root@docker1 code]# go run user_namespace2.go
sh-4.2$ id
uid=2333 gid=2233 groups=2233

经实验如果内核升级到4.18后可以去掉 新增的部分,也可正常运行

		UidMappings: []syscall.SysProcIDMap{
			{
				ContainerID: 2333,
				HostID:      0,
				Size:        1,
			},
		},
		GidMappings: []syscall.SysProcIDMap{
			{
				ContainerID: 2233,
				HostID:      0,
				Size:        1,
			},
		},
			
用户id为代码中定义ContainerID, UID不同说明UserNamespace生效了

注: 原代码为:

package main

import (
	"os/exec"
	"syscall"
	"os"
	"log"
)

func main() {
	cmd := exec.Command("sh")
	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags: syscall.CLONE_NEWUTS | syscall.CLONE_NEWIPC |syscall.CLONE_NEWPID | syscall.CLONE_NEWUSER,
		                              }

		
	cmd.SysProcAttr.Credential = &syscall.Credential{Uid: uint32(1), Gid: uint32(1)}
	cmd.Stdin = os.Stdin
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	if err := cmd.Run(); err != nil {
		log.Fatal(err)
		                        }
	    }
	    
但是新版本中syscall.CLONE_NEWUSER调用方式有改变, 代码改成以上新代码	    

------------------------------
实验六：
Network Namespace
Network Namespace是用来隔离网络设备.IP地址端口等网络栈的Namespace.

package main

import (
	"log"
	"os"
	"os/exec"
	"syscall"
)

func main() {
	cmd := exec.Command("sh")
	cmd.SysProcAttr = &syscall.SysProcAttr{
		Cloneflags: syscall.CLONE_NEWUTS | syscall.CLONE_NEWIPC | syscall.CLONE_NEWPID |
			syscall.CLONE_NEWNS | syscall.CLONE_NEWUSER |syscall.CLONE_NEWNET,
		UidMappings: []syscall.SysProcIDMap{
			{
				ContainerID: 2333,
				HostID:      0,
				Size:        1,
			},
		},
		GidMappings: []syscall.SysProcIDMap{
			{
				ContainerID: 2233,
				HostID:      0,
				Size:        1,
			},
		},
	}

	cmd.Stdin = os.Stdin
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	if err := cmd.Run(); err != nil {
		log.Fatal(err)
	}
	os.Exit(-1)
}

[root@docker1 code]# ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.3.131.2  netmask 255.255.255.0  broadcast 10.3.131.255
        inet6 fe80::6f94:7887:49d8:9c31  prefixlen 64  scopeid 0x20<link>
        inet6 fe80::1d91:81b9:3c9a:6363  prefixlen 64  scopeid 0x20<link>
        inet6 fe80::ef7:71d:7f83:38ff  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:27:15:75  txqueuelen 1000  (Ethernet)
        RX packets 9666  bytes 732210 (715.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 754  bytes 87009 (84.9 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@docker1 code]# go run network_namespace.go 
sh-4.2$ ifconfig

我们能清晰的看到Namespace里什么网络设备都没有. 这样就能断定Network Namespace与宿主机的网络是处于隔离状态的

======
综上 通过namespace技术，实现了进程间的 名称、pid、网络、挂载、用户等隔离
```



### Cgroup

```
进程间资源限制，刚好能满足容器资源隔离的需求
Linux 中，Cgroups 给用户暴露出来的操作接口是文件系统，即它以文件和目录的方式组织在操作系统的 /sys/fs/cgroup 路径下。
	
	
ls /sys/fs/cgroup/cpu
cgroup.clone_children  cgroup.sane_behavior  cpuacct.usage_percpu  cpu.rt_period_us   cpu.stat           release_agent  user.slice
cgroup.event_control   cpuacct.stat          cpu.cfs_period_us     cpu.rt_runtime_us  docker             system.slice
cgroup.procs           cpuacct.usage         cpu.cfs_quota_us      cpu.shares         notify_on_release  tasks
	
cfs_period 和 cfs_quota 这两个参数需要组合使用，可以用来限制进程在长度为 cfs_period 的一段时间内，只能被分配到总量为 cfs_quota 的 CPU 时间。	
	
下列进程疯狂消耗CPU，进程为:28466
# while : ; do : ; done &
	
   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                     
 28466 root      20   0  115544    608    192 R 100.0  0.0   1:39.23 bash             
想把改进程的cpu资源使用限制在改机器的 20%

# mkdir /sys/fs/cgroup/cpu/gz2101
# cd /sys/fs/cgroup/cpu/gz2101
# cat cpu.cfs_period_us 
100000
# cat cpu.cfs_quota_us 
-1
# echo 20000 > cpu.cfs_quota_us 
限制进程在长度为 cfs_period 的一段时间内，只能被分配到总量为 cfs_quota 的 CPU 时间。
	
资源限制的规则完成，但是未指定限制那个进程[通过tasks文件指定限制的进程]
# echo 28466 > tasks

#查看结果
   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                          
 28466 root      20   0  115544    608    192 R  19.9  0.0   5:01.77 bash   

cpu的使用得到了限制	
```





### chroot

```
在 Linux 操作系统里，有一个名为 chroot 的命令可以帮助你在 shell 中方便地完成这个工作。
顾名思义，它的作用就是帮你"change root file system"，即改变进程的根目录到你指定的位置。

假设，现在有一个 /home/tiger/test 目录，想要把它作为一个 /bin/bash 进程的根目录。

首先，创建一个 test 目录和几个 lib 文件夹：
# mkdir -p /home/tiger/test
# mkdir -p /home/tiger/test/{bin,lib64,lib}

然后，把 bash 命令拷贝到 test 目录对应的 bin 路径下：
# cp -v /bin/{bash,ls}  /home/tiger/test/bin

接下来，把 ls和bash命令需要的所有 so 文件，也拷贝到 test 目录对应的 lib 路径下。找到 so 文件可以用 ldd 命令：

# list="$(ldd /bin/ls | egrep -o '/lib.*\.[0-9]')"
# for i in $list; do cp -v "$i" "/home/tiger/test/${i}"; done

# list="$(ldd /bin/bash | egrep -o '/lib.*\.[0-9]')"
# for i in $list; do cp -v "$i" "/home/tiger/test/${i}"; done

最后，执行 chroot 命令，告诉操作系统，将使用 /home/tiger/test 目录作为 /bin/bash 进程的根目录：
# chroot /home/tiger/test /bin/bash

这时，执行 "/bin/ls /"，就会看到，它返回的都是 /home/tiger/test 目录下面的内容，而不是宿主机的内容。

更重要的是，对于被 chroot 的进程来说，它并不会感受到自己的根目录已经被"修改"成 /home/tiger/test 了。

这种视图被修改的原理，是不是跟之前介绍的 Linux Namespace 很类似呢？
```







### 容器文件系统 Overlayfs

```
 Overlayfs是一种类似aufs的一种堆叠文件系统，于2014年正式合入Linux-3.18主线内核，目前其功能已经基本稳定（虽然还存在一些特性尚未实现）且被逐渐推广，特别在容器技术中更是势头难挡。
它依赖并建立在其它的文件系统之上（例如ext4fs和xfs等等），并不直接参与磁盘空间结构的划分，仅仅将原来底层文件系统中不同的目录进行"合并"，然后向用户呈现。因此对于用户来说，它所见到的overlay文件系统根目录下的内容就来自挂载时所指定的不同目录的"合集"。
```

```
overlayfs最基本的特性，简单的总结为以下3点：
（1）上下层同名目录合并；
（2）上下层同名文件覆盖；(上层覆盖下层)
（3）lower dir文件写时拷贝。
这三点对用户都是不感知的。
overlayfs是aufs实现的一种
```

<img src="C:\Users\蘑菇仔\Desktop\work\小屁孩笔记\image\docker\Snipaste_2021-06-23_00-26-06.png" style="zoom: 50%;" />



```
实验1:	
通过AUFS实现overlayfs
分层镜像
	
AUFS实验
首先在实验目录下创建一个aufs的目录, 然后在aufs目录下创建一个mnt的目录作挂载点. 然后再aufs目录下创建一个名为container-layer的目
录, 再目录里创建文件container-layer.txt,文件内容为"I am container layer". 同样再aufs目录下创建四个名为image-layern的目录(n取值
 1 和 4). 四个目录里分别创建文件image-layer{n}.txt, 文件内容为"I am image layer{n}".

[root@tigerali ~]# cd /tmp/aufs/
[root@tigerali aufs]# ls
container-layer  image-layer1  image-layer2  image-layer3  image-layer4  mnt
[root@tigerali aufs]# cat container-layer/container-layer.txt 
I am container layer
[root@tigerali aufs]# cat image-layer1/image-layer1.txt 
I am imager-layer1
[root@tigerali aufs]# cat image-layer2/image-layer2.txt 
I am image layer2
[root@tigerali aufs]# cat image-layer3/image-layer3.txt 
I am image layer3
[root@tigerali aufs]# cat image-layer4/image-layer4.txt 
I am image layer4

要联合的文件都准备好了。接下来，吧 container-layer 和 4 个image-layer{n} 的目录用 AUFS 的方式挂载到刚刚的 mnt 目录。 
再mount aufs 命令中，没指定待挂载的目录权限，默认是 dirs 指定的左起第一个为 read-write 权限，后续都是 read-only 权限。

# mount -t aufs -o dirs=./container-layer:./image-layer1:./image-layer2:./image-layer3:./image-layer4 none ./mnt

# tree mnt/
mnt/
├── container-layer.txt
├── image-layer1.txt
├── image-layer2.txt
├── image-layer3.txt
└── image-layer4.txt

在系统 aufs 目录下，查看文件读写权限，用下列命令来确认新 mount 的文件系统中每个目录的权限。（注意：si_c59100bf41f6c091 应该是系统为 mnt 挂载点新创建的， 我们通过目录的日期也可判断这一点）

[root@tigerali aufs]# cat /sys/fs/aufs/si_c59100bf41f6c091/*
/tmp/aufs/container-layer=rw
/tmp/aufs/image-layer1=ro
/tmp/aufs/image-layer2=ro
/tmp/aufs/image-layer3=ro
/tmp/aufs/image-layer4=ro
64
65
66
67
68
/tmp/aufs/container-layer/.aufs.xino

我们可以清晰的看到只有 container-layer 是 read-write 的，其余的都是 read-only 的。
接下来作一个有意思的操作， 往 mnt/iamge-layer4.txt 文件末尾添加一行文字， "write to mnt's image-layer4.txt"。根据 Cow技术，我们猜想会产生什么结果

[root@tigerali aufs]# echo -e "\nwrite to mnt's image-layer4.txt" >> ./mnt/image-layer4.txt
查看 mnt/image-layer4.txt 文件的内容，发现内容已经被修改

[root@tigerali aufs]# cat mnt/image-layer4.txt 
I am image layer4

write to mnt's image-layer4.txt
但是此处的 mnt 只是一个虚拟挂载点,因此我们查看修改的到底是哪个文件

发现 image-layer4/image-layer4.txt 没有产生修改
[root@tigerali aufs]# cat image-layer4/image-layer4.txt 
I am image layer4

经检查发现 contianer-layer 目录,发现多了一个名为 image-layer4.txt 的文件,查看文件内容

[root@tigerali aufs]# cat container-layer/
container-layer.txt  image-layer4.txt     .wh..wh.aufs         .wh..wh.orph/        .wh..wh.plnk/        
      
[root@tigerali aufs]# cat container-layer/image-layer4.txt 
I am image layer4

write to mnt's image-layer4.txt
综上所述, 当我们尝试向 mnt/image-layer4.txt 文件进行写操作的时候, 系统首先在 mnt 目录下查找名为 image-layer4.txt 的文件, 将其拷贝到 read-write 层的 container-layer 目录中, 然后对 container-layer 目录中的 image-layer4.txt 进行写操作。 自此我们通过一个小 demo, 实现了自己的 AUFS 文件系统

如果系统中没有 AUFS ,在挂载时失败,安装方式参考
<Centos配置支持AUFS文件系统> 文档
```

