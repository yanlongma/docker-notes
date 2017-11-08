# Docker 学习笔记 - Docker 概览 


## 1. 什么是 Docker
Docker 是基于 Go 语言实现的开源容器项目，诞生于2013年年初，最初发起者是 dotCloud 公司。Docker 自开源后受到广泛的关注和讨论，目前已有多个相关项目（包括 Docker 三剑客、Kubernetes 等），逐渐形成了围绕 Docker 容器的生态体系。

由于 Docker 在业界造成的影响力实在太大，dotCloud 公司后来改名为 DockerInc，并专注于 Docker 相关技术和产品的研发。


## 2. LXC
Docker 最重要的技术基石是 Linux 容器（Linux Containers, LXC）技术。


## 3. Docker 与传统的虚拟化方式的不同之处
![docker-vs-vm.jpg](http://www.mayanlong.com/usr/uploads/2017/10/2909081233.jpg)

Docker 容器除了运行其中的应用外，基本不消耗额外的系统资源，保证应用性能的同时，尽量减少系统开销。

传统虚拟机方式运行 N 个不同的应用就要起 N 个不同的虚拟机（每个虚拟机都要单独分配独占的内存、磁盘等资源），而 Docker 只需要启动 N 个隔离的“很薄的”容器，并将应用放进容器内即可。应用获得的是接近原生的运行性能。


## 4. Docker 容器技术与传统虚拟机的特性比较
<table width="100%" border="1">
	<tr><th>特性</th><th>Docker</th><th>虚拟机</th></tr>
	<tr><td>启动速度</td><td>秒级</td><td>分钟级</td></tr>
	<tr><td>性能</td><td>接近原生</td><td>较弱</td></tr>
	<tr><td>内存代价</td><td>很小</td><td>较多</td></tr>
	<tr><td>硬盘使用</td><td>一般为MB</td><td>一般为GB</td></tr>
	<tr><td>运行密度</td><td>单机支持上千个容器</td><td>一般几十个</td></tr>
	<tr><td>隔离性</td><td>安全隔离</td><td>完全隔离</td></tr>
	<tr><td>迁移性</td><td>优秀</td><td>一般</td></tr>
</table>


## 5. 为什么要使用 Docker
> Docker 的构想是要实现“Build, Ship and Run Any App, Anywhere”， 既通过对应用的封装、分发、部署、运行生命周期进行管理，达到应用组件“一次封装，到处运行”的目的。这里的应用组件，既可以是一个 Web 应用、一个编译环境，也可以是一套数据库平台服务，甚至是一个操作系统或集群。

至于为什么要使用 Docker ？我觉得用上面这段话回答非常完美，具体的优点不在此赘述。
