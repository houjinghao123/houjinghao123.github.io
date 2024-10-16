+++
title = 'DockerFile基本使用'
date = 2024-10-16T13:53:02+08:00
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













