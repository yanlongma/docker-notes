# Docker 学习笔记 - 端口映射


## 一、概念

Docker 端口映射即映射容器内应用的服务端口到本机宿主机器。


## 二、实现

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


## 三、查看映射端口

使用 docker port 命令来查看当前映射的端口配置，也可以查看到绑定的地址。命令格式如下：
```
$ docker port CONTAINER [PRIVATE_PORT[/PROTO]]
```

容器有自己的内部网络和 IP 地址，可以使用 docker inspect + 容器ID 获取容器的具体信息。

