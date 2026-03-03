# ptool Docker 支持

本文档介绍 ptool 的 Docker 镜像构建和使用功能。

## 功能特性

- 🐳 多阶段构建，镜像体积小
- 🔒 非 root 用户运行（UID/GID 1000）
- 🌍 支持 linux/amd64 和 linux/arm64 多平台
- ⚙️ 支持外部配置文件挂载
- 🤖 GitHub Actions 自动化构建和发布

## 前置准备

如需推送到 Docker Hub，请在 GitHub 仓库的 Secrets 中配置：

- `DOCKER_USERNAME`：Docker Hub 用户名
- `DOCKER_PASSWORD`：Docker Hub 访问令牌

## 使用方法

### 拉取并运行

```bash
docker pull jiongjiongJOJO/ptool:latest
docker run --rm \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  jiongjiongJOJO/ptool:latest --help
```

### 本地构建

```bash
docker build -t ptool:local .
docker run --rm ptool:local version
```

### 多平台构建

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t jiongjiongJOJO/ptool:latest \
  --push \
  .
```

## 自动化构建

GitHub Actions 工作流（`.github/workflows/docker-build.yml`）会在以下情况自动构建：

- 推送到 `master` 或 `main` 分支：构建并推送 `latest` 标签
- 创建版本标签（`v*`）：构建并推送对应版本标签
- Pull Request：仅构建，不推送

## 目录结构

```
.
├── Dockerfile              # 多阶段构建配置
├── .dockerignore           # Docker 构建忽略文件
├── Makefile                # 便捷构建命令
├── docs/
│   └── DOCKER.md           # 详细 Docker 使用文档
└── .github/
    └── workflows/
        └── docker-build.yml  # CI/CD 工作流
```

## 配置说明

配置文件挂载到容器内的 `/home/ptool/.config/ptool` 目录：

```bash
docker run --rm \
  -v /path/to/your/config:/home/ptool/.config/ptool \
  jiongjiongJOJO/ptool:latest <command>
```

## 使用示例

```bash
# 查看版本
docker run --rm jiongjiongJOJO/ptool:latest version

# 查看状态
docker run --rm \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  jiongjiongJOJO/ptool:latest status

# 刷流
docker run --rm \
  -v ~/.config/ptool:/home/ptool/.config/ptool \
  jiongjiongJOJO/ptool:latest brush <clientName>
```

## 故障排查

- **权限问题**：确保配置目录对 UID 1000 可读写：`chown -R 1000:1000 ~/.config/ptool`
- **时区问题**：通过环境变量设置时区：`-e TZ=Asia/Shanghai`

## 更多信息

详细文档请参考 [docs/DOCKER.md](docs/DOCKER.md)。
