# Docker 使用指南

本文档介绍如何使用 Docker 运行 ptool。

## 快速开始

### 拉取镜像

```bash
# 从 Docker Hub 拉取
docker pull jiongjiongJOJO/ptool:latest

# 从 GitHub Container Registry 拉取
docker pull ghcr.io/jiongjiongJOJO/ptool:latest
```

### 运行容器

```bash
docker run --rm \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  jiongjiongJOJO/ptool:latest --help
```

## 配置说明

### 挂载点

| 路径 | 说明 |
|------|------|
| `/home/ptool/.config/ptool` | ptool 配置目录 |

### 环境变量

| 变量 | 默认值 | 说明 |
|------|--------|------|
| `TZ` | `Asia/Shanghai` | 时区设置 |
| `HOME` | `/home/ptool` | 用户主目录 |

## 使用示例

### 查看版本

```bash
docker run --rm jiongjiongJOJO/ptool:latest version
```

### 查看状态

```bash
docker run --rm \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  jiongjiongJOJO/ptool:latest status
```

### 刷流

```bash
docker run --rm \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  jiongjiongJOJO/ptool:latest brush <clientName>
```

### 辅种

```bash
docker run --rm \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  jiongjiongJOJO/ptool:latest iyuu
```

## Docker Compose 配置

创建 `docker-compose.yml` 文件：

```yaml
version: '3.8'

services:
  ptool:
    image: jiongjiongJOJO/ptool:latest
    container_name: ptool
    restart: unless-stopped
    volumes:
      - ~/.config/ptool:/home/ptool/.config/ptool
    environment:
      - TZ=Asia/Shanghai
    command: brush <clientName>
```

运行：

```bash
docker compose up -d
```

## 定时任务配置

使用 cron 定时运行 ptool：

```bash
# 每小时执行一次刷流
0 * * * * docker run --rm -v ~/.config/ptool:/home/ptool/.config/ptool jiongjiongJOJO/ptool:latest brush <clientName>
```

## 本地构建

```bash
# 克隆仓库
git clone https://github.com/jiongjiongJOJO/ptool.git
cd ptool

# 构建镜像
docker build -t ptool:local .

# 运行本地构建的镜像
docker run --rm \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  ptool:local --help
```

## 多平台构建

```bash
# 启用 buildx
docker buildx create --use

# 构建多平台镜像
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t jiongjiongJOJO/ptool:latest \
  --push \
  .
```

## 故障排查

### 配置文件权限问题

如果遇到配置文件权限问题，请确保宿主机配置目录对 UID 1000 可读写：

```bash
chown -R 1000:1000 ~/.config/ptool
```

### 查看容器日志

```bash
docker logs ptool
```

### 进入容器调试

```bash
docker run --rm -it \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  --entrypoint /bin/sh \
  jiongjiongJOJO/ptool:latest
```

## 安全建议

- 容器以非 root 用户 (UID/GID 1000) 运行
- 不要在镜像中存储敏感凭据，使用挂载的配置文件
- 定期更新镜像以获取安全补丁：`docker pull jiongjiongJOJO/ptool:latest`

## 镜像标签说明

| 标签 | 说明 |
|------|------|
| `latest` | 最新稳定版本（来自 master 分支） |
| `v*.*.*` | 特定版本，例如 `v0.1.11` |
| `v*.*` | 次要版本，例如 `v0.1` |
| `master` | master 分支最新构建 |
