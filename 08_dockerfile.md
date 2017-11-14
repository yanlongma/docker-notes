## 一、概念

Dockerfile 是一种文件格式的配置文件，用户可以使用 Dockerfile 来快速创建自己的镜像。


## 二、示例

Dockerfile 由一行行命令语句组成，并支持以 # 开头的注释行。一般而言，Dockerfile 分为四个部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令。

下面演示基于 ubuntu 镜像创建 nginx 镜像。

#### 1. 准备 Dockerfile 和所需文件

新建目录 nginx，在该目录下新建 index.html 文件，内容为 `Hello Docker！`；在该目录下新建 Dockerfile 文件，内容如下：
```
# 基于 ubuntu 镜像
FROM ubuntu

# 维护者是 myl
MAINTAINER myl

# 安装 nginx，并设置默认 index.html 文件
RUN apt-get update
RUN apt-get install -y nginx
COPY index.html /var/www/html

# 指定镜像的默认入口
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]

# 暴露 80 端口
EXPOSE 80
```

#### 2. 生成镜像

在 nginx 目录下执行以下命令生成镜像 `yanlongma/nginx`：
```
$ docker build -t yanlongma/nginx .
$ docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
yanlongma/nginx                   latest              e79f65bd053f        24 minutes ago      216MB
```

成功生成镜像后，使用 docker images 即可查看到。

#### 3. 运行镜像

使用 Dockerfile 生成的镜像和其它镜像一样使用，下面使用刚刚生成的 yanlongma/nginx 镜像启动一个容器：
```
$ docker run -d -p 8080:80 yanlongma/nginx
564e7509ff45416f3c99653dc79403ba4e66fdb4b078ae27de824877429f4cdc
$
$ curl http://localhost:8080
hello docker!
```

容器启动成功后，访问 `http://localhost:8080` 即可出现我们设置的 index.html 页面中内容。

