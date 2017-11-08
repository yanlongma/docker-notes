# Docker 学习笔记 - 端口映射与容器互联


## 一、前言

在实际应用中，经常会碰到需要多个服务组件容器共同协作的情况，这往往需要多个容器之间有能够互相访问到对方的服务。

除了通过网络访问外，Docker 还提供了两个很方便的功能来满足服务访问的基本需求：
1. 端口映射：允许映射容器内应用的服务端口到本地宿主主机；
2. 容器互联：提供了互联机制实现多个容器间通过容器名来快速访问。


## 二、端口映射

Docker 端口映射即映射容器内应用的服务端口到本机宿主机器。

当容器中运行一些网络应用，要让外部访问这些应用时，可以通过 -P 或 -p 参数两种方式来指定端口映射。

#### 1. 随机映射

使用 -P 参数时，Docker 会随机映射一个端口到内部容器开放的网络端口，如下开启一个 nginx 服务：
```
$ docker run -d -P nginx
e93349d539119dc48dc841e117f6388d6afa6a6065b75a5b4aedaf5fb2a051fc
$ 
$ docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                   NAMES
e93349d53911        nginx               "nginx -g 'daemon ..."   11 seconds ago      Up 9 seconds        0.0.0.0:32769->80/tcp   zen_kirch
```

使用 docker ps 看到，本地主机的 32769 端口被映射到了容器的 80 端口，这时我们通过本机浏览器访问 `http://localhost:32769` 就会出现 nginx 欢迎页面。

#### 2. 指定端口

使用 -p 参数时，可以指定要映射的端口，并且在一个指定的端口上只可以绑定一个容器。支持的格式有：
- IP:HostPort:ContainerPort
- IP:ContainerPort
- HostPort:ContainerPort

下面开启一个 nginx 服务，将本机 8080 端口映射到容器的 80 端口：
```
$ docker run -d -p 8080:80 nginx
23e725098712d061a1382f33d6fe54da23ae37597a62f8debdd3731b5f9cc4b9
$ 
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                   NAMES
23e725098712        nginx               "nginx -g 'daemon ..."   8 seconds ago       Up 6 seconds        0.0.0.0:8080->80/tcp    frosty_ptolemy
```

使用 docker ps 看到，本地主机的 8080 端口被映射到了容器的 80 端口，这时我们通过本机浏览器访问 `http://localhost:8080` 就会出现 nginx 欢迎页面。

#### 3. 查看映射端口

使用 docker port 命令来查看当前映射的端口配置，也可以查看到绑定的地址。命令格式如下：
```
$ docker port CONTAINER [PRIVATE_PORT[/PROTO]]
```

容器有自己的内部网络和 IP 地址，可以使用 docker inspect + 容器ID 获取容器的具体信息。


## 三、容器互联

容器的互联（linking）是一种让多个容器中应用进行快速交互的方式。它会在源和接收容器之间创建连接关系，接收容器可以通过容器名快速访问到源容器，而不是指定具体的 IP 地址。

下面使用 mysql 和 wordpress 镜像来搭建 wordpress 运行环境，演示容器互联的使用。

#### 1. 自定义容器名

连接系统依据容器的名称来执行。虽然创建容器时系统会默认分配一个名字，但建议自己自定义命名。

首先启动一个 mysql 容器，使用 --name 标记自定义容器名为 db：
```
$ docker run --name db -d -e MYSQL_ROOT_PASSWORD=123456 mysql:latest
35bd73d4d41370bb5218818f58b7f1b89744728c4cb6317d85634abdb47da461
$ 
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
35bd73d4d413        mysql:latest        "docker-entrypoint..."   11 seconds ago      Up 10 seconds       3306/tcp            db
```
使用 docker ps 可以看到该容器的 NAMES 为 db。

注：`-e MYSQL_ROOT_PASSWORD=123456` 是为 mysql 服务设置密码，具体可以查看 mysql 镜像的使用。

#### 2. 容器互联

使用 --link 参数可以让容器之间安全地交互。--link 的参数格式为 --link name:alias，其中 name 是要连接的容器名称，alias 是这个连接的别名。

下面启动一个 wordpress 容器，并将它连接到 db 容器：
```
$ docker run --name wp --link db:mysql -d -p 8080:80 wordpress:latest
d9ee6660b48f80328d14e7a2a57013e72fa8d88de8524d651e003940563e3090
$ docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
d9ee6660b48f        wordpress:latest    "docker-entrypoint..."   5 seconds ago       Up 4 seconds        0.0.0.0:8080->80/tcp   wp
35bd73d4d413        mysql:latest        "docker-entrypoint..."   18 minutes ago      Up 18 minutes       3306/tcp               db
```
此时，wp 容器和 db 容器建立了互联关系。

在浏览器中访问 `http://localhost:8080/` 会进入 wordpress 安装程序，安装完成后进入容器 db，你会发现数据表确实被写在该容器中。

注：如提示数据库连接错误，先进入 db 容器新建数据库，如默认名为 wordpress。
