+++
title = 'DockerFile和DockerCompose基本使用'
date = 2024-10-16T13:53:02+08:00
categories = ["Docker"]
tags = ["Docker"]
+++

# DockerFile的基本使用

## 为什么要使用

之前的方式去部署应用还是有点繁琐，因为之前使用的镜像并不能很好的符合我们自己的需求。

使用dockerFile可以自定义镜像

## 快速入门

**构建一个最简单的HelloWorld镜像**

- 创建一个文件

```shell
sudo mkdir images
```

- 在文件中编写相关指令

```
FROM ubuntu:22.04
CMD ["echo","helloworld"]
```



- 编译镜像

```shell
docker build -t hello:1.0 -f HelloWorld .
```



- 容器运行测试

```shell
docker run hello:1.0
```

## 指令学习

### FROM

- 作用

  定义基础镜像

- 用法

  FROM 镜像名:标签名

  FROM ubuntu：22.04

- 作用时机

  构建镜像的时候

### CMD

- 作用

  用来定义容器运行时的默认命令。可以在使用docker run的时候覆盖掉CMD中定义的命令

- 用法

  CMD ["命令1"，“参数1”,"参数2"]

  CMD echo $HOME

  ![](https://i.postimg.cc/hGVyPHxk/screenshot-48.png)

- 注意

  一个DockerFile中写多个CMD的时候只有最后一个CMD会去作用

### ENV

- 作用

  用来定义环境变量

- 用法

  ENV 变量名="变量值"

- 作用时机

  构建镜像的时候

- 脚本

  ```shell
  #第一种使用参数的方法
  FROM ubuntu:22.04
  ENV CONTENT hello
  CMD ["sh","-c","echo $CONTENT"]
  #第二种使用参数的方法
  FROM ubuntu:22.04
  ENV CONTENT="helloword"
  CMD echo $CONTENT
  ```



### RUN

- 作用

  它是用来定义构建过程中要执行的命令的

- 用法

  RUN 命令

  例如：RUN echo sg

- 作用时机

  构建镜像的时候



### WORKDIR

- 作用

  用于设置当前工作的目录，如果该目录不存在会自动创建。

- 用法

  WORKDIR 目录

  WORKDIR /root/app

- 作用时机

  构建镜像的时候

案例：

定义一个CONTENT变量，默认值为hellodocker，在镜像的/app目录下创建一个sg目录，在其中创建一个content.txt文件，文件的内容为CONTENT变量的值。容器启动时打印content.txt的内容

- 在Linux环境下执行这些命令

   ```shell
   export CONTENT=hellodocker
   mkdir /app
   cd /app
   mkdir sg
   cd sg
   echo $CONTINT>content.txt
   cat content.txt
   ```

- 使用dockerfile构建响应的容器

```dockerfile
FROM ubuntu:22.04
ENV CONTINT="hellodocker"
WORKDIR /app/sg
RUN echo $CONTINT>content.txt
CMD ["cat","content.txt"]
```

- 构建

```shell
docker build -t test:2.0 -f Test1 .
```

- 运行

```shell
docekr run test:2.0
```

### ADD

- 作用

  把构建上下文中的文件或者网络文件添加到镜像中，如果文件是一个压缩包会自动解压，如果是网络中的文件并不会自动解压

- 用法

  ADD 原路径 目标路径

  ADD sg-blog-vue.tar.gz .

- 作用时机

  构建镜像的时候

### EXPOSE

- 作用

  暴露需要发布的端口，让镜像使用者知道应该发布哪些端口

- 用法

  EXPOSE 端口号1 端口号2 ....

  EXPOSE 80 8080

- 作用时机

  构建镜像的时候

### COPY

- 作用

  从构建上下文中复制内容到镜像中

- 用法

  COPY 原路径 目标路径

  COPY sg-blog-vue.tar.gz .

### ENTRYPOINT

- 作用

  用来定义容器运行时的默认命令。docker run的时候无法覆盖ENTRYPOINT里的内容。

- 作用时机

  运行容器的时候

- 用法

  ENTRYPOINT ["命令1","参数1","参数2"]

  ENTRYPOINT ["echo","helloworld"]

- 解释

  与CMD结合，把不希望被覆盖的命令用ENTRYPOINNT定义，其他部分用CMD定义，这样启动容器的时候只会覆盖CMD的部分

- 例

  ENTRYPOINT ["java","-jar"] CMD ["app.jar"]

  上述写法的app.jar就是可以被覆盖的，而java -jar就是不能被覆盖

## 上传镜像

- 注册账号

  上传镜像需要有dockerhub的账号

- 本地登录验证

  docker login

  然后输入用户名和密码

- 给镜像打标签

  docker tag username/镜像名:tag   #username是DockerHub的用户名

- 推送镜像

  docker push username/镜像名:tag

# DockerCompose的基本使用

## 概述

- 为什么要学习

我们在部署运行一个项目的时候可能需要运行多个容器。 还要去处理这些容器的网络，数据卷等。  如果用docker命令一个个去处理还是不方便。 DockerCompose就是去解决这个问题的。

DockerCompose是用来定义和运行一个或多个容器(通常都是多个)运行和应用的工具

可以使用 YAML 文件来配置应用程序的服务。然后，使用单个命令，您可以根据配置创建并启动所有服务。

- 检查是否安装DockerCompose

使用docker compose version 可以查看版本

![](https://i.postimg.cc/26JJFNKw/screenshot-49.png)

## 快速入门

- 编写docker-compose.yaml模板文件
- 运行 docker compose up -d
- 停止 docker compose down

默认运行的文件时docker-compose，当使用docker compose up时会自动寻找docker-compose.yaml这个文件，如果更改文件名需要添加新的参数进行指定文件位置

```shell
docker-compose -f docker-compose.override.yml up
```



## 模板文件中的元素

### command

- 覆盖容器启动后的默认指令

### environment

- 指定环境变量，相当于run的-e选项

### image

- 用来指定镜像

### networks

- 指定网络，相当于run的--network

### ports

- 用来指定要发布的端口，相当于run的-p

### volumes

- 用来指定数据卷，相当于-v

### restart

- 用来指定重启策略，相当于--restart

案例

在之前自定义镜像版本的基础上，用DockerCompose来部署

```yaml
services:
  # mysql
  blog_mysql:
    image: mysql:5.7
    volumes:
      - mysql_data:/var/lib/mysql
    ports:
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: *******
    restart: always
    networks:
      - blog_net
  # redis
  blog_redis:
    image: redis:7.0
    volumes:
      - redis_data:/data
    ports:
      - 6379:6379
    restart: always
    command: ['redis-server','--appendonly','yes']
    networks:
      - blog_net
  # 后端服务
  sg_blog:
    image: sg_blog:01
    ports:
      - 7777:7777
    networks:
      - blog_net
    restart: always
  # 前端服务
  sg_blog_vue:
    image: sg_blog_vue:01
    ports:
      - 80:80
    restart: always

networks:
  blog_net:

volumes:
  mysql_data:
    #使用已经存在的数据卷
    external: true
  redis_data:
    external: true
```

![](https://i.postimg.cc/pV9Wz26g/screenshot-50.png)
