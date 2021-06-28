# kubeadm方式部署Kubernetes

192.168.200.10 vm1  master
192.168.200.11 vm2  node1
192.168.200.12 vm3  node2

### 一、准备工作

```
//所有节点安装

关闭防火墙：
# systemctl stop firewalld
# systemctl disable firewalld

关闭selinux：
# sed -i 's/enforcing/disabled/' /etc/selinux/config 
# setenforce 0

关闭swap：
# swapoff -a  # 临时
# vim /etc/fstab  # 永久  //在swap这行前面加个#就行了，wq退出

同步时间
# yum -y install ntp ntpdate
# ntpdate ntp1.aliyun.com

添加主机名与IP对应关系（记得设置主机名）：
# cat /etc/hosts
192.168.200.10 vm1
192.168.200.11 vm2
192.168.200.12 vm3

将桥接的IPv4流量传递到iptables的链：
# cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
# sysctl --system
```



### 二、所有节点安装Docker/kubeadm/kubelet

```
//所有节点安装
Kubernetes默认CRI（容器运行时）为Docker，因此先安装Docker。

1 安装Docker
# wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
# yum -y install docker-ce
# systemctl enable docker && systemctl start docker
# docker --version
Docker version 18.06.1-ce, build e68fc7a

2 添加阿里云YUM软件源
# cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

3 安装kubeadm，kubelet和kubectl  //kubeadm，kubelet三台都需要安装，kubectl只有master需要，但你想装也可以。
由于版本更新频繁，这里指定版本号部署：
# yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0  [kubectl只需要master安装]
# systemctl enable kubelet           [注意该步骤不能start启动]
```



### 三、部署Kubernetes Master

```
//只在192.168.200.10 Master执行。

# kubeadm init \
  --apiserver-advertise-address=192.168.200.10 \
  --image-repository registry.aliyuncs.com/google_containers \
  --kubernetes-version v1.18.0 \
  --service-cidr=10.1.0.0/16 \
  --pod-network-cidr=10.244.0.0/16

[kubeadm init后记得保存kubeadm join，在后面有用]
kubeadm join 192.168.200.10:6443 --token cw77d8.3rxfxuipx583n11p \
    --discovery-token-ca-cert-hash sha256:c07e26368e85b2a4ca980e96a68859e6f4a404df80454c1073724e7d7735f014 

# mkdir -p $HOME/.kube
# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
# sudo chown $(id -u):$(id -g) $HOME/.kube/config
# kubectl get nodes
```



### 四、安装Pod网络插件（CNI)

```
//在master执行
# wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml 
# kubectl apply -f kube-flannel.yml 
# kubectl get pods -n kube-system                [确保下列pod的状态都是running的]

NAME                             READY   STATUS    RESTARTS   AGE
coredns-7ff77c879f-wj2qb         1/1     Running   0          12m
coredns-7ff77c879f-zdbgk         1/1     Running   0          12m
etcd-master                      1/1     Running   0          12m
kube-apiserver-master            1/1     Running   0          12m
kube-controller-manager-master   1/1     Running   0          12m
kube-flannel-ds-2xvpd            1/1     Running   0          69s
kube-proxy-qq229                 1/1     Running   0          12m
kube-scheduler-master            1/1     Running   0          12m
```



### 五、 加入Kubernetes Node

```
//在所有node节点执行

向集群添加新节点，执行在kubeadm init输出的kubeadm join命令：

#  kubeadm join 10.3.133.211:6443 --token n78cb1.ynqouqnomu2ndlay \
    --discovery-token-ca-cert-hash sha256:6a0e3dc3d4ac8f6e5d7766230918ab9af40b7f99d70c3ec549630b9652cac846 
```

然后在master节点执行：``kubectl get nodes``       //一定要在master，node是无法显示的



### 六. 测试kubernetes集群

```
//在master节点执行
在Kubernetes集群中创建一个pod，验证是否正常运行：

# kubectl create deployment nginx --image=nginx
# kubectl expose deployment nginx --port=80 --type=NodePort
# kubectl get pod,svc
# kubectl get pod,svc -o wide

访问地址：http://NodeIP:Port  //访问成功就部署完成
```



### 七、 部署kuboard  

```
//在master节点执行
1. 部署
# kubectl apply -f https://kuboard.cn/install-script/kuboard.yaml
# kubectl apply -f https://addons.kuboard.cn/metrics-server/0.3.7/metrics-server.yaml

-----
[如果metric安装后报如下错误]
Error from server (ServiceUnavailable): the server is currently unable to handle the request (get pods.metrics.k8s.io)
则执行下列命令解决
kubectl create clusterrolebinding system:anonymous  --clusterrole=cluster-admin  --user=system:anonymous
------

查看运行状态
kubectl get pods -l k8s.kuboard.cn/name=kuboard -n kube-system

输出结果如下所示：
NAME                       READY   STATUS        RESTARTS   AGE
kuboard-54c9c4f6cb-6lf88   1/1     Running       0          45s

2.获取访问token
-拥有的权限
此Token拥有 ClusterAdmin 的权限，可以执行所有操作
# echo $(kubectl -n kube-system get secret $(kubectl -n kube-system get secret | grep kuboard-user | awk '{print $1}') -o go-template='{{.data.token}}' | base64 -d)

-执行如下命令可以获得 只读用户 的 Token
# echo $(kubectl -n kube-system get secret $(kubectl -n kube-system get secret | grep kuboard-viewer | awk '{print $1}') -o go-template='{{.data.token}}' | base64 -d)

view 可查看名称空间的内容
system:node 可查看节点信息
system:persistent-volume-provisioner 可查看存储类和存储卷声明的信息
适用场景

只读用户不能对集群的配置执行修改操作，非常适用于将开发环境中的 Kuboard 只读权限分发给开发者，以便开发者可以便捷地诊断问题

3.访问Kuboard
Kuboard Service 使用了 NodePort 的方式暴露服务，NodePort 为 32567；您可以按如下方式访问 Kuboard

http://任意一个Worker节点的IP地址:32567/

输入前一步骤中获得的 token，可进入 Kuboard 集群概览页
```

