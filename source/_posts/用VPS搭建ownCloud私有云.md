---
title: 用VPS搭建ownCloud私有云
date: 2019-08-19 00:34:55
tags:
---
## ownCloud 安装 <sub>踩坑</sub>

**本来打算直接按照官方教程安装的，但PHP环境配置一直出问题,最后还是用docker了(省心**
系统： CentOS7
VPS：hostwinds
<!--more-->
### 安装docker
* 检查内核版本，（>= 3.8)
```
# uname -r
3.10.0-957.27.2.el7.x86_64
```
* 删除自带的docker,如果有的话
```
# yum erase docker -y
```
* 安装一些必要的系统工具：

```
# yum install -y yum-utils device-mapper-persistent-data lvm2
```
* 添加软件源信息：
```
# yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
* 更新 yum 缓存：
```
# yum makecache fast
```
* 安装 Docker-ce：
```
# yum -y install docker-ce
```
* 启动 Docker 后台服务
```
# systemctl start docker
```
### docker 安装 owncloud 
* 拉取owncloud官方镜像
```
# sudo docker pull owncloud
```
* owncloud默认使用SOLite,但是对于更大的安装，官方推荐使用另外的数据库(mariadb/mysql等)
```
# docker pull mysql
```
* 启动mysql容器，用作owncloud容器的数据库
```
# docker run --name own-mysql -e MYSQL_ROOT_PASSWORD="password" -d mysql
```
* 启动owncloud容器
```
# docker run --name owncloud -p 8008:80   -v /data/db/owncloud:/var/www/html/data --link own-mysql:mysql -d owncloud
```
**-p 8008:80**: 将容器的80端口映射到宿主机的8008端口
**--link own-mysql:mysql -d owncloud**: 将owncloud容器(客户)链接到own-mysql容器(服务),链接别名：mysql

### 配置owncloud
在浏览器上访问 http:IP:8008,设置owncloud,这里遇到了问题
The server requested authentication method unknown to the client
google了一下是由于新版本的mysql帐号密码解锁机制不一致导致的
* 解决办法：
1. 进入mysql的bash中
```
# docker exec -it own-mysql bash
# docker pull vim
# vim /etc/mysql/my.cnf
```
2. 修改这个文件：
```
[mysqld]
default_authentication_plugin=mysql_native_password
```
3. 配置mysql：
```
# mysql -u root -p
Entry Password: [YOUR PASSWORD]
# CREATE USER 'root'@'localhost' IDENTIFIED BY 'password';
# GRANT ALL PRIVILEGES ON * . * TO 'root'@'localhost';
# FLUSH PRIVILEGES;
```
4. 最后重启mysql
```
# docker restart own-mysql
```
# END
