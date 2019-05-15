#### docker：docker是靠虚拟机或类似的技术支撑的 。

- **目的**

  Docker项目的目标是实现**轻量级**的操作系统虚拟化解决方案，创建软件程序**可移植**的轻量容器。 

- **底层实现**

  Docker 的基础是Linux容器（LXC）等技术，在LXC的基础上Docker进行了进一步的封装，让用户不需要去关心容器的管理，使得操作更为简便 ，Docker使用Cgroups来提供容器隔离，而union文件系统用于保存镜像并使容器变得短暂 。

  - Cgroups：Cgroups是Linux内核功能，它让两件事情变成可能：限制Linux进程组的资源占用（内存、CPU）；为进程组制作 PID、UTS、IPC、网络、用户及装载命名空间。 
  - Union文件系统：在union文件系统里，文件系统可以被装载在其他文件系统之上，其结果就是一个分层的积累变化 ![img](https://upload-images.jianshu.io/upload_images/670122-276ee7fde089e2b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/987/format/webp)

- **与虚拟机的区别**

| 特性       | 容器               | 虚拟机     |
| ---------- | ------------------ | ---------- |
| 启动       | 秒级               | 分钟级     |
| 硬盘使用   | 一般为 MB          | 一般为 GB  |
| 性能       | 接近原生           | 弱于原生   |
| 系统支持量 | 单机支持上千个容器 | 一般几十个 |

-  **docekr的特性**
  - 交互式Shell：Docker可以分配一个虚拟终端并关联到任何容器的标准输入上，例如运行一个一次性交互shell
  - 文件系统隔离：每个进程容器运行在完全独立的根文件系统里
  - 写时复制：采用写时复制方式创建根文件系统，这让部署变得极其快捷，并且节省内存和硬盘空间
  - 资源隔离：可以使用cgroup为每个进程容器分配不同的系统资源
  - 网络隔离：每个进程容器运行在自己的网络命名空间里，拥有自己的虚拟接口和IP地址
  - 日志记录：Docker将会收集和记录每个进程容器的标准流（stdout/stderr/stdin），用于实时检索或批量检索
  - 变更管理：容器文件系统的变更可以提交到新的映像中，并可重复使用以创建更多的容器。无需使用模板或手动配置

**docker的常用命令**

- **搜索镜像**：可以在docker  hub里边检索可用镜像，然后pull下来

  ````
  docker search   镜像名字
  ````

-  **下载镜像**：拉取镜像

  ````
  docker pull  
  ````

- **查看镜像**

  ```
  docker  images
  ```

-  **删除镜像**：镜像不能有容器

  ```
  docker rmi  镜像ID/镜像名
  ```

- **创建/运行容器**：最后跟上镜像ID或镜像名称。

  ````
  docker run  
  ````

  -  **/bin/bash**：表示启动容器后启动bash。 

  - **-t:**在新容器内指定一个伪终端或终端。 

  - **-i:**允许你对容器内的标准输入 (STDIN) 进行交互。 

  - **-d** :容器可以以进程方式后台运行

  -  **-v**：建立数据卷。 将本地的目录映射到容器里的目录

    ````
    docker  run   -v  /usr/local/nginx/html:/usr/share/nginx/html  
    ````

  -  **-p**：端口映射。将本地的80端口映射到容器里的8080端口，也就是说，这里的80可以用8080来代替，不过8080是访问容器里的内容的。可以用来区别宿主机和容器

    ```
    docker run -p  8080:80
    ```

  - **--name**:用来给容器命名

  -  **-e**:可以使用-e 指定key/value进行传递环境变量进去 

-  **查看容器**

  ```	
  docker ps  容器ID/容器名         这是查看正在运行的容器
  ```

  ```
  docekr ps  容器ID/容器名  -a     这是查看全部容器
  ```

-  **停止容器**

  ```
  docker stop  容器ID/容器名   
  ```

-  **进入容器**

  ```
  docker exec -it 容器ID/容器名  /bin/bash
  ```

- **删除容器**：容器必须停止才可以删除

  ```
  docker rm  容器ID/容器名
  ```

-  **制作镜像**   （[build方法创建](https://yeasy.gitbooks.io/docker_practice/content/image/build.html)）

  -  **docker  commit **:将容器打包成镜像

    ```
    docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
    ```

    **-a :**提交的镜像作者 

    **-c :**使用Dockerfile指令来创建镜像； 

    **-m :**提交时的说明文字； 

  -  **建立Dockerfile**

    - 在一个空白目录中，建立一个文本文件 

    - 内容：

      ```
      FROM nginx
      RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
      ```

      - from 是以一个镜像为基础，在其上进行定制 。这个就是以nginx为基础镜像制作的。from 并且必须是第一条指令 

      - run 指令是用来执行命令行命令的 。格式两种

        - *shell* 格式 ：`RUN <命令>`，就像直接在命令行中输入的命令一样。刚才写的 Dockerfile 中的 `RUN` 指令就是这种格式 

          ```
          FROM debian:stretch
          
          RUN buildDeps='gcc libc6-dev make wget' \
              && apt-get update \
              && apt-get install -y $buildDeps \
              && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
              && mkdir -p /usr/src/redis \
              && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
              && make -C /usr/src/redis \
              && make -C /usr/src/redis install \
              && rm -rf /var/lib/apt/lists/* \
              && rm redis.tar.gz \
              && rm -r /usr/src/redis \
              && apt-get purge -y --auto-remove $buildDeps
          ```

        - *exec* 格式：`RUN ["可执行文件", "参数1", "参数2"]` 

  -  **docker  build** ：使用 Dockerfile 创建镜像 (在dockerfile所在目录)

    ```
    docker build -t runoob/ubuntu:v1 . 
    ```

    - docker build： 用 Dockerfile 构建镜像的命令关键词。
    - [OPTIONS] : 命令选项，常用的指令包括 -t 指定镜像的名字，
    - 创建一个镜像，名称是runoob/ubuntu，标签是 v1。 最后还有一个  .   不能忘记 。在linux中，小数点.代表着当前目录。所以docker build -t myimage .中小数点.其实就是将当前目录设置为上下文路径 
    - 创建完毕之后，可以用`docker images`查看

-  **镜像导入导出**

  - save  和  load  ：用来将一个或多个image打包保存的工具。 

  ```	
  docker save 镜像:tag > 本地目录 +命名         导出镜像到本地
  ```

  ```
  docker load < 本地目录     					从本地导入镜像
  ```

  - export  和  import

  ```
  docker export 容器ID > 本地包   导出容器到本地
  ```

  ```
  cat ubuntu.tar |  docker import - test/ubuntu:v1.0  	  从本地导入容器
  ```

  - ubuntu.tar：本地包名
  - test/ubuntu:v1.0：容器名和标签