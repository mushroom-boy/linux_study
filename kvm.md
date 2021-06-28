# KVM安装

先把虚拟化打开

![](C:\Users\蘑菇仔\Desktop\work\小屁孩笔记\image\kvm\kvm.png)

查看CPU是否支持VT技术: 

  ```
  #cat /proc/cpuinfo |grep -E 'vmx|svm'  
  ```

需求内核(需要2.0以上):   

```
# uname  -r
2.6.32-358.el6.x86_64 
```

安装软件:

```
yum install qemu-kvm libvirt virt-manager librbd1-devel -y 
或
yum install *qemu*  *virt*  librbd1-devel -y  （这两个命令二选一就行）
```

启动服务:    

    systemctl start libvirtd    
查看kvm模块加载:

```
# lsmod | grep kvm
kvm_intel              53484  3 
kvm                   316506  1 kvm_intel
```









# KVM guest-os部署安装

### 命令行安装（或文本方式安装）

命令行安装=文本方式=完全文本方式=纯文本方式，都是一样的，只是叫法略有差别

完全文本方式安装:  内存不能给小，最起码2G，给小会报错

缺点：纯文本安装的输入时大小写莫名的变换，远程ssh没问题
           内存必须大于2048

```
virt-install --connect qemu:///system -n vm1 -r 2048 --disk path=/var/lib/libvirt/images/vm1.img,size=7  --os-type=linux --os-variant=centos7.0 --vcpus=1  --location=ftp://192.168.200.16/centos7 -x console=ttyS0 --nographics
```

如果出现这个：-bash: virt-install: command not found，执行以下命令

```
yum -y install virt-install
```



-n guestos name            虚拟机名字
-r num                     mem 内存大小
--disk path=路径,size=num  指定磁盘位置和名称以及磁盘大小
--os-type linux            系统类型
--os-variant=centos7.0     版本
--vcpus num                cpu核心数
--location                 指定镜像位置

文本安装进入后配置参考：https://zhuanlan.zhihu.com/p/133125524





### 模板镜像+配置文件 方式创建虚拟机

注意：第一台虚拟机不能用cp搞，你连第一台都没有怎么cp

```
配置文件+镜像文件[不是.iso镜像 理解为虚拟机磁盘]

1. 拷贝模板镜像和配置文件
[root@kvm ~]# cp /var/lib/libvirt/images/vm2.img /var/lib/libvirt/images/gz.img
[root@kvm ~]# cp /etc/libvirt/qemu/vm2.xml /etc/libvirt/qemu/gz.xml	 
	 
2. 修改配置文件
name uuid mem cpu disk net-interface 

vim /etc/libvirt/qemu/gz.xml	 
  <name>gz</name>                                              # 虚拟机名称[必改]
  <uuid>6d234bff-c219-4db0-98e2-7fc182d6c16b</uuid>            # uuid[必改]
  <memory unit='KiB'>2099200</memory>                          # 内存
  <currentMemory unit='KiB'>2099200</currentMemory>            # 内存
  <vcpu placement='static'>1</vcpu>                            # cpu
	...
  <devices>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/gz.img'/>          # 镜像文件路径[必改]
      <target dev='vda' bus='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x06' function='0x0'/>
    </disk>
     ...
    <interface type='network'>
      <mac address='52:54:00:0b:ee:2a'/>                       # mac地址[必须]  
      <source network='default'/>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
    </interface>


mac地址的修改可以使用下列命令来生成后三位 openssl rand -hex 3 | sed -r 's/..\B/&:/g'

3. 向kvm系统定义改虚拟机
virsh define /etc/libvirt/qemu/gz.xml
```







### ftp

没搞ftp看这里

```
yum -y install vsftpd
mkdir /var/ftp/centos7
cd /vat/ftp/centos7

winscp --> D:\iso\linux iso\千峰\CentOS-7-x86_64-DVD-1810.iso  --> /vat/ftp/centos7   (rz不能传大文件,所以用winscp，这个路径的镜像可以搞出完整的Gueset os，其他的镜像不是有问题，是内存给小了，最起码2G)

mount /dev/sr0 /vat/ftp/centos7   //挂载
-> umount /vat/ftp/centos7     //卸载(有问题才用)

systemctl restart vsftpd
```

有时候卸载不了,用lsof /dev/sr0 查看pid,然后kill pid

```
# lsof /var/ftp/centos7/
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sftp-serv 8001 root    7r   DIR   11,0     2048 1856 /var/ftp/centos7
# kill 8001
```









# KVM 命令

查看所有的机器

```
virsh list --all
```



查看启动的机器

```
virsh list
```



查看启动机器的IP

```
1. 方式一
# virsh domifaddr gz2102

2. 方式二  //方法一有时候会失效，这时就用方法二
# virsh dumpxml gz2102 |grep 'mac address' |awk -F"'" '{print  $2}'
52:54:00:77:54:ca

# arp -a |grep '52:54:00:77:54:ca'
? (192.168.122.8) at 52:54:00:77:54:ca [ether] on virbr0
```



登陆机器

```
virsh console gz
也可以直接用ssh
```



启动机器

```
# virsh start gz
```



停止机器

```
方法一
# virsh shutdown gz 
方法二
# virsh destroy gz     # 有一定的风险会损坏虚拟机  一般在虚拟机无法关机，需要强制关机使用
```

```
！！！kvm的暂停类似于vmware的挂起，但是不同点在于如果kvm的host os关机或者重启了，那么挂起就失效了
暂停虚拟机： 
  #virsh suspend vm_name  

恢复虚拟机：
  #virsh resume vm_name 
```

```
重置
# virsh reset gz      #作用不太明确 [效果时好时坏]
```



删除虚拟机

```
# virsh undefine gz   # 该删除并未完全删除，仅删除了定义和配置文件，镜像文件未删除[需要手动删除]
注意:虚拟机在开启的情况下undefine是无法删除的，但是如果再destroy会直接被删除掉
```



虚拟机开机自动启动

```
# virsh autostart gz
Domain gz marked as autostarted
# ls /etc/libvirt/qemu/autostart/     //此目录默认不存在，在有开机启动的虚拟机时自动创建
gz.xml
# ll /etc/libvirt/qemu/autostart/
lrwxrwxrwx 1 root root 24 Jun 15 16:46 gz.xml -> /etc/libvirt/qemu/gz.xml

# virsh autostart --disable vm1
  域 vm1取消标记为自动开始
# ls /etc/libvirt/qemu/autostart/
```







# kvm虚拟机添加设备

给虚拟机添加新硬件

```
修改配置文件方式:
# virsh edit vm2
Mem
  <memory unit='KiB'>2099200</memory>
  <currentMemory unit='KiB'>2099200</currentMemory>
cpu
  <vcpu placement='static'>1</vcpu>
disk
  创建空的磁盘文件：
  #qemu-img create -f qcow2 /var/lib/libvirt/images/vm2_1.qcow2 5G
  需要修改的地方 file磁盘路径、slot插槽修改且全局唯一、dev磁盘名字需要改
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/vm11.qcow2'/>
      <target dev='vda' bus='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x06' function='0x0'/>
    </disk>

  
net-interface
   需要修改的地方 mac地址、slot插槽修改且全局唯一
    <interface type='network'>
      <mac address='52:54:00:0b:ee:2a'/>
      <source network='default'/>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
    </interface>

```

需要注意的点
1. 关机后修改
2. 使用virsh edit vm_name修改配置





# 虚拟机克隆

```
1.图形界面：Applications （左上角）-----> System Tools ------>Virtual Machine Manager
   关闭要克隆的虚拟机，右键点击虚拟机选择Clone

2.字符终端，命令克隆
    # virt-clone -o vm1 --auto-clone
        WARNING  设置图形设备端口为自动端口，以避免相互冲突。
        正在分配 'vm1-clone.qcow2'            | 6.0 GB  00:00:05     
        成功克隆 'vm1-clone'。
         -o       origin,指定克隆的机器    
    
    # virt-clone -o vm1 -n vm2 --auto-clone      
        WARNING  设置图形设备端口为自动端口，以避免相互冲突。
        正在分配 'vm2.qcow2'                                                | 6.0 GB  00:00:06     
        成功克隆 'vm2'。
          -n  命名
        
    # virt-clone -o vm1 -n vm2 -f /var/lib/libvirt/images/vm2.img
        正在克隆        
        vm1.img              | 8.0 GB     01:03     
        Clone 'vm2' created successfully.
        -f 指定镜像路径
```







# 建立虚拟机磁盘镜像文件

磁盘镜像文件格式:
    raw   原始格式，性能最好 直接占用你一开始给多少 系统就占多少 不支持快照
    qcow  先去网上了解一下cow(写时拷贝copy on write) ，性能远不能和raw相比，所以很快夭折了，所以出现了qcow2   （性能低下 早就被抛弃）
    qcow2 性能上还是不如raw，但是raw不支持快照，qcow2支持快照。

现在默认安装好的用的是qcow2格式，所有做快照要把他转换成qcow2格式

什么叫写时拷贝？
raw立刻分配空间，不管你有没有用到那么多空间
qcow2只是承诺给你分配空间，但是只有当你需要用空间的时候，才会给你空间。最多只给你承诺空间的大小，避免空间浪费

工作当中用哪个？看你用不用快照。
工作当中虚拟机会有多个备份，一个坏了，再起一个就行了，所有没必要用快照。当然也不一定。
数据绝对不会存储到本地。

qemu-kvm  qemu是早先的一个模拟器，kvm是基于qemu发展出来的。

建立qcow2格式磁盘文件:
#qemu-img create -f qcow2 test.qcow2 20G 

建立raw格式磁盘文件:
#qemu-img create -f raw test.raw 20G  

查看已经创建的虚拟磁盘文件:
#qemu-img info test.qcow2 

磁盘转换
qemu-img  convert -f 转换前的类型 -O 转换后类型  转换前的磁盘名称  转换后的磁盘名称

qemu-img convert -f raw -O qcow2 test1.raw test1.qcow2
qemu-img convert -f qcow2 -O raw test1.qcow2 test2.raw





# 虚拟机修复命令

### 1.使用guestfish工具

```
作为虚拟化环境管理员，你肯定遇到过虚拟机无法启动的情况。实施排错时，你需要对虚拟机的内部进行检查。而guestfish工具集可以在这种情况下为你提供帮助。
 
#yum -y install libguestfs-tools

使用guestfish操作虚拟机磁盘镜像文件：
完成虚拟机磁盘镜像文件的复制之后，可以在libguestfs中使用guestfish这样的工具将其打开，这样就可以直接在vmdk文件上进行操作了。


使用命令来在虚拟机中创建一个连接到文件系统的交互式shell。在新出现的窗口中，你可以使用特定的命令来操作虚拟机文件。
#guestfish --rw -a  /path/to/windows.vmdk

第一个任务就是找到可用的文件系统：
1. ><fs> run                   //进入交互式shell之后第一个命令
2. ><fs> list-filesystems  //列出磁盘镜像文件内的文件系统
/dev/vda1: ext4
/dev/vdb1: iso9660
/dev/VolGroup/lv_root: ext4
/dev/VolGroup/lv_swap: swap
3.><fs>  mount /dev/centos/root   /     //当你使用guestfish shell找到可用文件系统类型之后，就可以进行挂载了。将文件系统到guestfish根目录下
4.><fs>  ls /                                               //ls命令查看文件/下内容 ，不能使用cd命令
bin  boot dev etc home  lib lib64  lost+found
5.><fs> cat /etc/passwd      //查看文件，不能像在其他shell环境中一样操作。目录所有路径必须从根开始
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin

在guestfish  shell当中可以使用像ls、cat、more、download这样的命令，来查看和下载文件以及目录

guestfish  读镜像
mount  /dev/sda1  /
cd /



查看帮助：这两个帮助显示的内容不一样
# guestfish --help 
# guestfish -h

```





### 2.使用Libguestfs工具

```
-bash: virt-rescue: command not found  //出现这种状况直接执行下面的句子
# yum -y install libguestfs-tools 

首先把需要修复的机器停止
virsh shutdown vm3

Virt-rescue提供直接访问方式：
这种方式跟linux系统光盘的rescue模式几乎一样，进去之后首先需要查看文件系统，然后手动挂载到/sysroot目录下，进入/sysroot目录就可以随意操作虚拟磁盘镜像内的文件了
# virt-rescue vm1         //进入修复模式，help查看帮助
><rescue>fdisk -l
><rescue>mount /dev/mapper/centos-root /sysroot/ 
><rescue>cd /sysroot/
><rescue>ls
><rescue>touch aaaaaaaaaaaaa

============================
查看磁盘镜像分区信息:
    # virt-df -h -d vm3
Filesystem                                Size       Used  Available  Use%
vm3:/dev/sda1                            1014M       112M       902M   12%
vm3:/dev/centos/root                      8.0G       970M       7.0G   12%


    # virt-filesystems -d vm3
    /dev/sda1
    /dev/sdb1
    /dev/VolGroup/lv_root

挂载磁盘镜像分区:
    # guestmount -d vm3 -m /dev/centos/root  --rw /mnt/
    # ls
    bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin 
    
    #cd home/
    # ls
    dj.txt
    
    # vim dj.txt 
     ni hao
    我是你爹
    hello world
   
弄完后记得卸载：
    # umount /mnt
```









# kvm快照

```
仅限qcow2格式的机器

# virsh snapshot-create-as rhel5u8-1 rhel5u8-1.snap
error: unsupported configuration: internal snapshot for disk vda unsupported for storage type raw   //这种报错是因为磁盘格式为raw，需要进行格式转换为qcow2

创建快照
# virsh snapshot-create-as  gz  gz.snap1
Domain snapshot gz.snap1 created

查看快照
virsh snapshot-list gz
	Name                 Creation Time             State
	-----------------------------------------------------------
	gz.snap1             2021-06-16 16:21:13 +0800 shutoff
	gz.snap2             2021-06-16 16:22:30 +0800 running


恢复快照
[恢复快照同时会回到快照创建是 的机器状态]
# virsh snapshot-revert  gz gz.snap1


删除快照
virsh snapshot-delete --snapshotname  gz.snap2 gz
Domain snapshot gz.snap2 deleted
```





# guest-os配置桥接和nat

```
配置文件方式配置桥接：在宿主机上
    1.修改配置文件 [新添加的网卡]
    # cat ifcfg-br0 
    TYPE=Bridge
    NAME=br0
    DEVICE=br0
    ONBOOT="yes"
    BOOTPROTO=static
    IPADDR=10.18.44.251
    GATEWAY=10.18.44.1
    NETMASK=255.255.255.0
    DNS1=10.18.44.100
    DNS2=8.8.8.8

    # cat ifcfg-ens33 [原本的通信网卡]
        TYPE="Ethernet"
        NAME="ens33"
        DEVICE="ens33"
        ONBOOT="yes"
        BRIDGE=br0
    
    2.重启libvirtd服务
    3.重启network服务 
    
   使用br网桥
      <interface type='bridge'>                     //修改网络类型
      <mac address='52:54:00:c4:43:75'/>
      <source bridge='br0'/>                         //修改source网络
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
    </interface>
   
   
    删除桥接网卡步骤：
    1.删除br0的配置文件
    2.修改正常网卡的配置文件
    3.重启系统    


----------------
配置文件方式创建nat网络：
# cp /etc/libvirt/qemu/networks/nat2.xml /etc/libvirt/qemu/networks/nat3.xml
# vim /etc/libvirt/qemu/networks/nat3.xml
<network>
  <name>nat3</name>
  <uuid>4d8b9b5c-748f-4e16-a509-848202b9c83b</uuid>
  <forward mode='nat'/>             //和隔离模式的区别
  <bridge name='virbr4' stp='on' delay='0'/>
  <mac address='52:57:00:62:0c:d4'/>
  <domain name='nat3'/>
  <ip address='192.168.104.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.104.128' end='192.168.104.254'/>
    </dhcp>
  </ip>
</network>

重启服务：
# systemctl  restart libvirtd


================================
存储问题
默认存储池：
    /var/lib/libvirt/images/    
```







自定义脚本

```
自定义脚本

#!/bin/bash
#kvm batch create vm tool
#version:0.1
#author:name
#需要事先准备模板镜像和配置文件模板
echo "1.创建自定义配置单个虚拟机
2.批量创建自定义配置虚拟机
3.批量创建默认配置虚拟机
4.删除虚拟机"

read -p "选取你的操作(1/2/3):" op

batch_self_define() {

        kvmname=`openssl rand -hex 5`

        sourceimage=/var/lib/libvirt/images/vmmodel.img
        sourcexml=/etc/libvirt/qemu/vmmodel.xml

        newimg=/var/lib/libvirt/images/${kvmname}.img
        newxml=/etc/libvirt/qemu/${kvmname}.xml

        cp $sourceimage  $newimg
        cp $sourcexml $newxml

        kvmuuid=`uuidgen`
        kvmmem=${1}000000
        kvmcpu=$2
        kvmimg=$newimg
        kvmmac=`openssl rand -hex 3 | sed -r 's/..\B/&:/g'`

        sed -i "s@kvmname@$kvmname@;s@kvmuuid@$kvmuuid@;s@kvmmem@$kvmmem@;s@kvmcpu@$kvmcpu@;s@kvmimg@$kvmimg@;s@kvmmac@$kvmmac@" $newxml
        virsh define $newxml
        virsh list --all
}
self_define() {
        read -p "请输入新虚机名称:" newname
        read -p "请输入新虚机内存大小(G):" newmem
        read -p "请输入新虚机cpu个数:" newcpu

        sourceimage=/var/lib/libvirt/images/vmmodel.img
        sourcexml=/etc/libvirt/qemu/vmmodel.xml

        newimg=/var/lib/libvirt/images/${newname}.img
        newxml=/etc/libvirt/qemu/${newname}.xml

        cp $sourceimage  $newimg
        cp $sourcexml $newxml

        kvmname=$newname
        kvmuuid=`uuidgen`
        kvmmem=${newmem}000000
        kvmcpu=$newcpu
        kvmimg=$newimg
        kvmmac=`openssl rand -hex 3 | sed -r 's/..\B/&:/g'`

        sed -i "s@kvmname@$kvmname@;s@kvmuuid@$kvmuuid@;s@kvmmem@$kvmmem@;s@kvmcpu@$kvmcpu@;s@kvmimg@$kvmimg@;s@kvmmac@$kvmmac@" $newxml
        virsh define $newxml
        virsh list --all
}

case $op in
1)self_define;;
2)
        read -p "请输入要创建的虚拟机的个数:" num
        read -p "请输入新虚机内存大小(G):" newmem
        read -p "请输入新虚机cpu个数:" newcpu

        for((i=1;i<=$num;i++))
        do
                batch_self_define $newmem $newcpu
        done;;

3)
        read -p "请输入要创建的虚拟机的个数:" num

        for((i=1;i<=$num;i++))
        do
                batch_self_define 1 1
        done;;

*)echo "输入错误，请重新执行脚本"
  exit;;
esac
```

