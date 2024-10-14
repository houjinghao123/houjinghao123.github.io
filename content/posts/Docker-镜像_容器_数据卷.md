+++
title = 'Docker 数据卷 网桥'
date = 2024-10-14T19:55:51+08:00
+++

# 镜像

## 如何查看镜像的详细信息



**方法**

```shell
docker image inspect [OPTIONS] IMAGE [IMAGE...]
docker image inspect 镜像id
```

# 容器

##  查看容器内进程

**方法**

```shell
docker top CONTAINER [ps OPTIONS]
docker top nginx_test
```



## 查看容器详细信息

**方法**

```shell
docker inspect [OPTIONS] NAME|ID [NAME|ID...]

```

# 数据卷

## 作用

实现宿主机和容器之间文件或者文件夹的同步。一般用来解决容器数据的持久化或者是宿主机和容器间的数据共享。

## 数据卷相关操作

### 设置数据卷

- 绝对路径

```shell
docker run -v 宿主机目录:容器目录[:读写权限] 镜像名
```

- 别名

我们可以直接使用数据卷的别名来作为宿主机的目录来使用。如果这个别名的数据卷还不存在的话，Docker会自动帮我们创建对应的数据卷。

```shell
docker run -v 数据卷别名:容器目录[:读写权限] 镜像名
```

 ### 列出所有的数据卷

```shell
docker volume ls
```

 ###  查看数据卷详情

```shell
docker volume inspect 数据卷名
```

 ### 创建数据卷

```shell
docker volume create 数据卷名
# 查看数据卷在宿主机的位置
docker volume inspect
```

 ### 删除数据卷

```shell
docker volume rm 数据卷名
```

# 网络(网桥)

## 作用

虽然默认情况下容器和容器可以进行网络通信。但是每次创建容器都是Docker给容器分配的IP地址这让我们使用起来不太方便。

这些情况我们都可以创建自定义网络来解决这些问题。把需要互相连通的容器加入到同一个网络，这样容器和容器之间就可以通过容器名来代替ip地址进行互相访问。

## 网络相关操作

### 创建网络

```shell
docker network create 网络名
# 例
docker network create blog_net
```

### 列出网络

```shell
docker network ls
```



### 加入网络

- 创建容器时加入

  我们可以在容器创建时使用--network选项让容器创建时就加入对应的网络。

```shell
docker run --network 网络名 镜像名
```

- 容器创建后加入

```shell
# 如果容器已经创建了想加入网络可以使用docker network connect命令。
docker network connect [选项] 网络名 容器名或容器id
```

### 查看网络详情

可以查看都有那些容器加入该网络

```shell
docker network inspect 网络名或者网络id
```

### 移除网络

```shell
docker network rm 网络名或网络id
```





