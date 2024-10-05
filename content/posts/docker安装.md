+++
title = 'Ubuntu上Docker安装'
date = 2024-10-05T17:31:39+08:00
categories = ["docker"]
tags= ["docker","Ubuntu"]
+++
## 概述
### 什么是docker

Docker是一个应用容器引擎。 一个容器可以理解成是一个轻量级的虚拟机。但是又和虚拟机有些不同。

+ docker有着比虚拟机更少的抽象层
+ docker利用的是宿主机的内核，而不需要加载操作系统OS内核

### 为什么要使用docker

+ 解决应用部署的不便，让应用部署更加简单方便
+  避免环境不同导致问题
+ 降低微服务阶段的学习成本，减少安装时间，聚焦核心
+ 为理解实际开发中的打包发布流程打基础



## docker的安装


### 准备安装环境
```shell
#安装前先卸载操作系统默认安装的docker，
sudo apt-get remove docker docker-engine docker.io containerd runc

#安装必要支持
sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg lsb-release

```

### 开始安装
```shell
#添加 Docker 官方 GPG key （可能国内现在访问会存在问题）
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# 阿里源（推荐使用阿里的gpg KEY）
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg



#添加 apt 源:
#Docker官方源
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


#阿里apt源
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


#更新源
sudo apt update
sudo apt-get update

```

```shell
#安装最新版本的Docker
sudo apt install docker-ce docker-ce-cli containerd.io
#等待安装完成

#查看Docker版本
sudo docker version

#查看Docker运行状态
sudo systemctl status docker

```

### 安装Docker 命令补全工具

```shell
sudo apt-get install bash-completion

sudo curl -L https://raw.githubusercontent.com/docker/docker-ce/master/components/cli/contrib/completion/bash/docker -o /etc/bash_completion.d/docker.sh

source /etc/bash_completion.d/docker.sh

```

###  添加docker用户组

用户在不添加sudo命令来直接使用docker



```shell
sudo groupadd docker

# 将当前用户添加到用户组
sudo usermod -aG docker $USER

# 使权限生效
newgrp docker

# 查看所有容器
docker ps -a

# 最后一步 更新.bashrc文件
sudo nvim ~/.bashrc
```

在.bashrc文件最后一样添加

```shell
groupadd -f docker
```

如果没有此行命令每次打开新的终端都必须先执行一次 “newgrp docker” 命令

![](https://i.postimg.cc/G3ksbG41/screenshot-30.png)
### 配置镜像
在 /etc/docker/daemon.json添加可以用的镜像(10.5可用)

```json
{
    "registry-mirrors": [
            "https://docker.aityp.com/",
            "https://dockerproxy.cn"
    ]
}
```

重启docker

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```

`参考文档`

[docker的安装](https://blog.csdn.net/u011278722/article/details/137673353)

[配置docker镜像](https://www.cnblogs.com/xydchen/p/18258781)