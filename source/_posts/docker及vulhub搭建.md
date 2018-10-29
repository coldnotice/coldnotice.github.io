---
title: docker及vulhub搭建
date: 2018-10-29 21:07:20
tags: 
- docker
---

# 一、系统：centos 7

**更改yum源:**

```
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
cd /etc/yum.repos.d/
wget http://mirrors.163.com/.help/CentOS7 Base-163.repo
yum clean all
yum makecache
```

<!-- more -->

# 二、应用容器：docker

> [**Docker**](https://docs.docker.com/) provides a way to run applications securely isolated in a container, packaged with all its dependencies and libraries.

**1. 概念:** 

- 镜像：image
- 容器：container
- 仓库：hub

**2. 安装 docker:**

```
sudo yum update
sudo yum remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-selinux \
                docker-engine-selinux \
                docker-engine

sudo yum-config-manager --add-repo  https://download.docker.com/linux/centos/docker-ce.repo
yum list docker-ce --showduplicates | sort -r
sudo yum install docker-ce

## 启动、开机启动 
sudo systemctl start docker
sudo systemctl enable docker

## 测试是否安装成功
docker version
sudo docker run hello-world`

## 卸载：
sudo yum remove docker-ce
sudo rm -rf /var/lib/docker
```

**3. docker 常用命令:**

```
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls [-a]

docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls -q                                      # List container IDs
docker container ls --all
docker container ls -aq

docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers

docker inspect <task or container>                   # Inspect task or container
docker container stop CONTAINER_ID

docker exec -it ubuntu_bash bash                     # 进入容器内部
```

# 三、PoC 验证环境：vulhub

> [**Vulhub**](https://github.com/vulhub/vulhub) is an open-source collection of pre-built vulnerable docker environments. No pre-existing knowledge of docker is required, just execute two simple commands and you have a vulnerable environment.

```
service docker start                     # 启动docker服务
pip install docker-compose               # 安装compose

# 下载项目
wget https://github.com/vulhub/vulhub/archive/master.zip -O vulhub-master.zip
unzip vulhub-master.zip
cd vulhub-master

cd flask/ssti                    # 进入某一个漏洞/环境的目录
docker-compose build             # 自动化编译环境
docker-compose up -d             # 启动整个环境
docker-compose stop              # 停止整个环境
docker-compose down -v           # 删除整个环境

docker-compose.yml               # 配置文件
```

