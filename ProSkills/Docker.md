# Image常用命令

## docker search

docker search [option] keyword

## docker pull

docker pull [image_name]:[tag]

## docker查看镜像信息

docker images

docker image list

docker inspect [image-name]:[tag] : 查看image的详细信息

## docker镜像的导出和导入

docker save: 导出镜像，以 tar 包的形式存在

docker save -o redis.tar redis:latest

docker load: 导入镜像

docker load -i redis.tar

## docker删除镜像

docker rmi [image-name]

-f, --force: 强制删除镜像

-no-prune: 不要删除未带标签的父镜像

当有多个同名镜像存在时，想要删除指定镜像，要带上tag。

docker rmi [image-id]

docker image prune: 清理镜像

## 上传镜像

`````
docker login
docker tag python:3 wusuowei/python:3
docker push wusuowei/python:3
`````

# Container 常用命令

Docker启动容器有两种方式：

- 基于镜像新建一个容器
- 将处于终止状态（exited）的容器重新启动

## 启动

### 创建一个新容器

docker run IMAGE:TAG

`````
# 启动ubuntu容器并且通过终端输出 Hello World
docker run ubuntu:latest /bin/echo 'Hello World'
`````

上面的命令运行容器，并且在输出 Hello World之后就会终止运行。如果想以交互的方式运行容器，用如下命令：

`````
docker run -t -i ubuntu:latest /bin/bash
`````

-t: 让Docker分配一个伪终端（pseudo-tty）并且绑定到容器的标准输入上。

-i: 让容器的标准输入保持打开。



**执行 docker run 后，Docker 都干了些啥？**

1. 检查本地是否存在指定的镜像，不存在就从registry下载。
2. 利用镜像创建并启动一个容器。
3. 分配一个文件系统，并且在只读的镜像层外面挂载一层可读写层。
4. 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去。
5. 从地址池中分配一个ip地址给容器。
6. 执行用户指定的应用程序。
7. 执行完毕后容器被终止。



在实际应用中，需要让容器以后台的方式运行，以此保证一个稳定的服务。

添加 -d ，可以让容易在后台运行：

`````
docker run -d ubuntu:latest /bin/sh -c "while true; do echo hello world; sleep 1; done"
`````

执行成功后，会返回一个容器ID。

后台运行的容器可以通过以下命令查询：

```shell
docker container logs [container ID or Names]
```

### 启动终止状态的容器

docker container start [container ID or Name]

## 查看容器

docker ps: 查看正在运行的容器

docker ps -a: 查看所有容器

docker ps -l: 查看最新创建的容器

docker ps -n=2: 查看最新创建的两个容器

## 进入容器

想要进入到已经运行的容器，有以下两种命令：

- docker exec
- docker attach

exec 和 attach的区别在于：在exec中，使用exit退出容器时，不会导致容器停止。

### docker exec

```shell
docker exec -it [container ID or Name]
-----------------------------------------------
docker exec -it redis /bin/bash
-----------------------------------------------
exit
```

## 关闭容器

关闭容器有以下两种方式：

### stop

docker stop [container ID or Name]

### kill

强制关闭：

docker kill [container ID or Names]



对于交互式的容器，可以通过输入 exit 或者 Ctrl+d 来退出。

## 重启容器

docker restart -t=5 [container ID or Names]

```shell
# 限时5s关闭redis容器，然后重启
docker restart -t=5 redis
```

## 导入导出容器

docker export: 导出

```shell
docker export [container ID] > container name
----------------------------------------------------
docker export 9e8dfwed432324 > redis.tar
```

docker import: 导入

```shell
cat redis.tar | docker import - test/reids:1.0
----------------------------------------------------
docker import http://example.com/exampleimage.tgz example/imagerepo
```

均是以tar包形式。

## 删除容器

docker rm [container ID or Names]

-f, --force: 强制删除

删除所有已经停止运行的容器：docker container prune

# 数据管理

## 数据卷

​	是一个可以让多个容器使用的特殊目录，用于持久化数据以及共享容器间的数据，它以正常的文件或目录的形式存在于宿主机器上。而且，其生命周期独立于容器的生命周期，即删除容器时，数据卷不会删除。

### volume

它是Docker管理宿主机文件系统的一部分，默认保存于/var/lib/docker/volumes，其默认名为一串很长的ID。

![Docker 查看本地数据卷](https://raw.githubusercontent.com/ericxiao417/Pics/main/202306212120630.jpeg)

```shell
# 创建数据卷
docker volume create test-vol
# 查看所有的volume
docker volume ls
# 查看指定卷信息
docker volume inspect test-vol
# 删除数据卷
docker volume rm test-vol
# 统一删除数据卷
docker volume prune
```

### 运行容器时挂载数据卷

#### 1. -v

`````
docker run -d -it --name=test-nginx -p 8011:80 -v test-vol:/usr/share/nginx/html nginx:1.13.12
`````

-d: 后台运行容器

--name=test-ngin: 指定容器名为test-nginx

-p 8011:80 : 将容器的80端口挂载到宿主机的8011端口

-v test-vol:/usr/share/nginx/html : 将 test-vol 数据卷挂载到容器中的/usr/share/nginx/html 目录下

#### 2. --mount

```
docker run -d -it --name=test-nginx -p 8011:80 --mount source=test-vol, target=/usr/bin/share/nginx/html nginx:1.13.12
```

#### 3.区别

使用-v时，如果宿主机上没有指定文件不会报错，会自动创建指定文件。

当使用--mount时，如果宿主机上没有指定文件，不会自动创建文件。



容器运行成功后，进入到/var/lib/docker/volumes 目录下，验证数据卷是否挂载成功。挂载成功后，无论是修改/var/lib/docer/volumes下的数据，还是进入到容器中修改/usr/bin/share/nginx/html的数据，都会同步修改对应的挂载目录，类似前端开发中的双向绑定。

# Dockfile



