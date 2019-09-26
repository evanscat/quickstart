### 下载可执行文件(Download etcd binary file)

[https://github.com/etcd-io/etcd/releases](https://github.com/etcd-io/etcd/releases)

e.g.

```shell
ETCD_VER=v3.4.1
GOOGLE_URL=https://storage.googleapis.com/etcd
GITHUB_URL=https://github.com/etcd-io/etcd/releases/download
DOWNLOAD_URL=${GITHUB_URL}
rm -f ./etcd-${ETCD_VER}-linux-amd64.tar.gz
rm -rf etcd-download && mkdir etcd-download
curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o ./etcd-${ETCD_VER}-linux-amd64.tar.gz
tar xzvf ./etcd-${ETCD_VER}-linux-amd64.tar.gz -C ./etcd-download/ --strip-components=1
mv ./etcd-${ETCD_VER}-linux-amd64.tar.gz ./etcd-download/
```

### 构建镜像(Build image)

*使用alpine作为基础镜像，对于etcd一些功能可能需要额外安装其他组件*

dockefile

```dockerfile
FROM alpine
WORKDIR /app/
COPY ./etcd-download/etcd /app/
ENTRYPOINT ["./etcd"]
```

从docker命令构建（ use docker command for building）

```shell
docker build -t etcd:latest .
```

### 创建并运行容器（Create and run container）

```
name          节点名称
data-dir      指定节点的数据存储目录
listen-peer-urls      监听URL，用于与其他节点通讯
listen-client-urls    对外提供服务的地址：比如 http://ip:2379,http://127.0.0.1:2379 ，客户端会连接到这里和 etcd 交互
initial-advertise-peer-urls   该节点同伴监听地址，这个值会告诉集群中其他节点
initial-cluster   集群中所有节点的信息，格式为 node1=http://ip1:2380,node2=http://ip2:2380,… 。注意：这里的 node1 是节点的 --name 指定的名字；后面的 ip1:2380 是 --initial-advertise-peer-urls 指定的值
initial-cluster-state     新建集群的时候，这个值为 new ；假如已经存在的集群，这个值为 existing
initial-cluster-token     创建集群的 token，这个值每个集群保持唯一。这样的话，如果你要重新创建集群，即使配置和之前一样，也会再次生成新的集群和节点 uuid；否则会导致多个集群之间的冲突，造成未知的错误
advertise-client-urls     对外公告的该节点客户端监听地址，这个值会告诉集群中其他节点
```

```shell
etcd --name ${NAME} --data-dir ${DATADIR}  \
    --listen-client-urls http://0.0.0.0:${PORT}  \
    --advertise-client-urls ${MYHOST}:${PORT} \
    --listen-peer-urls ${MYHOST}:${CLUSTER_PORT} \
    --initial-advertise-peer-urls ${MYHOST}:${CLUSTER_PORT} \
    --initial-cluster ${YOUR_CLUSTETR}
```

**只运行单机版，参考以下命令**

```shell
docker run -itd  -p 2380:2380 -p 2379:2379 etcd:latest \
    --listen-client-urls http://0.0.0.0:2379  \
    --advertise-client-urls http://192.168.1.77:2379 \
```

###使用docker-compose构建并运行(build & run using docker-compose )

```yaml
version: "3.5"
services:
  etcd:
    image: "etcd:latest"
    build:
      context: ./
      dockerfile: Dockerfile
    container_name: etcd
    ports:
      - "2379:2379"
      - "2380:2380"
    restart: always
    command: --listen-client-urls http://0.0.0.0:2379 --advertise-client-urls http://192.168.1.77:2379
```

```shell
docker-compose build #build image
docker-compose up -d #create and run container
```

