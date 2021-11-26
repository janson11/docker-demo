# docker-demo
docker demo

## 1 Docker的原理
Docker 是利用 Linux 的 Namespace 、Cgroups 和联合文件系统三大机制来保证实现的， 所以它的原理是使用 Namespace 做主机名、网络、PID 等资源的隔离，使用 Cgroups 对进程或者进程组做资源（例如：CPU、内存等）的限制，联合文件系统用于镜像构建和容器运行环境。
* Namespace:
    Docker 主要用到以下五种命名空间。
    pid namespace：用于隔离进程 ID。
    net namespace：隔离网络接口，在虚拟的 net namespace 内用户可以拥有自己独立的 IP、路由、端口等。
    mnt namespace：文件系统挂载点隔离。
    ipc namespace：信号量,消息队列和共享内存的隔离。
    uts namespace：主机名和域名的隔离。
* Cgroups
  Cgroups 是一种 Linux 内核功能，可以限制和隔离进程的资源使用情况（CPU、内存、磁盘 I/O、网络等）。在容器的实现中，Cgroups 通常用来限制容器的 CPU 和内存等资源的使用。
* 联合文件系统
  联合文件系统，又叫 UnionFS，是一种通过创建文件层进程操作的文件系统，因此，联合文件系统非常轻快。Docker 使用联合文件系统为容器提供构建层，使得容器可以实现写时复制以及镜像的分层构建和存储。常用的联合文件系统有 AUFS、Overlay 和 Devicemapper 等。

## 2 Docker核心概念
* 镜像：
  它是一个只读的文件和文件夹组合。它包含了容器运行时所需要的所有基础文件和配置信息，是容器启动的基础。所以你想启动一个容器，那首先必须要有一个镜像。镜像是 Docker 容器启动的先决条件。
  镜像的使用方式：
  1、自己创建镜像。通常情况下，一个镜像是基于一个基础镜像构建的，你可以在基础镜像上添加一些用户自定义的内容。
  2、从功能镜像仓库拉取别人制作好的镜像。
  
* 容器：
  容器是 Docker 的另一个核心概念。通俗地讲，容器是镜像的运行实体。镜像是静态的只读文件，而容器带有运行时需要的可写文件层，并且容器中的进程属于运行状态。即容器运行着真正的应用进程。容器有初建、运行、停止、暂停和删除五种状态。
  虽然容器的本质是主机上运行的一个进程，但是容器有自己独立的命名空间隔离和资源限制。也就是说，在容器内部，无法看到主机上的进程、环境变量、网络等信息，这是容器与直接运行在主机上进程的本质区别。

* 仓库：
 Docker 的镜像仓库类似于代码仓库，用来存储和分发 Docker 镜像。镜像仓库分为公共镜像仓库和私有镜像仓库。
 
目前主要有两个标准文档：容器运行时标准 （runtime spec）和容器镜像标准（image spec）。

## 3 Docker命令
 启动命令：
 docker run -d busybox sleep 3600
 通过以下命令查看一下 dockerd 的 PID：
 ps aux |grep dockerd
 进程父子关系
  sudo pstree -l -a -A 4147

构建镜像
使用docker commit命令从运行中的容器提交为镜像；

使用docker build命令从 Dockerfile 构建镜像。


使用 Dockerfile 构建镜像具有以下特性：
Dockerfile 的每一行命令都会生成一个独立的镜像层，并且拥有唯一的 ID；
Dockerfile 的命令是完全透明的，通过查看 Dockerfile 的内容，就可以知道镜像是如何一步步构建的；
Dockerfile 是纯文本的，方便跟随代码一起存放在代码仓库并做版本管理。

## 4 Dockerfile命令

Dockerfile 指令	指令简介

|Dockerfile指令   |指令简介|
|---|---|
|FROM|Dockerfile 除了注释第一行必须是 FROM ，FROM 后面跟镜像名称，代表我们要基于哪个基础镜像构建我们的容器。|
| RUN  |  RUN 后面跟一个具体的命令，类似于 Linux 命令行执行命令。 |
| ADD  | 拷贝本机文件或者远程文件到镜像内  |
| COPY  | 拷贝本机文件到镜像内  |
|USER| 指定容器启动的用户  |
| ENTRYPOINT  | 容器的启动命令  |
| CMD  | CMD 为 ENTRYPOINT 指令提供默认参数，也可以单独使用 CMD 指定容器启动参数  |
|ENV|  指定容器运行时的环境变量，格式为 key=value |
| ARG  | 定义外部变量，构建镜像时可以使用 build-arg = 的格式传递参数用于构建  |
|  EXPOSE |  指定容器监听的端口，格式为 [port]/tcp 或者 [port]/udp |
|  WORKDIR |  为 Dockerfile 中跟在其后的所有 RUN、CMD、ENTRYPOINT、COPY 和 ADD 命令设置工作目录。 |



RUN	RUN 后面跟一个具体的命令，类似于 Linux 命令行执行命令。
ADD	拷贝本机文件或者远程文件到镜像内
COPY	拷贝本机文件到镜像内
USER	指定容器启动的用户
ENTRYPOINT	容器的启动命令
CMD	CMD 为 ENTRYPOINT 指令提供默认参数，也可以单独使用 CMD 指定容器启动参数
ENV	指定容器运行时的环境变量，格式为 key=value
ARG	定义外部变量，构建镜像时可以使用 build-arg = 的格式传递参数用于构建
EXPOSE	指定容器监听的端口，格式为 [port]/tcp 或者 [port]/udp
WORKDIR	为 Dockerfile 中跟在其后的所有 RUN、CMD、ENTRYPOINT、COPY 和 ADD 命令设置工作目录。


镜像操作命令：

拉取镜像，使用 docker pull 命令拉取远程仓库的镜像到本地 ；

重命名镜像，使用 docker tag 命令“重命名”镜像 ；

查看镜像，使用 docker image ls 或 docker images 命令查看本地已经存在的镜像；

删除镜像，使用 docker rmi 命令删除无用镜像 ；

构建镜像，构建镜像有两种方式。第一种方式是使用 docker build 命令基于 Dockerfile 构建镜像，也是我比较推荐的镜像构建方式；第二种方式是使用 docker commit 命令基于已经运行的容器提交为镜像。

镜像的实现原理：
镜像是由一系列的镜像层（layer ）组成，每一层代表了镜像构建过程中的一次提交，当我们需要修改镜像内的某个文件时，只需要在当前镜像层的基础上新建一个镜像层，并且只存放修改过的文件内容。分层结构使得镜像间共享镜像层变得非常简单和方便。2021年11月26日