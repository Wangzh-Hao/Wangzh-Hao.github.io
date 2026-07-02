---
date: "2018-11-30T17:28:41+08:00"
publishdate: "2018-11-30T17:28:41+08:00"
lastmod: "2018-11-30T17:28:41+08:00"
draft: false
title: "Docker初探——Ubuntu14.04上Docker的安装"
tags: ["技术", "docker"]
series: []
categories: []
toc: true
summary: "一直很好奇Docker到底是一个什么工具，还记得实习的时候，部门经理有个任务就是安装Docker。"
---

一直很好奇Docker到底是一个什么工具，还记得实习的时候，部门经理有个任务就是安装Docker，熟悉Docker基本概念与使用。后来我从后端转前端，这事儿就耽搁了下来。恰逢最近公司有业务场景会接触使用Docker，所以趁着好奇心还在就来探索一下。

## 了解概念

没了解前，我以为Docker是一个能把几台电脑连起来相互使用运行环境和资源的工具，现在觉得自己真是错得离谱。
某种意义上，可以把Docker看成简化版的Linux操作系统，开发人员在这个容器平台上开发完产品可以连同运行环境交付给运维。从根本上消除协作编码时开发人员"在我的机器上可正常工作"，运维安装就各种不行的问题。

{{< center >}}<img name="view" src="/img/images/Docker学习(1)--初探Docker/container.png"/>{{< /center >}}

开发人员使用了 Docker，就不必安装和配置复杂的数据库，也无需在不兼容语言工具链版本之间切换时担心。应用容器化之后，其复杂性就被转移到能够轻松构建、共享和运行的容器中。当有新同事安排到新的代码库时，无需再费时费力地安装软件和解释设置过程。短短数分钟，就能够构建和调试应用。
就像上面的那张图，docker是鲸鱼，背上的一个个集装箱是相互隔离的独立运行的`容器`。集装箱里可以安装各种软件自带的代码、运行时环境、系统工具、系统库和设置的`镜像`。

## 安装

安装这部分我是按照[Docker中文文档](https://docs.docker-cn.com/engine/installation/linux/docker-ce/ubuntu/)一步步往下走的。
虚拟机版本：Ubuntu14.04(LTS)

### 卸载旧版本

```shell
$ sudo apt-get remove docker docker-engine docker.io
```

### Trusty 14.04 的推荐附加软件包

```shell
$ sudo apt-get update

$ sudo apt-get install \
    linux-image-extra-$(uname -r) \
    linux-image-extra-virtual
```

### 使用镜像仓库进行安装

首次在新的主机上安装 Docker CE 之前，您需要设置 Docker 镜像仓库。然后，您可以从此镜像仓库安装和更新 Docker。

1. 更新 apt 软件包索引：

```shell
$ sudo apt-get update
```

2. 安装软件包，以允许 apt 通过 HTTPS 使用镜像仓库：

```shell
 $ sudo apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     software-properties-common
```

3. 添加 Docker 的官方 GPG 密钥：

```shell
 $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

验证密钥指纹是否为 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88。

```shell
$ sudo apt-key fingerprint 0EBFCD88

 pub   4096R/0EBFCD88 2017-02-22
       Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
 uid                  Docker Release (CE deb) <docker@docker.com>
 sub   4096R/F273FCD8 2017-02-22
```

4. 设置 stable 镜像仓库

```shell
$ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
```

### 安装 DOCKER CE

1. 更新 apt 软件包索引，安装最新版本的 Docker CE。

```shell
$ sudo apt-get update
$ sudo apt-get install docker-ce
```

2. 验证是否正确安装了 Docker CE，运行 hello-world 镜像

```shell
$ sudo docker run hello-world
```

### 安装阿里云镜像加速器

到这Docker安装了，但由于`某些和谐因素`，国内访问DockerHub的速度偏慢，所以我们需要设置国内大厂的镜像，如`阿里云`。
正是这一步遇到一些难点让我吃尽苦头，还记得那天安装完毕已经`凌晨两点多`了。

这是因为我Ubuntu 14.04的版本过低的原因,这里呼吁安装的时候Ubuntu版本最好高于14.04，这样会省去不少麻烦。
在这也说一下解决办法，当你的内核版本是12.04、14.04，而阿里云关于配置Ubuntu的官方教程是：

```shell
$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://3rl5wcnm.mirror.aliyuncs.com"]
}
EOF
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

这时你会发现系统是没有`systemctl`命令的!
那么我解决这个问题的办法就是：

```shell
$ echo "DOCKER_OPTS=\"\$DOCKER_OPTS --registry-mirror=https://ebrzfw28.mirror.aliyuncs.com\"" | sudo tee -a /etc/default/docker
```

这个等价于上面的那几个命令，当你是14.04以下的时候，就运行这条。

### 然后重启

```shell
$ service docker restart
```

### 确认是否生效

```shell
$ sudo ps -ef | grep dockerd
```

若能看到阿里云的镜像链接就说明配置镜像加速器成功！
