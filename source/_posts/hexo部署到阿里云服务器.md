---
title: hexo部署到阿里云服务器
date: 2017-12-27 11:32:01
tags: hexo
categories: 建站
comments: ture
---
## 一.准备

1.阿里云服务器
2.github账号

<!-- more -->## 二.安装环境

1.gcc 
安装nginx需要先将官网下载的源码进行编译，编译依赖gcc环境，如果没有gcc环境，需要安装gcc：
``` javascript
yum install gcc-c++ 
```
2.pcre 
PCRE(Perl Compatible Regular Expressions)是一个Perl库，包括 perl 兼容的正则表达式库。nginx的http模块使用pcre来解析正则表达式，所以需要在linux上安装pcre库。
``` javascript
yum install -y pcre pcre-devel 
```
3.zlib 
zlib库提供了很多种压缩和解压缩的方式，nginx使用zlib对http包的内容进行gzip，所以需要在linux上安装zlib库。
``` javascript
yum install -y zlib zlib-devel
```
4.openssl 
OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。 
nginx不仅支持http协议，还支持https（即在ssl协议上传输http），所以需要在linux安装openssl库。
``` javascript
yum install -y openssl openssl-devel
```
5.下载Nginx:

Nginx源代码包可以从官方网站下载http://nginx.org/en/download.html，目前最新稳定版本为1.10.1，还有开发版本可供选择。相关命令如下：
``` javascript
wget https://nginx.org/download/nginx-1.10.1.tar.gz
tar zxf nginx-1.10.1.tar.gz
cd nginx-1.10.1/
```
6.安装Nginx：

在安装之前需要进行配置，这也是linux下安装软件的常见步骤。初次安装可以直接使用configure脚本，如果有需要可以设置开关选项开启需要的功能模块，这里就不展开了。相关命令如下：

./configure
make
make install

这里根据网上的一些方法做了配置，但是make编译还是报错，所以直接执行./configure --prefix=/root/nginx-1.10.1 --conf-path=/root/nginx-1.10.1/nginx.conf 再编译成功。
到sbin目录下运行nginx
``` javascript
# cd nginx-1.10.1/sbin
# ./nginx
```
7.安装hexo以及相关插件

``` javascript
# npm install -g hexo
# npm install -g hexo-cli
# npm install hexo-deployer-git --save
# npm install hexo-server --save
```
hexo的用法请参照官网：https://hexo.io/docs/writing.html

## 三.部署到服务器

1.初始化 Git 仓库

可以将git仓库放到自定义位置，我是将其放在 /home/web/blog.git 目录下的
``` javascript
# cd /home/web
# git init --bare blog.git
```
2.配置 git hooks

``` javascript
# cd /home/web/blog.git/hooks
# vim post-receive
```
写入git --work-tree=/home/web/hexo --git-dir=/home/web/blog.git checkout -f 保存退出（/home/web/hexo是从github上clone下来的hexo代码根目录）

设置这个文件的可执行权限
``` javascript
# chmod +x post-receive
```
3.配置 hexo 的 deploy

修改 hexo 目录下的 _config.yml 找到 deploy, 修改为：
``` javascript
deploy:
  type: git
  repo: git@SERVER:/home/web/blog.git
  branch: master
```
## 四.使用

新建文章：
``` javascript
# hexo new "hello world"
```
生成 & 部署：
``` javascript
# hexo clean && hexo g && hexo d
```