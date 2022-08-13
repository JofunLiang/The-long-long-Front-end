# 使用Docker和Nginx部署静态网站项目

[Docker](https://github.com/docker/docker-ce) 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。[Nginx](https://github.com/nginx/nginx) 是一个高性能的 HTTP 和反向代理服务器。两者的安装和使用方法本文就不再赘述了，想了解更多请移步官方文档。

## 配置 Nginx

在项目根目录下创建 nginx 文件夹，该文件夹下新建 nginx.conf 配置文件，配置如下：
```nginx
user  root;
worker_processes  1;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    server_tokens  off; #屏蔽nginx版本号

    sendfile        on;

    keepalive_timeout  65;

    server {
        listen       80 default_server;
        server_name  localhost;

        location / {   #生产环境，镜像内的访问路径
            root   /app; #镜像外的工作路径
            index  index.html index.htm;
        }
    }
}
```
该配置文件定义了首页的指向为 /app/index.html，所以一会需要把构建出来的 dist 目录下的资源文件复制到 app 目录中。

## 配置 Dockerfile

静态项目只需要 dist 目录下的内容就可以了，所以，要创建一个 .dockerignore 文件限制（排除） copy 到 docker 容器内的文件，这样一些和应用运行无关的文件就不会被复制到容器内。这里使用 .dockerignore 排除 dist 以外的文件内容，在项目根目录新建 .dockerignore 配置文件，配置如下：
```dockerignore
.git
node_modules
src
public
```
> 想了解更多 .dockerignore 配置请移步 [.dockerignore file](https://docs.docker.com/engine/reference/builder/#dockerignore-file)。

在项目根目录下创建 Dockerfile 文件，配置如下：
```Dockerfile
FROM nginx
WORKDIR /app/
COPY dist/ /app
COPY nginx/nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
```
* FROM：定制的镜像都是基于 FROM 的镜像，这里的 nginx 就是定制需要的基础镜像。后续的操作都是基于 nginx。默认使用最新版本的 nginx，可以设置版本，如：FROM nginx:1.3.2。
* WORKDIR：指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。
* EXPOSE：暴露端口，在运行时使用随机端口映射时，也就是 docker run -P 时，会自动随机映射 EXPOSE 的端口。

> 想了解更多 Dockerfile 配置请移步 [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)。

## 构建镜像

在 Dockerfile 文件的存放目录下，执行以下命令构建镜像：
```shell
docker build -t nginx-demo .
```
* nginx-demo：构建的镜像的名称。
* .：上下文路径，是指 docker 在构建镜像，有时候想要使用到本机的文件（比如复制），docker build 命令得知这个路径后，会将路径下的所有内容打包。如果未说明最后一个参数，那么默认上下文路径就是 Dockerfile 所在的位置。

完成后，使用以下命令查看镜像列表：
```shell
docker image ls
```
结果如下：
```shell
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
nginx-demo   latest    6769c4cb3c92   31 seconds ago   142MB
```
可以看到刚刚构建的 nginx-demo 镜像出现在列表中，则镜像构建成功。

## 运行容器

接下来，使用 nginx-demo 镜像启动一个容器：
```shell
docker run -p 3000:80 -d --name my-app nginx-demo
```
* docker run：基于镜像启动一个容器
* -p 3008:80：端口映射，将宿主的3000端口映射到容器的80端口
* -d：后台方式运行
* --name：容器名，查看 Docker 进程
* nginx-demo：使用的镜像的名称

运行以下命令，查看运行的容器：
```shell
docker ps
```
结果如下：
```shell
CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS         PORTS                  NAMES
62b5dfadbe2d   nginx-demo   "/docker-entrypoint.…"   3 minutes ago   Up 3 minutes   0.0.0.0:3000->80/tcp   my-app
```
可以看到一个名为 my-app 的容器正在运行。在浏览器地址栏输入 localhost:3000 访问一下刚刚部署好的网站项目。

