# Docker 指南

Docker Compose 是运行 FreeLLMAPI 个人使用的推荐方式。容器在端口 3001 上通过单个进程提供 Express API 和已构建的 React 仪表盘，SQLite 数据持久化存储在命名卷中。

## 前置条件

- Docker
- Docker Compose
- 用于生成 `ENCRYPTION_KEY` 的 OpenSSL

## 快速开始

创建包含 32 字节加密密钥的 `.env` 文件：

```bash
ENCRYPTION_KEY="$(openssl rand -hex 32)"
printf "ENCRYPTION_KEY=%s\nPORT=3001\n" "$ENCRYPTION_KEY" > .env
```

启动应用：

```bash
docker compose up -d
```

打开 http://localhost:3001，在 **密钥** 页面添加供应商密钥，然后使用生成的 `freellmapi-...` 密钥配合任何兼容 OpenAI 的客户端使用。

## API 调用示例

```bash
curl http://localhost:3001/v1/chat/completions \
  -H "Authorization: Bearer freellmapi-your-unified-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "auto",
    "messages": [{"role": "user", "content": "Say hello from FreeLLMAPI."}]
  }'
```

## 运维操作

查看状态：

```bash
docker compose ps
```

查看日志：

```bash
docker compose logs -f freellmapi
```

停止应用：

```bash
docker compose down
```

版本发布后更新到最新的 GHCR 镜像：

```bash
docker compose pull
docker compose up -d
```

从源码本地重新构建：

```bash
docker compose up -d --build
```

## 配置说明

| 变量 | 是否必需 | 默认值 | 描述 |
| --- | --- | --- | --- |
| `ENCRYPTION_KEY` | 是 | 无 | 用于静态加密供应商 API 密钥的 64 字符十六进制密钥。生成一次后请保持稳定。 |
| `PORT` | 否 | `3001` | Docker Compose 暴露的主机端口。容器监听端口 3001。 |

`freellmapi-data` 卷在 `/app/server/data` 路径下存储 SQLite 数据。升级时请保持相同的卷和 `ENCRYPTION_KEY`，否则已有的加密供应商密钥将无法解密。

## 发布的镜像

镜像发布到 GitHub Container Registry：

```bash
docker pull ghcr.io/tashfeenahmed/freellmapi:latest
```

Docker 工作流会构建 Pull Request 但不推送镜像。当本仓库收到 `main` 分支的工作流后，推送到 `main` 和版本标签会自动发布镜像到 GHCR。
