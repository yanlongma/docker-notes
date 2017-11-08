# Docker 学习笔记 - Docker 数据管理


## 一、前言

生产环境使用 Docker 的过程中，往往需要对数据进行持久化或者需要在多个容器之间进行数据共享，这必然涉及容器的数据管理操作。

容器中管理数据主要有两种方式：
1. 数据卷（Data Volumes）：容器内数据直接映射到本地主机环境；
2. 数据卷容器（Data Volumes Containers）：使用特定容器维护数据卷；


## 二、数据卷

数据卷是一个可供容器使用的特殊目录，它将主机操作系统目录直接映射进容器，类似 Linux 的 mount 操作。

数据卷可提供很多有用的特性，如下：
- 数据卷可以在容器之间共享和重用，容器间传递数据将变得高效方便；
- 对数据卷内数据的修改会立马生效，无论是容器内操作还是本地操作；
- 对数据卷的更新不会影响镜像，解耦了应用和数据；
- 卷会一直存在，直到没有容器使用，可以安全的卸载他。

#### 1. 在容器内创建一个数据卷

使用 docker run 命令时，使用 -v 参数可以在容器内创建一个数据卷。多次使用 -v 标记可以创建多个数据卷。

下面使用 ubuntu 镜像创建一个容器，并创建一个数据卷挂载到容器的 /myvolumes 目录：
```
$ docker run -it -v /myvolumes ubuntu
root@35ebd0dccc3d:/#
root@35ebd0dccc3d:/# ls 
bin  boot  dev  etc  home  lib  lib64  media  mnt  myvolumes  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

#### 2. 挂载一个主机目录作为数据卷

使用 -v 参数也可以挂载一个本地的已有目录到容器中作为数据卷。这个特性非常适合用于开发，下面演示使用 nginx 镜像创建一个容器运行 nginx 服务并挂载数据卷。

首先通过 nginx 镜像创建一个最基本的服务：
```
$ docker run --name nginx-server1 -d -p 8081:80 nginx
b3faaf910a6653fc2b2d1c99672a98bdaafd89f07eacf229e355192ee0c1cc16
```
这时我们通过本机浏览器访问 `http://localhost:8081` 会出现一个经典的 nginx 欢迎页面，如果想修改页面，只能进入容器修改，显然这并不服务我们的开发习惯。

下面通过 nginx 镜像创建一个服务，并挂载一个本地目录 /html 作为数据卷，html 目录下有一个 index.html 文件，内容为 `Hello, Data Volumes !`，命令如下：
```
$ docker run --name nginx-server2 -d -p 8082:80 -v /html:/usr/share/nginx/html nginx
ee9694da5f5364029d60e22f9136991017c76c2f40e7f8742c49173d4cc1905a
```
这时我们通过本机浏览器访问 `http://localhost:8082` 就会出现 index.html 页面的内容。修改本机 index.html 中的内容后刷新页面，发现页面的内容也随之改变，显然这更符合开发的需求。


## 三、数据卷容器

如果用户需要在多个容器之间共享一些持续更新的数据，最简单的方式是使用数据卷容器。数据卷容器也是一个容器，但是它的目的是专门用来提供数据卷供其它容器挂载。

首先创建一个数据卷容器 dbcontainer，并在其中创建一个数据卷挂载到 /dbdata：
```
$ docker run --name dbcontainer -it -v /dbdata ubuntu
root@af3425b83313:/# 
root@af3425b83313:/# ls 
bin  boot  dbdata  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

然后，其它容器可以使用 `--volumes-from` 来挂载 dbcontainer 容器中的数据卷，例如创建 web1 和 web2 两个容器，并从 dbcontainer 容器中挂载数据卷：
```
$ docker run -it --name web1 --volumes-from dbcontainer ubuntu
$ docker run -it --name web2 --volumes-from dbcontainer ubuntu
```

这时容器 web1 和 web2 都挂载同一个数据卷到相同的 /dbdata 目录。三个容器任何一方在该目录下的写入，其它容器都能看到。
