# Docker 学习笔记 - Docker 仓库


## 一、概念

仓库（Repository）是集中存放镜像的地方，分公共仓库和私有仓库。一个容易与之混淆的概念是注册服务器（Registry）。实际上注册服务器是存放仓库的具体服务器，一个注册服务器上可以有多个仓库，而每个仓库下面可以有多个镜像。

![docker-repository.jpg](http://www.mayanlong.com/usr/uploads/2017/10/2652242540.jpg)

例如对于仓库地址 hub.docker.com/ubuntu，其中 hub.docker.com 是注册服务器地址，ubuntu 是仓库名。

## 二、仓库

#### 1. Docker Hub 官方仓库

[Dcoker Hub](https://hub.docker.com/) 是 Docker 官网维护的一个公共镜像仓库。

#### 2. 国内镜像市场

国内的镜像市场非常多，比较知名的有下面这些：

[阿里云：https://dev.aliyun.com/search.html](https://dev.aliyun.com/search.html)<br>
[时速云：https://hub.tenxcloud.com](https://hub.tenxcloud.com)<br>
[网易蜂巢：https://c.163.com](https://c.163.com)<br>
[DaoCloud：https://www.daocloud.io](https://www.daocloud.io)

不会翻墙的可以优先考虑使用国内镜像。

#### 3. 搭建本地私有仓库

如果有需要可以选择自己搭建本地私有仓库。