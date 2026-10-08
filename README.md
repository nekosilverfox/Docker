<!-- SPbSTU 报告起始 -->

<div align="center">
  <!--<img width="250px" src="https://raw.githubusercontent.com/NekoSilverFox/NekoSilverfox/403ab045b7d9adeaaf8186c451af7243f5d8f46d/icons/new_logo_spbstu_ru.svg" align="center" alt="new_logo_spbstu_ru" />  新式 π logo -->
  <img width="250px" src="https://github.com/NekoSilverFox/NekoSilverfox/blob/master/icons/logo_building_spbstu.png?raw=true" align="center" alt="logo_building_spbstu" /> <!-- 研究型大学 logo -->
  </br>
  <b><font size=3>Санкт-Петербургский политехнический университет Петра Великого</font></b>
  </br>
  <b><font size=2>Институт компьютерных наук и технологий</font></b>
  </br>
  <b><font size=2>Высшая школа программной инженерии</font></b>
</div>


<div align="center">
<b><font size=6>Docker 学习笔记</font></b>


[![License](https://img.shields.io/badge/license-Apache%202.0-brightgreen)](LICENSE)

</div>
<div align=left>
<div STYLE="page-break-after: always;"></div>
<!-- SPbSTU 报告结束 -->


[toc]

# Docker

## 简介

> Docker 是一种容器技术，解决软件跨环境迁移的问题。Docker 可以减少运维部署相关的成本。不需要写代码，基本都是 Linux 命令的操作。

**Docker是一个开源的应用容器引擎，是一个快速交付应用、运行应用的技术，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的Linux机器上，也可以实现虚拟化，容器是完全使用沙箱机制，相互之间不会有任何接口。**

Docker 概念：

- Docker 是一个开源的应用容器引擎
- 基于 Go 语言实现
- Docker 可以让开发者打包自己的应用以及环境依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上
- 容器使用沙箱机制，相互隔离
- 容器性能开销极低
- ==Docker 直接调用各个 Linux 系统发行版本的 **Linux内核**，而不是包含一整个操作系统==



**Docker 如何解决依赖的兼容问题的？**

- 将应用的Libs（函数库）、Deps（依赖）、配置与应用一起打包

- 将每个应用放到一个隔离**容器**去运行，避免互相干扰

<img src="doc/pic/image-20230416165958517.png" alt="image-20230416165958517" style="zoom:50%;" />

这样打包好的应用包中，既包含应用本身，也保护应用所需要的Libs、Deps，无需再操作系统上安装这些，自然就不存在不同应用之间的兼容问题了。

虽然解决了不同应用的兼容问题，但是开发、测试等环境会存在差异，操作系统版本也会有差异，怎么解决这些问题呢？



**Docker如何解决不同系统环境的问题？**

- Docker将用户程序与所需要调用的系统(比如Ubuntu)==函数库==一起打包

- Docker运行到不同操作系统时，直接基于打包的库函数，借助于操作系统的Linux内核来运行

    <img src="doc/pic/image-20230416170215882.png" alt="image-20230416170215882" style="zoom:50%;" />



**Docker 如何解决大型项目依赖关系复杂，不同组件依赖的兼容性问题？**

- Docker允许开发中将应用、依赖、函数库、配置一起**打包**，形成可移植==镜像==

- Docker应用运行在==容器==中，使用沙箱机制，相互**隔离**



**Docker如何解决开发、测试、生产环境有差异的问题**

- Docker镜像中包含完整运行环境，包括系统函数库，仅依赖系统的Linux内核，因此可以在任意Linux操作系统上运行



## Docker 与虚拟机

虚拟机（virtual machine）是在操作系统中模拟硬件设备，然后运行另一个操作系统，比如在 Windows 系统里面运行 Ubuntu 系统，这样就可以运行任意的Ubuntu应用了。

**Docker**仅仅是封装函数库，并没有模拟完整的操作系统，如图：

![image-20230416171221246](doc/pic/image-20230416171221246.png)

**Docker和虚拟机的差异：**

- ==docker是一个系统进程==；虚拟机是在操作系统中的操作系统

- docker体积小、启动速度快、性能好；虚拟机体积大、启动速度慢、性能一般





# 架构

**镜像和容器**

- **镜像（Image）**：Docker将应用程序及其所需的依赖、函数库、环境、配置等文件打包在一起，称为镜像。镜像都是只读的，容器只能读镜像中的文件，不能进行写操作
- **容器（Container）**：镜像中的应用程序运行后形成的进程就是**容器**，只是Docker会给容器做隔离，对外不可见，避免污染镜像。容器会以为自己是这个主机上唯一的进程



**Docker是一个CS架构的程序，由两部分组成：**

- **服务端（server）**： Docker守护进程，负责处理Docker指令，管理镜像、容器等
- **客户端（client）**： 通过命令或RestAPl向Docker服务端发送指令。可以在本地或远程向服务端发送指令。

![image-20230416135410319](doc/pic/image-20230416135410319.png)

- `daemon` - 是守护进程，控制开启关闭或者开始 Docker 容器

---





---



**DockHub**：一个镜像托管的服务器，类似的还有阿里云镜像服务，统称为 **DockerRegistry （镜像服务器）**。我们一方面可以将自己的镜像共享到DockerHub，另一方面也可以从DockerHub拉取镜像：

![image-20230416175800875](doc/pic/image-20230416175800875.png)

----



# Docker 容器的 Linux Kernel 共享机制

Docker 容器**不是一台完整的 Linux 虚拟机**。

即使使用不同的 Docker Image 创建容器，这些容器在同一个 Docker Linux 环境中运行时，仍然会==**共享同一个 Linux Kernel**。==

例如：

```
                    Linux Kernel
                         │
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
   Ubuntu Container Debian Container Alpine Container
```

这里：

- Ubuntu、Debian、Alpine 是不同的 **用户空间（Userspace）**
- 三个 Container 是不同的运行环境
- ==**Linux Kernel 只有一个**==

------



## Docker Image 中有什么？

Docker Image 主要包含：

```
Ubuntu Image
├── /bin
├── /etc
├── /usr
├── /lib
├── /var
├── glibc
├── bash
└── 其他用户空间程序
```

可以简单理解为：

> **Docker Image = 文件系统 + 用户空间程序**

它通常**不包含一个需要独立启动的 Linux Kernel**。

因此：

```
docker run ubuntu
docker run debian
docker run alpine
```

虽然使用了三个不同的 Image，但==它们并不会各自启动一个 Linux Kernel。==

------



## 不同 Image 也会共享 Kernel

例如：

```
                         Linux Kernel
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
        Ubuntu C1         Debian C2        Alpine C3
        glibc + bash      glibc + bash     musl + sh
```

三个容器可以拥有完全不同的：

- Linux 发行版
- C/C++ 运行库
- Shell
- Python 版本
- 系统工具
- 文件系统内容

但是它们执行底层系统调用时，最终都会进入**同一个 Linux Kernel**。

例如：

```
Container A ──┐
Container B ──┼──→ Linux Kernel
Container C ──┘
```

常见系统调用包括：

```
open()
read()
write()
socket()
mmap()
clone()
```

这些操作最终都是由共享的 Linux Kernel 负责执行。

------

## 为什么容器能够做到隔离？

虽然容器共享 Kernel，但并不意味着容器之间完全没有隔离。

Linux Kernel 本身提供了多种隔离机制，其中最重要的是：

### Namespace

Namespace 决定：

> **容器能够“看到”什么。**

例如不同容器可以拥有各自独立的：

- PID
- Network
- Mount
- IPC
- Hostname
- User

因此 Container A 中可能看到：

```
PID 1
PID 2
PID 3
```

Container B 中也可能看到：

```
PID 1
PID 2
PID 3
```

虽然它们背后实际上都由**同一个 Kernel**管理。

------

### cgroups

cgroups 决定：

> **容器能够使用多少资源。**

例如：

```
Linux Kernel
│
├── Container A
│   ├── CPU ≤ 2 cores
│   └── Memory ≤ 4 GB
│
└── Container B
    ├── CPU ≤ 1 core
    └── Memory ≤ 2 GB
```

Kernel 负责限制：

- CPU
- Memory
- Disk I/O
- Process 数量等

因此可以简单记忆：

> **Namespace：隔离“看到什么”**
> **cgroups：限制“能用多少”**

------



## 与虚拟机的区别

### Docker Container

```
                 Linux Kernel
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
     Container A Container B Container C
       Ubuntu       Debian       Alpine
```

特点：

> **多个容器共享一个 Linux Kernel。**

------

### Virtual Machine

```
             Hypervisor
          ┌──────┼──────┐
          ▼      ▼      ▼
        VM A   VM B   VM C
          │      │      │
       Kernel A Kernel B Kernel C
          │      │      │
       Ubuntu  Debian  Alpine
```

特点：

> **每台 VM 都拥有自己的 Linux Kernel。**

所以两者最核心的区别可以记成：

|                 | Docker Container      | Virtual Machine |
| --------------- | --------------------- | --------------- |
| Kernel          | **共享**              | **独立**        |
| 用户空间        | 独立                  | 独立            |
| 隔离方式        | Namespace、cgroups 等 | 硬件虚拟化      |
| 启动开销        | 通常较低              | 通常较高        |
| 是否包含完整 OS | 否                    | 是              |

------



## macOS 上的 Docker Desktop

在 macOS 上还需要特别注意：

**Docker Container 并不是直接运行在 macOS 的 XNU Kernel 上。**

大致结构是：

```
macOS
 │
 │
 └── Linux VM
      │
      └── Linux Kernel
           │
           ├── Ubuntu Container
           ├── Debian Container
           └── Alpine Container
```

因此在 Mac 上：

> **多个 Docker Container 共享的是 Docker Desktop Linux VM 中的 Linux Kernel，而不是 macOS 的 Kernel。**

例如在容器中执行：

```
uname -r
```

看到的 Kernel 版本实际上对应的是 Docker 所运行的 Linux 环境。

------



**一个非常重要的结论：**

**更换 Docker Image ≠ 更换 Linux Kernel。**

例如：

```
Ubuntu Image
       ↓
Ubuntu Container
       ↓
共享 Linux Kernel

Debian Image
       ↓
Debian Container
       ↓
共享 Linux Kernel

Alpine Image
       ↓
Alpine Container
       ↓
共享 Linux Kernel
```

因此，即使：

```
Ubuntu 24.04
Debian 13
Alpine 3.x
```

三个容器使用完全不同的用户空间，它们仍然可以共享同一个 Linux Kernel。

------



**一句话记忆:**

> **Docker Container：共享 Kernel，隔离用户空间。**

> **Virtual Machine：Kernel 也相互独立。**

可以把 Docker Image 简单理解成：

> **“我要给你什么软件环境。”**

把 Container 理解成：

> **“我要运行这个软件环境里的进程。”**

把 Linux Kernel 理解成：

> **“我负责管理 CPU、内存、网络、磁盘以及系统调用。”**

最终结构就是：

```
不同 Image
    ↓
不同 Container
    ↓
共享 Linux Kernel
    ↓
CPU / RAM / Disk / Network
```



# Docker 服务相关命令

## 守护进程 (daemon)

> 基于 CentOS 7

| 命令                     | 说明                |
| ------------------------ | ------------------- |
| systemctl start docker   | 启动 Docker         |
| systemctl status docker  | docker 状态         |
| systemctl stop docker    | 关闭 docker         |
| systemctl restart docker | 重启 docker         |
| systemctl enable docker  | 开机自动启动 docker |

## 镜像 (image)

镜像内部涵盖了一个最小操作系统和我们的程序及依赖包

### 镜像名称

首先来看下镜像的名称组成：

- 镜名称一般分两部分组成：[repository]:[tag]。
- 在没有指定tag时，默认是latest，代表最新版本的镜像

如图：

<img src="doc/pic/image-20230416180102697.png" alt="image-20230416180102697" style="zoom: 25%;" />

这里的mysql就是repository，5.7就是tag，合一起就是镜像名称，代表5.7版本的MySQL镜像。

### 命令

| 命令                                         | 说明                                                     |
| -------------------------------------------- | -------------------------------------------------------- |
| `docker images`                              | 查看本地镜像（列表）。输出表格中的 `latest` 代表最新版本 |
| `docker search IMAGE_NAME`                   | 搜索镜像（远程镜像库）                                   |
| `docker pull IMAGE_NAME:TAG`                 | 拉取镜像，不写版本`TAG`代表最新版 `latest`               |
| `docker rmi IMAGE_ID 或者 IMAGE_NAME:TAG`    | 删除镜像（rm 代表 remove，i 代表 image）                 |
| `docker save -o 指定文件.tar IMAGE_NAME:TAG` | 保存镜像（`-o`代表 output 至 tar 文件）                  |
| `docker load -i 读取的文件.tar`              | 读取镜像                                                 |



可以使用命令组合，使用 "`"（Tab 键上面那个符号）可以在一个命令里使用另一个命令的结果。比如：

- `docker images -q` 代表输出所有本地镜像的 id 号
- `docker rmi IMAGE_ID` 代表删除一个镜像

他们“撇”组合之后的代表删除本地所有镜像：

```bash
 docker rmi `docker images -q`
```



## 容器 (container)

容器是通过镜像创建的，这节就是创建并操作容器

<img src="doc/pic/image-20230416180344386.png" alt="image-20230416180344386" style="zoom: 67%;" />

### **创建容器**

`docker run -参数 --name=容器自定义名字 -p 宿主机端口:容器端口 IMAGE:TAG /bin/bash`

- `-i` 保持容器**始终运行**，通常 与`-t` 同时使用。加入`-it`这两个参数后，容器创建后自动进入容器中，退出容器后，容器自动关闭。

- `-t` 为容器重新分配一个伪输入终端，通常与`-i`同时使用。

    通过 `-it` 创建的容器，退出后会立刻关闭

- `-d` 以守护(后台)模式运行容器。创建一个容器在后台运行，需要使用 `docker exec` 进入容器。**退出后，容器不会关闭**

    通过 `-id` 创建的容器，退出后会**不会**关闭

- `-p` 将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口

- `/bin/bash` 初始化指令可以替换成别的



`-it` 创建的容器一般称为交互式容器，`-id` 创建的容器一般称为守护式容器



### **查看容器**

`docker ps -参数`

- 如果不加参数只输出**正在运行的容器**
- `-a` 输出所有容器



### 其他命令

| 命令                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `docker run -参数 --name=容器自定义名字 IMAGE:TAG /bin/bash` | **创建**容器，并让其运行                                     |
| `docker pause `                                              | **暂停**容器（挂起进程）                                     |
| `docker unpause`                                             | 取消暂停容器                                                 |
| `docker ps -参数`                                            | 查看所有运行容器及其状态                                     |
| `docker start 容器名`                                        | **启动**容器                                                 |
| `docker exec -it 容器名字 /bin/bash`                         | 进入容器                                                     |
| `docker stop 容器名`                                         | **停止**容器（杀死进程）                                     |
| `docker rm 容器名或ID`                                       | 删除指定容器                                                 |
| `docker imspect 容器名`                                      | 查看容器信息                                                 |
| `docker logs 容器名`                                         | 查看日志，`-f` 代表持续跟踪（follow）日志，按 Ctrl+C 退出日志跟踪，但不会关闭容器 |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |



### 容器中的数据卷



# 配置可使用 SSH 的容器

> https://www.cherryservers.com/blog/ssh-into-docker-container

1. 创建 `Dockerfile`

    **注意：**

    - **ubuntu 16.04 可以连接，而 latest 版本连不上。需要使用其他办法连接**
    - 要把以下文件中的密码 `YOUR_PASSWORD` 改成自己的

    ```dockerfile
    FROM ubuntu:16.04
    RUN apt-get update && apt-get install -y openssh-server
    RUN mkdir /var/run/sshd
    
    # Set root password for SSH access (change 'YOUR_PASSWORD' to your desired password)
    RUN echo 'root:YOUR_PASSWORD' | chpasswd
    RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
    RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
    EXPOSE 22
    CMD ["/usr/sbin/sshd", "-D"]
    ```

    - `FROM ubuntu:16.04`：将标准的 Ubuntu 16.04 镜像（可以在 Docker Hub 上找到）作为我们自定义 Docker 镜像的基础镜像。接下来的指令将在此基础镜像上构建。

    - `RUN apt-get update && apt-get install -y openssh-server`：该命令更新容器内的包索引并安装 OpenSSH-server 包，这是 SSH 服务器功能所需的。

    - `RUN mkdir /var/run/sshd`：用于在容器内部设置目录 `/var/run/sshd`。SSH 守护进程需要此目录才能正常运行。

    - `RUN echo 'root:your_password' | chpasswd`：在容器内部设置 root 用户的密码。将 `'your_password'` 替换为你想要的密码。

    - `RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config`：修改 `sshd_config` 文件以启用 root 密码登录，这意味着允许 "root" 用户使用密码登录，而不是依赖 SSH 密钥等其他认证方法。

    - `RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd`：修改 SSH 守护进程的可插拔认证模块配置，以防止与 systemd 可能出现的问题。

    - `EXPOSE 22`：在容器中暴露端口 22，允许对容器内运行的 SSH 服务器进行 SSH 连接。

    - `CMD ["/usr/sbin/sshd", "-D"]`：指定从此镜像启动容器时运行的默认命令。在这种情况下，它以前台模式启动 SSH 守护进程，并使用 `-D` 选项，允许容器在 SSH 服务器活动时保持运行。

        

2. 通过  `Dockerfile` 构建镜像，然后创建并运行容器

    ```bash
    docker build -t 镜像名字 .
    docker run -id -p 主机端口:22 --name 容器名 镜像名
    docker start 容器名
    ```

    

3. 使用 SSH 工具进行连接









































