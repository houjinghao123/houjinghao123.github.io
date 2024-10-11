+++
title = 'Docker命令'
date = 2024-10-05T17:49:22+08:00
categories = ["docker"]
tags= ["docker","Ubuntu"]
+++



## 镜像和容器相关命令



### 搜索镜像

- 使用Docker自带的搜索命令可能会因为网络问题搜索失败

```shell
docker search contains name:vesion
```

- 我们可以使用docker search去搜索镜像，也可以去Docker-Hub找自己需要的镜像。(推荐)

 ### 下载镜像

```dockerfile
docker pull [镜像仓库地址/]镜像名[:标签]
```

### 列出镜像信息

- docker images [选项]
![](https://i.postimg.cc/Y24gPRLr/screenshot-31.png)
- 相关操作
  - 列出镜像的镜像id docker images -q
  - 列出所有镜像的的镜像id docker images -aq 或者 docker images -a -q
  - 列出所有镜像名为mysql的镜像id docker images -aq --filter=reference=mysql

### 列出容器信息

- docker ps [选项]默认显示正在运行的容器信息

- 相关操作

  - 列出当前正在运行的容器 docker ps
  - 列出所有容器，无论是否在运行 docker ps -a
  - 列出所有退出状态的容器 docker ps -f status=exited
  - 列出所有退出状态的容器id docker ps -f status=exited -q

### 创建并运行容器

- docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
- docker stop[ids]  停止容器的运行

#### 容器的运行方式

- 默认的运行方式 docker run nginx:latest
- 后台运行 docker run -d nginx:latest
- 交互式运行 docker run -it nginx:latest bash



### 删除容器

- docker rm [选项]  [容器ID或容器名...]
- 相关操作
  - 删除hello-world的容器 docker rm 2d1ec2bba545
  - 尝试删除一个运行的nginx容器  docker rm -f fa8075110f21
  - 尝试删除所有容器 docker rm -f $(docker ps -aq )
  - 尝试删除所有非运行状态的容器 docker ps -f status=exited -q 或者 docker rm $(docker ps -f status=exited -q)

![](https://i.postimg.cc/MHW8PQR2/screenshot-32.png)

### 进入容器执行命令

- docker exec [选项] 容器ID或容器名 命令 [参数...]

后台运行一个nginx镜像的容器，然后尝试以交互式的方式进入该容器内部执行 curl 指令测试nginx是否启动成功

![](https://i.postimg.cc/1X3MWGmq/screenshot-33.png)















