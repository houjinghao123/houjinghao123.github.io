+++
title = 'Docker在Ubuntu上部署项目案例'
date = 2024-10-15T11:47:16+08:00

+++
# 使用三更的博客项目进行部署测试

## 安装Mysql

安装前明确需求

- 使用的镜像是mysql5.7

- 后台运行 -d

- 数据需要持久化存储 -v 使用别名mysql_data

- 开放3306端口 -p

- 设置root的密码 -e

- 停止后自动重启 --restart 

- 加入一个网络 --network 

- 容器命名为blog_mysql

  

```shell
docker run -d \
-v mysql_data:/var/lib/mysql \
-p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=PaiTcgDK0VUZCM \
--restart always \
--network blog_net \
--name blog_mysql \
mysql:5.7
```

##  安装Redis

安装前明确需求

- 版本 redis 7
- 后台运行
- 开启aof持久化
- 数据需要持久化存储 redis-server --appendonly yes
- 开放端口
- 停止后自动重启
- 容器命名为blog_reids
- 加入网络 blog_net

```shell
docker run -d \
-v reids_data:/data \
-p 6379:6379 \
--restart always \
--name blog_redis \
--network blog_net \
redis:7.0 redis-server --appendonly yes
```



##  安装OpenJDK 运行jar包

需求

- openjdk 8u111版本
- 后台运行 -d
- 端口为7777 -p
- 使用数据卷用于存放jar包 -v
- 退出后重新运行 --restart
- 加入blog_net 网络 --network
- 容器名字是 sb_blog --name

```shell
docker run \
-p 7777:7777 \
-d \
-v /usr/blog:/usr/blog \
--restart always \
--network blog_net \
--name sg_blog \
java:openjdk-8u111 java -jar /usr/blog/sangeng-blog-1.0-SNAPSHOT.jar 
```

使用上面的方式启动会报错，因为没有设置数据库和redis的连接方式

```shell
docker run \
-p 7777:7777 \
-d \
--network blog_net \
-v /usr/blog:/usr/blog \
--restart always \
--name sg_blog \
java:openjdk-8u111 java -jar /usr/blog/sangeng-blog-1.0-SNAPSHOT.jar \
"--spring.datasource.url=jdbc:mysql://blog_mysql:3306/sg_blog?characterEncoding=utf-8&serverTimezone=Asia/Shanghai" \
"--spring.datasource.username=root" \ 
"--spring.datasource.password=1234" \
"--spring.redis.host=blog_redis" \
```

##  使用nginx部署前端项目

分析需求

- 后台运行 -d
- 开放端口 -p
- 使用数据卷的方式同步静态资源 -v
- 停止后自动重启 --restart
- 容器命名sg_blog_vue

```shell
docker run -d \
-p 80:80 \
-v /usr/blog/sg-blog-vue/dist:/usr/share/nginx/html \
--restart always \
--name sg_blog_vue \
nginx:1.21.5
```





