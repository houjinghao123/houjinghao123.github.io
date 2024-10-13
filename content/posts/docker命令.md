+++
title = 'Docker命令'
date = 2024-10-05T17:49:22+08:00
categories = ["Docker"]
tags= ["Docker","Ubuntu"]
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

**docker exec [选项] 容器ID或容器名 命令 [参数...]**

后台运行一个nginx镜像的容器，然后尝试以交互式的方式进入该容器内部执行 curl 指令测试nginx是否启动成功

![](https://i.postimg.cc/1X3MWGmq/screenshot-33.png)

- docker exec -it  容器id bash

![](https://i.postimg.cc/fTqKH6Mv/screenshot-36.png)

这个命令是进入正在运行的容器， -it参数是可交互命令行，bash是所使用的命令行方式。

-i 以交互模式运行容器，通常与 -t 同时使用

-t 启动容器后，为容器分配一个命令行，通常与 -i 同时使用

### 查看容器日志

**docker logs[选项]容器ID或容器名**

- 查看容器日志并且是持续输出

​	docker logs -f 容器id

- 查看容器的最近20条日志

​	docker logs -n 20 容器id

### 容器文件拷贝

我们可以使用 docker cp 命令来实现容器和宿主机之间 文件和目录的相互拷贝

- 命令

​	把容器中的文件拷贝到宿主机中

​	docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH

注意开放权限

![](https://i.postimg.cc/Wp7zPkY9/screenshot-37.png)

​	把宿主机的文件拷贝到容器中

​	docker cp [OPTIONS] SRC_PATH CONTAINER:DEST_PATH

![](https://i.postimg.cc/L5Pr2L2x/screenshot-40.png)

### 停止容器

- 命令

  docker stop [选项] [容器ID或容器名...]

### 运行容器

- 命令

​	docker start [选项] 容器ID或容器名

