# 一、Git、Github、Gitlab的区别

Git是版本控制系统，Github是在线的基于Git的代码托管服务。 GitHub是2008年由Ruby on Rails编写而成。GitHub同时提供付费账户和免费账户。这两种账户都可以创建公开的代码仓库，只有付费账户可以创建私有的代码仓库。 Gitlab解决了这个问题, 可以在上面创建免费的私人repo。 2018年GitHub被微软收购后，为免费用户也开启了创建私人repo







# 二、安装git

```
yum -y install git
```



初次运行 Git 前的配置

```
配置git
git config --global user.name "username"  #配置git使用用户
git config --global user.email "email@mail.com"  #配置git使用邮箱
git config --global color.ui true  #语法高亮
git config --list # 查看全局配置
```

初次使用git [本地使用]

```
1. 初始化库
mkdir /gz2101
cd /gz2101
git init    # Create an empty Git repository or reinitialize an existing one   初始化一个新的git仓库或者重置一个已经存在的仓库
git status  # Show the working tree status  查看工作区的状态
```



## 常用命令说明

| **命令**     | **命令说明**                                 |
| ------------ | -------------------------------------------- |
| **add**      | 添加文件内容至索引                           |
| **bisect**   | 通过二分查找定位引入 bug 的变更              |
| **branch**   | 列出、创建或删除分支                         |
| **checkout** | 检出一个分支或路径到工作区                   |
| **clone**    | 克隆一个版本库到一个新目录                   |
| **commit**   | 记录变更到版本库                             |
| **diff**     | 显示提交之间、提交和工作区之间等的差异       |
| **fetch**    | 从另外一个版本库下载对象和引用               |
| **grep**     | 输出和模式匹配的行                           |
| **init**     | 创建一个空的                                 |
| **Git**      | 版本库或重新初始化一个已存在的版本库         |
| **log**      | 显示提交日志                                 |
| **merge**    | 合并两个或更多开发历史                       |
| **mv**       | 移动或重命名一个文件、目录或符号链接         |
| **pull**     | 获取并合并另外的版本库或一个本地分支         |
| **push**     | 更新远程引用和相关的对象                     |
| **rebase**   | 本地提交转移至更新后的上游分支中             |
| **reset**    | 重置当前HEAD到指定状态                       |
| **rm**       | 从工作区和索引中删除文件                     |
| **show**     | 显示各种类型的对象                           |
| **status**   | 显示工作区状态                               |
| **tag**      | 创建、列出、删除或校验一个GPG签名的 tag 对象 |



# 三、git操作

## 基础

第一次提交[在每一步后添加命令 git status，查看状态变化]

```
1. touch a.txt
2. git add a.txt  或者 git add .    //前者是指定一个文件，后者是把所有文件都上传暂存区
3. git status
4. git commit -m 'abcd'   //提交到本地仓库
5. git log 
```



第一次取消追踪[在每一步后添加命令 git status，查看状态变化]

```
touch b.txt
git add b.txt
git rm b.txt   //从暂存区删除文件
```



第一次从本地仓库取消提交

```
git commit -m 'dsfa'

git reset --hard commit_id     //从仓储区回滚，根据ip选择回滚到那个版本
```

```
git diff commit-id1 commit_id2 对比两个版本的区别
git diff commit-id1 commit_id2 --name-only  对比两个版本有区别的文件
```

```
git rm filename      从暂存区删除文件filename[取消对文件filename的追踪]
```



git mv(移动或重命名一个文件、目录或符号链接)

```
git mv d.txt f.txt 
```



## 查看历史记录

```
git log   #→查看提交历史记录
git reflog #查看所有历史记录
git log -2   #→查看最近几条记录
git log -p -1  #→-p显示每次提交的内容差异,例如仅查看最近一次差异
git log --stat -2 #→--stat简要显示数据增改行数，这样能够看到提交中修改过的内容，对文件添加或移动的行数，并在最后列出所有增减行的概要信息
git log --pretty=oneline #→--pretty根据不同的格式展示提交的历史信息
git log --pretty=fuller -2 #→以更详细的模式输出提交的历史记录
git log --pretty=fomat:"%h %cn"  #→查看当前所有提交记录的简短SHA-1哈希字串与提交着的姓名。
```



还原某一个版本

```
commit-id  用git log或git reflog查看
git reset --hard commit-id  或git reset --head head^
```



## 标签使用  tag

```
git tag  tagname
git tag      查看标签
git reset --hard tagname  利用标签回滚
```



## 对比数据

```
git diff README
git diff --name-only HEAD HEAD^
git diff --name-only head_id head_id2
```



## 分支结构

```
branch
checkout
merge     

创建分支
git branch branchname

查看分支
git branch [自己当前所在的分支前有*标记]

切换分支
git checkout branchname

查看所有未合并的分支
git branch --no-merged

删除分支
git branch -d branchname
```



在windows上使用git

```
git gui  图形化
git cmd  使用dos命令和git常用命令操作
git bash 使用unix命令和git常用命令
```





# 四、github使用

```
www.github.com

watch   持续关注
star    点赞
fork    创建一个分支到自己的仓库
            
git clone  url             //复制github地址，下载本地仓库
例：git clone git@gitlab.example.com:root/clsn.git

git push 
git remote -v              //查看仓库地址
git pull                   //将远程仓库的代码同步到本地[先要先clone过]
```







# 五、部署gitlab

一键三连先

```
selinux firewalld iptables  //都要关闭
```



2.安装gitlab依赖包

```
[root@localhost ~]# yum install -y curl openssh-server openssh-clients postfix cronie policycoreutils-python
# gitlab-ce 10.x.x以后的版本需要依赖policycoreutils-python
检查是否启动了 nginx、redis、mysql等，如果有都关闭了,一定要关，要不然会冲突
```



3.开启postfix，并设置开机自启 [如果不用邮箱通知可以不开启]

```
[root@localhost ~]# systemctl start postfix;systemctl enable postfix
```



4.下载git，也可以配置源后用yum下载

```
我有包直接拉的
yum localinstall -y gitlab-ce-13.6.2-ce.0.el7.x86_64.rpm
```



5.查看版本号

```
head -1 /opt/gitlab/version-manifest.txt
```



6.Gitlab 配置

```
# 没有域名，可以设置为本机IP地址
vim /etc/gitlab/gitlab.rb
external_url 'http://172.17.0.61'
```



7.初始化 Gitlab (第一次使用配置时间较长)

```
gitlab-ctl reconfigure   //这个过程比较慢
```



8.启动 Gitlab 服务

```
gitlab-ctl start 

其他管理命令
gitlab-ctl start                        # 启动所有 gitlab 组件；
gitlab-ctl stop                         # 停止所有 gitlab 组件；
gitlab-ctl restart                      # 重启所有 gitlab 组件；
gitlab-ctl status                       # 查看服务状态；
gitlab-ctl reconfigure                  # 启动服务；
vim /etc/gitlab/gitlab.rb               # 修改默认的配置文件；
gitlab-ctl tail    
```

这个时候直接输入ip进浏览器创建账号就行了，记得ssh，不然每次pull都要输入密码，顺便创建仓库。





9.在linux上添加用户个人配置

```
剩下的看视频
```

























































猴子都能懂的GIT <https://backlog.com/git-tutorial/cn/>
Pro Git书籍    https://git-scm.com/book/zh/v2