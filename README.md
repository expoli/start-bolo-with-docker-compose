# 使用 docker-compose 一键启动 bolo 博客

本项目专注于使用 docker-compsoe 进行容器的编排，实现 bolo 博客的一键启动，以避免广大人民群众在进行 bolo 部署时会走不必要的弯路；降低了使用门槛，同时也大大增加了维护与迁移的便利性。

## 快速开始

### 服务器部署

默认 bolo 的访问域名为 localhost，如果您想直接在本地在本地试用，并通过 localhost 进行访问、那么无需修改任何文件、直接参考 [本地快速部署测试](#本地快速部署测试)，即可。

在进行服务器部署时，请根据需要修改 `docker-compose.yaml` 中的 `bolo` 服务中的 `command` 项。修改完成后根据 [本地快速部署测试](#本地快速部署测试)，进行后续步骤即可。

```yaml
  bolo:
    image: tangcuyu/bolo-solo:latest
    restart: always
    container_name: "bolo"
...
...
    networks:
      - bolo-net
    command: --listen_port=8080 --server_scheme=http --server_host=(修改为你博客的域名或ip)  --server_port=
```

**启动参数说明：**

- `--listen_port` ：进程监听端口
- `--server_scheme` ：最终访问协议，如果反代服务启用了 HTTPS 这里也需要改为 https
- `--server_host` ：最终访问域名或公网 IP，不要带端口
- `--server_port` ：最终访问端口，使用浏览器默认的 80 或者 443 的话值留空即可

详情请参考：[Solo 用户指南](https://hacpai.com/article/1492881378588)

### 本地快速部署测试

如果你只想体验一下那么可以根据下面的命令提示进行 bolo 的快速部署。

1. **克隆本项目至本地**

```shell
git clone https://github.com/expoli/start-bolo-with-docker-compose.git
```

1. **进入至项目跟路径**

```shell
cd start-bolo-with-docker-compose
```

3. **使用 docker-compose 启动 bolo**

```shell
# 后台启动
docker-compose up -d

# 前台方式启动可以看到日志输出、方便进行排错
docker-compose up
```

4. **更新容器**

```shell
docker-compose pull && docker-compose up -d
```

5. **删除容器与docker网络（但保留mysql数据库）**

```shell
docker-compose down
```

6. **完全删除**

如果你想完全卸载 bolo 只需要删除本项目文件夹即可、**因为mysql数据库文件挂载至了本项目的mysql自文件夹**，这种方式也防止因不熟悉docker-compse导致了数据的丢失。

```shell
sudo rm start-bolo-with-docker-compose -rf
```

## 项目介绍

### 文件结构

```shell
├── .gitignore
├── LICENSE
├── mysql # mysql 数据库
│   └── data
├── .mysql.env # mysql 容器环境变量配置文件
├── nginx
│   ├── conf.d # nginx 子配置文件目录、可添加自定义配置文件（以.conf结尾）
│   ├── nginx.conf
│   └── ssl
├── README.md
├── .bolo.env # bolo 容器环境变量配置文件
├── theme # 主题文件存放路径、如需挂载自定义主题、请在 docker-compose.yaml 中做好相应配置
│   └── solo-nexmoe
└── web
    └── markdowns # markdown 文件存放路径（使用markdown 文件初始化时bolo使用）详情参考 solo 导入markdown文件
```

### mysql 容器环境变量

下面是默认的配置、可根据个人需要进行更该、但需要确保与bolo容器的环境变量值一致、保证数据库连接的有效性。

```shell
# cat .mysql.env

MYSQL_ROOT_PASSWORD=expoli
MYSQL_USER=solo
MYSQL_DATABASE=solo
MYSQL_PASSWORD=solo123456
```

### bolo 容器环境变量

下面是默认的配置、可根据个人需要进行更该、但需要确保与bolo容器的环境变量值一致、保证数据库连接的有效性。

```shell
# cat .bolo.env

RUNTIME_DB=MYSQL
JDBC_USERNAME=solo
JDBC_PASSWORD=solo123456
JDBC_DRIVER=com.mysql.cj.jdbc.Driver
JDBC_URL=jdbc:mysql://mysql:3306/solo?useUnicode=yes&characterEncoding=UTF-8&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
```