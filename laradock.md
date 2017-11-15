# Docker 学习笔记 - 使用 LaraDock 搭建 PHP 开发环境


## 一、简介

LaraDock 致力于简化创建开发环境过程，能够帮助我们在 Docker 上快速搭建 PHP 开发环境。 它预装了 Docker 镜像，为我们提供了一站式的开发环境，而不需要再去在本地机器安装 PHP、Nginx、MySQL 以及其他任何开发过程中需要的软件。 

#### 相关资料：
[LaraDock 官网：http://laradock.io](http://laradock.io)<br>
[LaraDock Github：https://github.com/LaraDock/laradock](https://github.com/LaraDock/laradock)


## 二、安装

LaraDock 镜像的原理先不介绍，直接进入安装步骤。

#### 1. 建立工作目录

新建 webroot 目录，laradock 和项目的代码都将放在该目录下（也可以直接使用以前的 web 根目录）：
```
$ mkdir webroot
```

#### 2. 克隆 LaraDock 代码

进入 webroot 目录，执行以下命令：
```
$ git clone https://github.com/Laradock/laradock.git
```

#### 3. 生成 LaraDock 配置文件

进入 laradock 目录，执行以下命令：
```
$ cp env-example .env
```

如需配置 Nginx 端口、Mysql 密码等，均在 .env 文件中设置即可。

#### 4. 开启容器

在 laradock 目录下执行以下命令，可以选择自己需要启动的容器：
```
$ docker-compose up -d nginx mysql redis
```

LaraDock 提供了很多容器，可以根据自己的选择启动。具体如下： 
```
nginx, hhvm, php-fpm, mysql, redis, postgres, mariadb, neo4j, mongo, apache2, caddy, memcached, beanstalkd, beanstalkd-console, workspace
```

#### 5. 访问 web 服务

在浏览器中访问 `http://localhost`，发现已经可以访问 nginx 服务，只不过报 404 而已。


## 三、详解

#### 1. 404 解决方法 

打开 laradock/nginx/sites/default.conf，发现 root 为 /var/www/public。在不改配置文件的情况下，只需在 webroot 目录下新建 public/index.html 文件， `http://localhost`即可正常访问。

#### 2. 数据卷

通过 docker-compose.yml 文件可以看到， webroot/ 目录被映射到 workspace 容器内 /var/www 下作为数据卷。
```
applications:
  image: tianon/true
  volumes:
    - ${APPLICATION}:/var/www		
```

#### 3. Nginx 配置虚拟主机

laradock/nginx/sites/ 目录下新建 test.conf，内容如下：
```
server {

    listen 80;
    listen [::]:80;

    server_name test.com;
    root /var/www/test;
    index index.php index.html index.htm;

}
```

webroot 目录下新建 test/index.php 文件

hosts 文件中做域名映射 127.0.0.1 test.com		

停止、构建、重启容器，在 laradock 目录下执行以下命令即可：
```
$ docker-compose down
$ docker-compose build nginx
$ docker-compose up -d nginx mysql redis
```

访问 test.com  

