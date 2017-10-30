# Docker 学习笔记 - Docker 容器命令汇总


## 一、前言

容器是 Docker 的另一个核心概念。简单来说，容器是镜像的一个运行实例。所不同的是，镜像是静态的只读文件，而容器带有运行时需要的可写文件层。

如果认为虚拟机是模拟运行的一整套操作系统（包括内核、应用运行态环境和其他系统环境）和跑在上面的应用，那么 Docker 容器就是独立运行的一个（或一组）应用，以及它们必须的运行环境。

下面总结了常用的容器相关命令。


## 二、查看容器

查看容器信息相关命令，下面会用到：
```
docker ps        列出所有启动状态的容器
docker ps -a     列出所有容器
docker ps -qa    列出所有容器 ID
```


## 三、创建容器

#### 1. 新建容器

使用 docker create 命令新建一个容器，会产生容器 ID，例如：
```
$ docker create -it ubuntu:latest
ba8bc0e5995e56a76ae0361687316ce6062247752c6754d2b16bc135e306233f
```

使用 docker create 命令新建的容器处于停止状态，我们可以使用 docker ps -a 查看：
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
ba8bc0e5995e        ubuntu:latest       "/bin/bash"         About a minute ago   Created                                 brave_engelbart 
```

#### 2. 启动容器

使用 docker start 命令来启动一个已经创建的容器，例如启动刚创建的 ubuntu 容器（使用上面产生的容器 ID）：
```
$ docker start ba8b
ba8b
```

再用 docker ps 查看，可以看到该容器处于启动状态：
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
ba8bc0e5995e        ubuntu:latest       "/bin/bash"         18 minutes ago      Up 10 minutes                           brave_engelbart
```

#### 3. 新建并启动容器

使用 docker run 可以直接新建并启动容器：
```
$ docker run ubuntu:latest /bin/echo 'Hello World'
Hello World
```

使用 `-t` 参数将启动一个 bash 终端，允许用户交互：
```
$ docker run -it ubuntu:latest /bin/bash
root@976214c9686c:/#
```

有时需要让 Docker 容器在后台以守护态形式运行，可以通过添加 `-d` 参数来实现。


## 四、终止容器

使用 docker stop 命令来终止一个运行中的容器。例如终止上面的 ba8b 容器：
```
$ docker stop ba8b
ba8b
```


## 五、进入容器

使用 docker exec 命令可以进入一个启动状态的容器中，例如下面启动 ba8b 容器并进入：
```
$ docker start ba8b
ba8b
$ docker exec -it ba8b /bin/bash
root@ba8bc0e5995e:/# 
```


## 六、删除容器

使用 docker rm 命令可以删除处于终止或退出状态的容器，该命令格式为：
```
$ docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

选项为：
```
-f, --force     强行终止并删除运行中的容器
-l, --link      删除容器的连接
-v, --volumes   删除容器挂载的数据卷
```

例如强制删除运行中的 ba8b 容器：
```
$ docker rm -f ba8b
ba8b
```


## 七、导出和导入容器

#### 1. 导出容器

使用 docker export 可以导出容器到文件，不管此时容器处于什么状态。

下面导出一个容器 ID 为 fd15 的容器到文件：
```
$ docker export -o test1.tar fd15
```

也可以：
```
$ docker export fd15 > test2.tar
```

#### 2. 导入容器

导出的文件又可以使用 docker import 命令导入变成镜像，下面将导出的 test1.tar 文件导入到本地镜像库中：
```
$ docker import test1.tar  yanlongma/ubuntu:20171030
```
