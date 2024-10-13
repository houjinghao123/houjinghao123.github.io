+++
title = 'Docker Run命令详解'
date = 2024-10-13T16:48:15+08:00

+++

#Run命令详解

## -p 端口对外发布（端口映射）

###  解释

为了使外部网络能够访问到容器

将容器端口发布到宿主机端口，就可以通过宿主机的端口访问容器内的端口了。

### 使用方法

```shell
docker run -p 宿主机端口:容器端口 镜像名   #发布一个端口
docker run -p 宿主机端口1:容器端口1 -p 宿主机端口2:容器端口2 镜像名   #发布多个端口
```

可以通过[DockerHub](https://hub.docker.com/)或者DockerFile来确定不同容器发布的端口

### 练习

 后台运行nginx容器，让我们能通过宿主机的端口去测试容器中的nginx。

```shell
 docker run -d -p 80:80 nginx
```





![](https://i.postimg.cc/8csnzWG2/screenshot-42.png)

![](https://i.postimg.cc/QtfyDm2g/screenshot-41.png)



## -v 数据卷

### 解释

当需要把容器中的数据持久化保存或者宿主机和容器间的数据共享。

将宿主机目录或文件挂载到容器中，实现宿主机和容器之间的数据共享和持久化。



### 用法

```shell
docker run -v 宿主机目录:容器目录[:读写权限] 镜像名
```

- 读写权限

在使用 -v 选项时，可以添加 :ro 或 :rw 来指定容器对挂载的目录或文件是只读（read-only）还是可读写（read-write）。如果不指定默认是rw。

- 如何知道挂载位置

可以通过[DockerHub](https://hub.docker.com/)或者DockerFile来确定不同容器挂载的位置

### 练习

- 后台运行nginx容器，发布一个最简单的静态页面到Nginx，让我们能够通过浏览器去访问这个静态页面

```shell
docker run -d -p 81:80 -v /home/cp_test/index.html:/usr/share/nginx/html/index.html nginx
```



![](C:/Users/h%27j%27h/AppData/Roaming/Typora/typora-user-images/image-20241013183006181.png)



## -e 设置环境变量

### 解释

容器中某些变量不能直接写死，需要让使用者在创建容器的时候指定，这种情况镜像中一般是定义环境变量来使用。  例如mysql容器的root密码。 遇到这种镜像创建的容器我就可以使用-e来设置环境变量的值。

### 用法

```shell
docker run -e 变量名=变量值 镜像名
```



- 如何知道那些环境变量可以被设置

可以通过[DockerHub](https://hub.docker.com/)或者DockerFile。

### 练习

- 后台运行一个mysql5.7的容器,
- 要求容器中的mysql可以被外部连接
- mysql容器的数据需要持久化存储，不能因为容器被删除而丢失。
- mysql的root用户的密码设置为 1234

```shell
docker run -d -p 3306:3306 -v /home/mysql5.7_data/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 mysql:5.7
```

![](https://i.postimg.cc/7PQ1VW84/screenshot-43.png)

尝试删除容器在重新创建容器后看数据是否存在

```shell
docker rm -f 9cffce295b0f
```



删除连接后会显示连接错误

![](https://i.postimg.cc/qMPsf6vZ/screenshot-44.png)

重新创建容器

```shell
docker run -d -p 3306:3306 -v /home/mysql5.7_data/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 mysql:
5.7
```

可以正常显示

![](https://i.postimg.cc/76tzKfR9/screenshot-45.png)

## --name 容器命名

### 解释

需要帮助我们更快的识别出来容器的作用。之前的容器命名都是随机命名的。当在进行删除停止容器等一些操作可以直接使用容器名字而不是容器id

### 用法

```shell
docker run --name 需要定义的容器名 镜像名
```


### 练习

- 后台运行nginx容器

- 发布一个最简单的静态页面到Nginx，让我们能够通过浏览器去访问这个静态页面

- 容器命名为 nginx-test

```shell
docker run -d --name nginx-test -p 82:80 -v /home/cp_test/index.html:/usr/share/nginx/html/index.html nginx
```

![](https://i.postimg.cc/c4S3Xy9V/screenshot-46.png)

## --restart 容器退出后的重启策略

### 解释

保证容器退出后也可以自动重启

### 用法

```shell
docker run --restart 重启策略 镜像名
```

- 重启策略

  no：容器退出时不会自动重启。

  always：容器总是在退出后自动重启。

  on-failure[:max-retries]：容器仅在非正常退出时重启，可以指定最大重试次数。

  unless-stopped：容器会在退出后自动重启，除非手动停止了容器。

`默认策略为no`

### 练习

- 后台运行nginx容器
- 容器命名为 nginx-test 
- 发布一个最简单的静态页面到Nginx，让我们能够通过浏览器去访问这个静态页面

- 该容器在退出后可以自动重启

```shell
docker run -d --name nginx-test -p 83:80 -v /home/cp_test/index.html:/usr/share/nginx/html/index.html --restart unless-stopped nginx 
```

