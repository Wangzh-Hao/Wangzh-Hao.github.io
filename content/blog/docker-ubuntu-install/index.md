---
date: "2018-11-30T17:28:41+08:00"
publishdate: "2018-11-30T17:28:41+08:00"
lastmod: "2018-11-30T17:28:41+08:00"
draft: false
title: "Getting Started with Docker — Installing Docker on Ubuntu 14.04"
tags: ["tech", "docker"]
series: []
categories: []
toc: true
summary: "I had always been curious about what Docker really is. Back during my internship, my department manager assigned a task to install Docker."
---

I had always been curious about what Docker really is. I remember during my internship, my department manager assigned a task to install Docker and get familiar with its basic concepts and usage. Later, when I transitioned from backend to frontend development, that task got shelved. Coincidentally, my current company has a business scenario that involves using Docker, so I decided to explore it while my curiosity was still fresh.

## Understanding the Concepts

Before I understood it, I thought Docker was a tool that could connect multiple computers to share runtime environments and resources. Looking back, I couldn't have been more wrong.
In a sense, you can think of Docker as a simplified Linux operating system. Developers can build their products on this container platform and deliver them to operations teams along with the runtime environment. This fundamentally eliminates the classic problem where developers say "it works on my machine" but operations can't get it running.

{{< center >}}<img name="view" src="/img/images/Docker学习(1)--初探Docker/container.png"/>{{< /center >}}

By using Docker, developers no longer need to install and configure complex databases or worry about switching between incompatible language toolchain versions. Once an application is containerized, its complexity is transferred to containers that can be easily built, shared, and run. When a new colleague joins and needs to work on an existing codebase, there's no longer a need to spend time installing software and explaining setup procedures. Applications can be built and debugged in just minutes.

As shown in the image above, Docker is like a whale, and the containers on its back are isolated, independently running `containers`. Inside each container, you can install `images` that bundle your code, runtime environment, system tools, system libraries, and settings.

## Installation

I followed the [Docker Chinese Documentation](https://docs.docker-cn.com/engine/installation/linux/docker-ce/ubuntu/) step by step for the installation.
Virtual machine version: Ubuntu 14.04 (LTS)

### Uninstall Old Versions

```shell
$ sudo apt-get remove docker docker-engine docker.io
```

### Recommended Additional Packages for Trusty 14.04

```shell
$ sudo apt-get update

$ sudo apt-get install \
    linux-image-extra-$(uname -r) \
    linux-image-extra-virtual
```

### Install Using a Repository

Before installing Docker CE on a new host machine for the first time, you need to set up the Docker repository. After that, you can install and update Docker from the repository.

1. Update the apt package index:

```shell
$ sudo apt-get update
```

2. Install packages to allow apt to use a repository over HTTPS:

```shell
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```

3. Add Docker's official GPG key:

```shell
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Verify that the key fingerprint is 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88.

```shell
$ sudo apt-key fingerprint 0EBFCD88

 pub   4096R/0EBFCD88 2017-02-22
       Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
 uid                  Docker Release (CE deb) <docker@docker.com>
 sub   4096R/F273FCD8 2017-02-22
```

4. Set up the stable repository

```shell
$ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
```

### Install Docker CE

1. Update the apt package index and install the latest version of Docker CE.

```shell
$ sudo apt-get update
$ sudo apt-get install docker-ce
```

2. Verify that Docker CE is correctly installed by running the hello-world image:

```shell
$ sudo docker run hello-world
```

### Configure Alibaba Cloud Mirror Accelerator

At this point, Docker is installed. However, due to certain network factors, accessing DockerHub from within China is relatively slow. To speed things up, we need to set up a mirror from a domestic provider, such as Alibaba Cloud.

This step turned out to be quite challenging. I remember the installation wasn't finished until after 2:00 AM that night.

The reason was that my Ubuntu 14.04 had an older kernel version. I strongly recommend using Ubuntu versions higher than 14.04 for the installation to avoid these headaches.
Here's the workaround for those stuck with kernel versions 12.04 or 14.04. The official Alibaba Cloud tutorial for configuring Ubuntu recommends:

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

You'll quickly discover that your system doesn't have the `systemctl` command!
Here's how I solved it:

```shell
$ echo "DOCKER_OPTS=\"$DOCKER_OPTS --registry-mirror=https://ebrzfw28.mirror.aliyuncs.com\"" | sudo tee -a /etc/default/docker
```

This is equivalent to the commands above. If you're on Ubuntu 14.04 or below, run this command instead.

### Then Restart

```shell
$ service docker restart
```

### Verify It Worked

```shell
$ sudo ps -ef | grep dockerd
```

If you can see the Alibaba Cloud mirror link in the output, the mirror accelerator has been configured successfully!
