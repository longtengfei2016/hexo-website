---
title: 利用阿里云Centos7建站过程
date: 2017-12-28 17:17:31
tags: 建站
---
以下可能不尽详述,如有问题欢迎指出

## 准备过程:

1.阿里云主机一台

2.域名一个

3.github个人帐号

## 开始:

### 1.以root帐号登录云主机

### 2.安装apache
``` javascript
[root@192 ~]# yum install -y httpd 
```
安装mysql
``` javascript
[root@192 ~]# yum install -y mysql-server mysql-devel
```
    注意:安装过程中可能会有问题,包括缺少各种依赖,根据提示自己yum安装
<!-- more -->
### 3.修改http配置文件并启动http服务

web服务的入口文件是在  /var/www/ 下的index.html文件,我们要做的是将这个改成你自己项目的路径比如/var/www/my-project

打开配置文件
``` javascript
[root@192 ~]# vim /etc/httpd/conf/httpd.conf
```
将所有/var/www/替换成/var/www/my-project,保存退出
启动http服务
``` javascript
[root@192 ~]# systemctl start httpd.service
```
　　
### 4.这一步是要将我们本地window7上的文件放到linux服务器上,这里我们将文件先放到github上,然后服务器从github上拉取更新,本地也可以拉取开发,便于代码管理;

安装 git

``` javascript
[root@192 ~]# wget https://Github.com/Git/Git/archive/v2.3.0.tar.gz
```
解压

``` javascript
[root@192 ~]# tar xvf v2.3.0.tar.gz
```

编译安装
``` javascript
[root@192 ~]# make prefix=/usr/local/git all

[root@192 ~]# make prefix=/usr/local/git install
```
查看是否成功
``` javascript
[root@192 ~]# git --version
```

设置git用户名和邮箱
``` javascript
[root@192 ~]# git config --global user.name 'zhangsan'

[root@192 ~]# git config --global user.email 'zhangsan@163.com'
```

生成公钥
``` javascript
ssh-keygen -t rsa -C "zhangsan@163.com"
```

默认不使用用户名密码 enter键三次即可.

### 5.将公钥添加到github上

(1)Centos7里是没有剪切板的,因此想在服务器全选粘贴的小伙伴自己去装剪切板(反正我试了好久,于是直接第二种方法了)

(2)常规的手段是使用工具Xshell(评估期过后收费)和SecureCRT(推荐)将公钥拷到本地然后添加到github里

CRT连接时会有public key校验,因此在次之前,我们需要修改/etc/ssh/sshd_config的PasswordAuthentication项为yes，重启服务（systemctl restart httpd.service）

CRT连接成功后我们要将密钥拷贝到window7上,公钥放在 /root/.ssh/id_rsa.pub ,CRT的服务器下载路径在Options > session Options > X/Y/Zmodem 里自己设置
``` javascript
[root@192 ~]# sz  /root/.ssh/id_rsa.pub
```

进入github settings > SSH and GPG key 添加完成

### 6.将本地项目上传到github上,并clone到服务器上

这里注意:你web入口文件的路径一定要跟上面修改的 /var/www/my-project 相同,也就是你github的项目就叫my-project,你在/var/www路径下进行clone就行了

### 7.这个时候访问公网ip,就能看到我们自己的主页了@_@

域名解析后,访问域名也是一样的效果,但是访问几次后,如果没有备案就无法再访问了,会提示你去备案