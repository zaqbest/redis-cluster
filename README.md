# Redis Cluster

该目录提供一个本地/开发环境使用的 Redis Cluster：6 个节点，3 个主节点和 3 个从节点。

## 启动

先确认外部网络存在（其他 `apps` 服务也使用该网络）：

```bash
docker network create docker-kit-network 2>/dev/null || true
docker compose up -d
```

检查集群状态：

```bash
docker compose exec redis-node-1 redis-cli -a 123456 cluster info
docker compose exec redis-node-1 redis-cli -a 123456 cluster nodes
```

## 端口

| 节点 | Redis 端口 | Cluster Bus 端口 | 角色 |
| --- | ---: | ---: | --- |
| redis-node-1 | 7001 | 17001 | 主 |
| redis-node-2 | 7002 | 17002 | 主 |
| redis-node-3 | 7003 | 17003 | 主 |
| redis-node-4 | 7004 | 17004 | 从 |
| redis-node-5 | 7005 | 17005 | 从 |
| redis-node-6 | 7006 | 17006 | 从 |

容器之间通过 `docker-kit-network` 使用 `redis-node-N:6379` 通信。应用如果运行在同一个 Docker 网络中，建议使用这些容器服务名连接；宿主机客户端可使用 `localhost:7001` 到 `localhost:7006`，并且必须使用支持 Redis Cluster 重定向的客户端。

## 停止和清理

```bash
docker compose down
docker compose down -v  # 同时删除 6 个节点的数据卷
```

默认密码沿用 `redis/redis.conf` 的 `123456`，生产环境请修改 `redis.conf` 中的 `requirepass` 和 `masterauth`。