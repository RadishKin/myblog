---
title: Docker入门笔记（一）
categories:
- 笔记
tags:
- docker
- java
---




如今，如果你作为一个开发者而不知道容器化技术，那你可能要反思一下自己了，作为计算机领域发展最快最热门的技术之一，容器成为了现代互联网服务架构的基石。  
2013年DotCloud发布了容器服务Docker，并在2015年发布了其第一个正式版本，同年国内的Daocloud和灵雀云等公司开始提供基于docker的云服务业务，正是在此机会下我有幸接触了当时还非常先进的容器化技术（其实我当时对容器化还没有任何概念）。Docker在当时给我留下了最大的印象就是启动快，部署非常快，并且我当时参照daocloud官方提供的教程构建了一些镜像，不过当时的我，只是把Docker作为了一种新奇好玩的玩具，而没有深入的了解和使用。  
时间转到今天，随着微服务的兴起，容器化技术已经成为了微服务架构中的重要基石，docker对我来说不再是一个玩具，而是重要的开发部署工具，从今天起，我将重新开始学习docker,并用一系列文章来记录我学习过程。  

## Docker 概述

### 什么是Docker
虽然Docker及其背后代表的容器化技术已经非常热门了，但是容器化并不是什么新技术，于1979年出现的Chroot Jailb被认为是Dcoker技术的起源，也就是现代Linux系统中的chroot环境，下面列出的一些都是早期的容器服务：
- Chroot Jail
- FreeBSD Jails
- Linux VServer
- Solaris Containers
- OpenVZ
- Process Containers
- LXC
- Warden
- LMCTFY
- Docker
- RKT

正如我们所看到的，docker 并不是第一个容器化技术，但它的确是最知名的一个。Docker 诞生于 2013 年，并获得了快速的发展。

所以到底什么是Docker呢？

我们看看书本上的说法：
>Docker使用Google公司推出的Go语言进行开发实现，基于 Linux内核的cgroup，namespace，以及AUFS类的Union FS 等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。最初实现是基于LXC，从0.7以后开始去除LXC，转而使用自行开发的libcontainer，从1.11开始，则进一步演进为使用runC和containerd。  
Docker在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护。使得 Docker 技术比虚拟机技术更为轻便、快捷。
> ————Docker技术入门与实战第2版

实际上docker本质上就是一种虚拟化技术，与传统的虚拟化技术不同的是，他没有在虚拟化环境中虚拟出一个完整的操作系统内核，而是将虚拟化环境中的进程放在物理机内核中执行,这就是docker容器化的核心。这种方式使docker具有了很多传统虚拟化方式没有的优势。

### 为什么选择docker

#### 1.更高效的利用系统资源
由前文我们知道，dcoker容器内的程序本质上就是宿主机上运行的进程，这样就极大减小了性能的损耗，由于不需要模拟内核和周边硬件，也节省了这部分的性能损耗

#### 2.更快的启动时间
docker容器本质就是一个个宿主机内核程序，所以启动较快

#### 3.运行环境一致性
 Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性
                                            
#### 3.持续交付和部署
使用 Docker 可以通过定制应用镜像来实现持续集成、持续交付、部署。开发人员可以通过 Dockerfile 来进行镜像构建，并结合 持续集成(Continuous Integration) 系统进行集成测试，而运维人员则可以直接在生产环境中快速部署该镜像，甚至结合持续部署(Continuous Delivery/Deployment) 系统进行自动部署。

#### 4.更轻松的维护和拓展
Docker 使用的分层存储以及镜像的技术，使得应用重复部分的复用更为容易

## Docekr的镜像与容器
首先我们了解三个Docker的基本概念
- 镜像（Image）
- 容器（Container）
- 仓库（Repository）  

这三个概念组成了Docker的完整生命周期，我们将在下面的过程中详细介绍

### 环境
如今，Docker可以运行在所有的主流操作系统上，包括Linux，Windows，MacOSX，因为我平时喜欢使用Linux，本文将在Linux上部署和使用，我将使用我的笔记本电脑和一台Daocloud胶囊主机来完成本文的内容
- 本地环境：Manjaro Linux18.0+Docker18.09.0-ce,
- ~~远程主机环境：Ubuntu14.04+(因为写作到这的时候断网了就放弃远程环境了)~~

在Manjaro安装Docker非常简单，在终端输入：
```bash
sudo Pacman -S docker-ce
```
~~即可安装，并无需额外配置~~  
启动Docker服务
```bash
systemctl start docker 
```
检查Docker版本
```bash
$ docker --version
Docker version 18.09.0-ce, build 4d60db472b
```
Docker就安装好了

### 镜像
首先我们将了解Docker最核心的概念之一：镜像。镜像是Docker特殊的文件存储形式，其中储存了Linux运行时的用户空间，根据Linux的设计，Linux分为内核和用户空间，宿主机为Docker容器运行提供了内核支持，而Linux镜像为Docker运行提供了用户空间的支持。除了Linux的用户空间外，Docker镜像还储存了一些为运行时准备的一些配置参数（如匿名卷、环境
变量、用户等），Docker镜像不包含任何动态数据，构建完成后不会被改变。

由于docker使用了UnionFS技术，所以Docker实现了分层储存的特性，镜像并非是一个完整的“镜像”文件，而是一种抽象概念，在存储中，Docker镜像根据构建顺序依次完成储存，不同的镜像可以共享其中的中间层，大大减小了Docker镜像的体积。

#### 获取镜像
Docker运行前需要对应的镜像，所以我们可以从镜像仓库拉取镜像，Docker默认的镜像仓库是DockerHub，其中内置了大量高质量的Dokcer基础镜像，我们也可以将自己的镜像发布至DockerHub
>前文我们提到了仓库（Repository）的概念，仓库是一个集中存储和分发镜像的服务，一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。我们可以部署只自己仓库，也可以使用公共的仓库服务。

获取镜像使用`docker pull`命令：
```bash
docker pull [选项] [Docker Registry地址]<仓库名>:<标签>
```
如没有仓库地址名，将使用默认的仓库。
例如：
```
$ sudo docker pull node:latest
latest: Pulling from library/node
54f7e8ac135a: Already exists 
d6341e30912f: Already exists 
087a57faf949: Already exists 
5d71636fb824: Pull complete 
0c1db9598990: Pull complete 
89669bc2deb2: Pull complete 
647616206038: Pull complete 
64f67f03205b: Pull complete 
Digest: sha256:fed3f48ec9e97a57c17977ff120c3e7bf7d2d4e930ef650bba3f2ada5ac4a33a
Status: Downloaded newer image for node:latest
```
这个命令将在Docker的中央仓库下载一个最新版本的Nodejs镜像

#### 列出镜像
我们可以使用`docker images`列出所有本地的镜像
```
$ docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
docker-test                       0.0.1               ce25b22437c5        9 days ago          443MB
node                              latest              37f455de4837        9 days ago          894MB
tomcat                            latest              48dd385504b1        9 days ago          475MB
ubuntu                            latest              93fd78260bd1        3 weeks ago         86.2MB
fabric8/java-jboss-openjdk8-jdk   1.2.3               7a2a8001c977        23 months ago       422MB
```
列表包含了仓库名、标签、镜像 ID、创建时间以及所占用的空间。

docker分层构建的方式产生了大量的中间层镜像，docker默认只显示最外层的镜像，我们可以通过以下命令查看
```
$ docker images -a 
```

此外`docker images`还可以通过一些条件来查看镜像：
```bash
# 查看特定仓库名的镜像
$ sudo docker images ubuntu 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              93fd78260bd1        3 weeks ago         86.2MB

# 使用过滤器查看镜像
# 从某个镜像开始的所有镜像
sudo docker images -f since=ubuntu
# 查看特点标签的镜像
docker images -f label=com.example.version=0.1

# 使用Go语言模板制定输出格式
$ docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag
}}"

```

#### 删除镜像
当我们不需要某个镜像的时候，我们可以选择删除镜像，使用`docker rmi`命令即可删除镜像
例如：
```bash
$ sudo docker rmi tomcat:latest
```

#### 构建镜像
使用DockerFile构建镜像将在下一篇文章中详细展开

### 容器
如果你熟悉面向对象的开发的话，你可能更容易理解镜像与容器之间的关系。镜像就是面向对象中的类，而容器就是实例，容器就是实例化的镜像，容器是动态的。如前文所述，容器的实质是宿主机中的进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间,容器内的进程是隔离与宿主机环境的。

#### 运行
当我们有了镜像之后，我们就可以运行我们的镜像并建立新的容器（实例）,docker使用`docker run` 命令来运行容器
```bash

$ sudo docker run -it --rm ubuntu /bin/bash    
root@d332f4d5aecb:/# cat /etc/os-release 
NAME="Ubuntu"
VERSION="18.04.1 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.1 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
root@d332f4d5aecb:/# exit
exit
```

- --it ：这是两个参数，一个是 -i ：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终获取镜像终端。
- --rm ：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm 。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。
- ubuntu:14.04 ：这是指用 ubuntu:14.04 镜像为基础来启动容器。
/bin/bash ：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash 。

当利用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：
- 检查本地是否存在指定的镜像，不存在就从公有仓库下载
启动
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

我们可以使用`-d`参数使容器在后台运行，例如：
```
$ sudo docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
77b2dc01fe0f3f1265df143181e7b9af5e05279a884f4776ee75350ea9d8017a
```

使用`docker ps` 可以查看docker容器运行状态
```
$ sudo docker ps
```

希望获取docker日志信息
```
docker logs 77b2dc01
```

#### 终止容器
当我们需要终止容器时可以使用`docker stop` 命令,当容器z中的应用终止时，容器也会自动终止，可以使用`docker ps -a `查看中止的容器，终止的容器可以用`docker start`来重新启动,同理，`docker restart` 将会将容器重启


#### 删除容器
可以使用 `docker rm` 来删除一个处于终止状态的容器。 例如
```
$sudo docker rm trusting_newton
trusting_newton
```
如果要删除一个运行中的容器，可以添加` -f `参数。Docker 会发送 `SIGKILL`
信号给容器。

使用如下命令可以终结全部处于终止状态的容器
```
docker rm $(docker ps -a -q)
```
#### 其他操作

##### 导出容器
如果要导出本地某个容器，可以使用 `docker export ` 命令。
```
$ sudo docker export 3f6>java.tar
```

#### 导入容器
导出的本地镜像可以用`docker import`命令来导入
```bash
cat ubuntu.tar | sudo docker import - test/ubuntu:v1.0
```  
  
  
  
下一篇文章，我们讲讲解docker构建镜像的常见方式


>参考  
>[杨保华,戴王剑，曹亚伦-Docker技术入门与实战第二版](https://d.ljmx.top/Docker技术入门与实战%2B第2版.pdf)  
>[alonghub-Docker的发展历程](https://www.cnblogs.com/along21/p/9183609.html)