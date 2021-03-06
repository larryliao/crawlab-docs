## Docker 安装部署

这应该是部署应用的最方便也是最节省时间的方式了。在最近的一次版本更新 [v0.4.0](https://github.com/tikazyq/crawlab/releases/tag/v0.4.0) 中，我们发布了Golang版本，并且支持Docker部署。下面将一步一步介绍如何使用Docker来部署Crawlab。

对Docker不了解的开发者，可以参考一下这篇文章（[9102 年了，学点 Docker 知识](https://juejin.im/post/5c2c69cee51d450d9707236e)）做进一步了解。如果已经了解 Docker 的开发者，可以跳过第 1 节，如果对 Docker 和 Docker-Compose 了解的开发者，可以跳过 1、2 两节，直接阅读 “安装并启动 Crawlab“ 小节。

**推荐人群**: 

- 想要快速体验 Crawlab 的开发者
- 对 `Docker` 比较了解或者愿意学习 Docker 的开发者

**推荐配置**:

- Docker: 18.03+
- Docker-Compose: 1.24+

### 1. 安装 Docker

Docker 的安装其实不复杂，英语不错的请参照 [官方文档](https://docs.docker.com/) 进行安装。下面将介绍两种安装方式。

#### 1.1 Windows & Mac

Windows 和 Mac 的用户可以下载 Docker Desktop 来完成 Docker 安装。

下载地址: https://www.docker.com/products/docker-desktop

点击下图的按钮，按照官网步骤，完成下载安装。

![](http://static-docs.crawlab.cn/get-docker-desktop.png)

#### Linux

对于 Linux 用户，请参照以下表格的链接来安装 Docker。

| 操作系统          | 文档                                                     |
| :---------------- | :------------------------------------------------------- |
| Ubuntu            | https://docs.docker.com/install/linux/docker-ce/ubuntu   |
| Debian            | https://docs.docker.com/install/linux/docker-ce/debian   |
| CentOS            | https://docs.docker.com/install/linux/docker-ce/centos   |
| Fedora            | https://docs.docker.com/install/linux/docker-ce/fedora   |
| 其他 Linux 发行版 | https://docs.docker.com/install/linux/docker-ce/binaries |

### 1.2 下载镜像

我们已经在 [DockerHub](https://hub.docker.com/r/tikazyq/crawlab) 上构建了Crawlab的镜像，开发者只需要将其 pull 下来使用。在 pull 镜像之前，我们需要配置一下镜像源。因为我们在墙内，使用原有的镜像源速度非常感人，因此将使用 DockerHub 在国内的加速器。如果是 Mac 或者 Linux 用户，创建 `/etc/docker/daemon.json` 文件，在其中输入如下内容。

```json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

这样的话，pull 镜像的速度会比不改变镜像源的速度快很多。

执行以下命令将 Crawlab 的镜像下载下来。镜像大小大概在几百兆，因此下载需要几分钟时间。

```bash
docker pull tikazyq/crawlab:latest
```

#### 1.3 Docker 常用命令

请查看这篇文章来查看 Docker 的常用命令。

https://blog.csdn.net/u013378306/article/details/86668313

### 2. 安装 Docker-Compose

为了方便起见，我们用`docker-compose`的方式来部署。`docker-compose`是一个集群管理方式，可以利用名为`docker-compose.yml`的`yaml`文件来定义需要启动的容器，可以是单个，也可以（通常）是多个的。

安装 `docker-compose` 其实比较简单，在安装了 `pip` 的情况下（Python 3），执行以下命令。

```bash
pip install docker-compose
```

安装好 `docker-compose` 后，请运行 `docker-compose ps` 来测试是否安装正常。正常的应该是显示如下内容。

```bash
Name   Command   State   Ports
------------------------------
--------------------------------
```

这是没有 Docker 容器在运行的情况，也就是空列表。如果有容器在运行，可以看到其对应的信息。

### 3. 安装并启动 Crawlab

Crawlab的`docker-compose.yml`定义如下。

```yaml
version: '3.3'
services:
  master: 
    image: tikazyq/crawlab:latest
    container_name: master
    environment:
      # CRAWLAB_API_ADDRESS: "https://<your_api_ip>:<your_api_port>"  # backend API address 后端 API 地址. 适用于 https 或者源码部署
      CRAWLAB_SERVER_MASTER: "Y"  # whether to be master node 是否为主节点，主节点为 Y，工作节点为 N
      CRAWLAB_MONGO_HOST: "mongo"  # MongoDB host address MongoDB 的地址，在 docker compose 网络中，直接引用服务名称
      # CRAWLAB_MONGO_PORT: "27017"  # MongoDB port MongoDB 的端口
      # CRAWLAB_MONGO_DB: "crawlab_test"  # MongoDB database MongoDB 的数据库
      # CRAWLAB_MONGO_USERNAME: "username"  # MongoDB username MongoDB 的用户名
      # CRAWLAB_MONGO_PASSWORD: "password"  # MongoDB password MongoDB 的密码
      # CRAWLAB_MONGO_AUTHSOURCE: "admin"  # MongoDB auth source MongoDB 的验证源
      CRAWLAB_REDIS_ADDRESS: "redis"  # Redis host address Redis 的地址，在 docker compose 网络中，直接引用服务名称
      # CRAWLAB_REDIS_PORT: "6379"  # Redis port Redis 的端口
      # CRAWLAB_REDIS_DATABASE: "1"  # Redis database Redis 的数据库
      # CRAWLAB_REDIS_PASSWORD: "password"  # Redis password Redis 的密码
      # CRAWLAB_LOG_LEVEL: "info"  # log level 日志级别. 默认为 info
      # CRAWLAB_LOG_ISDELETEPERIODICALLY: "N"  # whether to periodically delete log files 是否周期性删除日志文件. 默认不删除
      # CRAWLAB_LOG_DELETEFREQUENCY: "@hourly"  # frequency of deleting log files 删除日志文件的频率. 默认为每小时
      # CRAWLAB_SERVER_REGISTER_TYPE: "mac"  # node register type 节点注册方式. 默认为 mac 地址，也可设置为 ip（防止 mac 地址冲突）
      # CRAWLAB_SERVER_REGISTER_IP: "127.0.0.1"  # node register ip 节点注册IP. 节点唯一识别号，只有当 CRAWLAB_SERVER_REGISTER_TYPE 为 "ip" 时才生效
      # CRAWLAB_TASK_WORKERS: 8  # number of task executors 任务执行器个数（并行执行任务数）
      # CRAWLAB_RPC_WORKERS: 16  # number of RPC workers RPC 工作协程个数
      # CRAWLAB_SERVER_LANG_NODE: "Y"  # whether to pre-install Node.js 预安装 Node.js 语言环境
      # CRAWLAB_SERVER_LANG_JAVA: "Y"  # whether to pre-install Java 预安装 Java 语言环境
      # CRAWLAB_SETTING_ALLOWREGISTER: "N"  # whether to allow user registration 是否允许用户注册
      # CRAWLAB_SETTING_ENABLETUTORIAL: "N"  # whether to enable tutorial 是否启用教程
      # CRAWLAB_NOTIFICATION_MAIL_SERVER: smtp.exmaple.com  # STMP server address STMP 服务器地址
      # CRAWLAB_NOTIFICATION_MAIL_PORT: 465  # STMP server port STMP 服务器端口
      # CRAWLAB_NOTIFICATION_MAIL_SENDEREMAIL: admin@exmaple.com  # sender email 发送者邮箱
      # CRAWLAB_NOTIFICATION_MAIL_SENDERIDENTITY: admin@exmaple.com  # sender ID 发送者 ID
      # CRAWLAB_NOTIFICATION_MAIL_SMTP_USER: username  # SMTP username SMTP 用户名
      # CRAWLAB_NOTIFICATION_MAIL_SMTP_PASSWORD: password  # SMTP password SMTP 密码
    ports:    
      - "8080:8080" # frontend port mapping 前端端口映射
    depends_on:
      - mongo
      - redis
    # volumes:
    #   - "/var/crawlab/log:/var/logs/crawlab" # log persistent 日志持久化
  worker:
    image: tikazyq/crawlab:latest
    container_name: worker
    environment:
      CRAWLAB_SERVER_MASTER: "N"
      CRAWLAB_MONGO_HOST: "mongo"
      CRAWLAB_REDIS_ADDRESS: "redis"
    depends_on:
      - mongo
      - redis
    # environment:
    #   MONGO_INITDB_ROOT_USERNAME: username
    #   MONGO_INITDB_ROOT_PASSWORD: password
    # volumes:
    #   - "/var/crawlab/log:/var/logs/crawlab" # log persistent 日志持久化
  mongo:
    image: mongo:latest
    restart: always
    # volumes:
    #   - "/opt/crawlab/mongo/data/db:/data/db"  # make data persistent 持久化
    # ports:
    #   - "27017:27017"  # expose port to host machine 暴露接口到宿主机
  redis:
    image: redis:latest
    restart: always
    # command: redis-server --requirepass "password" # set redis password 设置 Redis 密码
    # volumes:
    #   - "/opt/crawlab/redis/data:/data"  # make data persistent 持久化
    # ports:
    #   - "6379:6379"  # expose port to host machine 暴露接口到宿主机
  # splash:  # use Splash to run spiders on dynamic pages
  #   image: scrapinghub/splash
  #   container_name: splash
  #   ports:
  #     - "8050:8050"
```

这里先定义了 `master` 节点和 `worker` 节点，也就是Crawlab的主节点和工作节点。`master` 和 `worker` 依赖于 `mongo` 和 `redis` 容器，因此在启动之前会同时启动 `mongo` 和 `redis` 容器。这样就不需要单独配置 `mongo` 和`redis` 服务了，大大节省了环境配置的时间。

其中，我们设置了Redis和MongoDB的地址，分别通过 `CRAWLAB_REDIS_ADDRESS` 和 `CRAWLAB_MONGO_HOST` 参数。`CRAWLAB_SERVER_MASTER` 设置为`Y`表示启动的是主节点（该参数默认是为`N`，表示为工作节点）。`CRAWLAB_API_ADDRESS` 是前端的API地址，请将这个设置为公网能访问到主节点的地址，`8000`是API端口。环境变量配置详情请见 [配置章节](../Config/)，您可以根据自己的要求来进行配置。

⚠️**注意**: 在生产环境中，强烈建议您将数据库持久化，因为否则的话，一旦您的 Docker 容器发生意外导致关闭重启，您的数据将丢失。持久化的方法就是将上述 `docker-compose.yml` 模版中的关于持久化的代码取消注释就可以了。持久化的数据包括：MongoDB 数据库、Redis 数据库、日志。

安装完 `docker-compose` 和定义好 `docker-compose.yml` 后，只需要运行以下命令就可以启动Crawlab。

```bash
docker-compose up -d
```

同样，在浏览器中输入 `http://localhost:8080` 就可以看到界面。

### 4. 更新/重启 Crawlab

当 Crawlab 有更新时，我们会将新的变更构建更新到新的镜像中。最新的镜像名称都是 `tikazyq/crawlab:latest`。而一个指定版本号的镜像名称为 `tikazyq/crawlab:<version>`，例如 `tikazyq/crawlab:0.4.7` 为 v0.4.7 版本对应的镜像。

如果您需要更新最新的版本的镜像，只需要执行以下代码。

```bash
# 关闭并删除 Docker 容器
docker-compose down

# 拉取最新镜像
docker pull tikazyq/crawlab:latest

# 启动 Docker 容器
docker-compose up -d
```

### 5. 下一步

请参考 [爬虫章节](../Spider/README.md) 来详细了解如何使用 Crawlab。

