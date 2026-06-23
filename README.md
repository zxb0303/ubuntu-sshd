# ubuntu-sshd

一个开箱即用的 Ubuntu Docker 镜像，预装并启用了 SSH 服务，支持通过环境变量动态设置 root 密码，可用于快速搭建可远程登录的 Ubuntu 容器环境。

镜像地址：[`zxb0303/ubuntu-ssh`](https://hub.docker.com/r/zxb0303/ubuntu-ssh)

## 特性

- 基于官方 `ubuntu` 镜像构建，支持 `20.04` / `22.04` / `24.04` / `26.04` 多版本
- 预装常用工具：`openssh-server`、`sudo`、`curl`、`vim`
- 启动时通过环境变量 `ROOT_PASSWORD` 动态设置 root 密码，避免密码硬编码
- 默认开启 root 登录与密码认证，方便测试与内网使用
- 通过 GitHub Actions 自动构建并推送至 DockerHub

## 镜像标签

| Tag      | 基础镜像     |
| -------- | ------------ |
| `20.04`  | ubuntu:20.04 |
| `22.04`  | ubuntu:22.04 |
| `24.04`  | ubuntu:24.04 |
| `26.04`  | ubuntu:26.04 |

## 环境变量

| 变量名          | 默认值 | 说明                        |
| --------------- | ------ | --------------------------- |
| `ROOT_PASSWORD` | `root` | 容器启动时设置的 root 密码 |

## 端口

| 端口 | 协议 | 说明           |
| ---- | ---- | -------------- |
| `22` | TCP  | SSH 服务监听端口 |

## 快速开始

### 使用 docker-compose（推荐）

```yaml
version: '3'
services:
  ubuntu-sshd:
    restart: always
    image: zxb0303/ubuntu-ssh:24.04
    container_name: ubuntu-sshd
    ports:
      - "22:22"
    environment:
      ROOT_PASSWORD: 123456
    network_mode: "bridge"
```

启动：

```bash
docker-compose up -d
```

### 使用 docker run

```bash
docker run -d \
  --name ubuntu-sshd \
  --restart always \
  -p 22:22 \
  -e ROOT_PASSWORD=123456 \
  zxb0303/ubuntu-ssh:24.04
```

### 连接 SSH

```bash
ssh root@<服务器IP>
# 输入上面设置的 ROOT_PASSWORD 即可登录
```

## 自行构建

仓库中的 [`Dockerfile.template`](./Dockerfile.template) 使用 `{{UBUNTU_VERSION}}` 占位符支持多版本构建。

### 本地构建

```bash
# 替换占位符并生成 Dockerfile
sed "s/{{UBUNTU_VERSION}}/24.04/g" Dockerfile.template > Dockerfile

# 构建镜像
docker build -t my-ubuntu-ssh:24.04 .
```

### 通过 GitHub Actions 构建

仓库已配置 [`docker-publish.yml`](./.github/workflows/docker-publish.yml) 工作流，在 GitHub 仓库的 **Actions** 页面手动触发 `Docker Build and Push`，选择目标 Ubuntu 版本（`20.04` / `22.04` / `24.04` / `26.04`）即可自动构建并推送至 DockerHub。

需要在仓库 Secrets 中预先配置：

- `DOCKERHUB_USERNAME`：DockerHub 用户名
- `DOCKERHUB_TOKEN`：DockerHub Access Token

## 安全提示

- 该镜像默认允许 root 通过密码登录，**仅建议在内网或测试环境使用**
- 在公网环境部署时，请：
  - 使用强密码或改用 SSH 密钥认证
  - 修改默认对外端口（例如映射为 `2222:22`）
  - 配合防火墙限制访问来源

## License

MIT
