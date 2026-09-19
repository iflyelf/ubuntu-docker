# ubuntu-docker

Ubuntu 全功能开发/运维基础镜像。内置 **Go / Node.js / Python** 开发环境及完整的编译工具链、
系统运维与网络排障工具，适合作为构建、调试、多语言开发的通用基础镜像。

支持 `linux/amd64` 与 `linux/arm64` 多架构，镜像标签为 `latest`。

> 如果只需要轻量的运维/排障基础镜像（无 Go/Node.js/Python），请使用精简版
> [ubuntu-lite](https://github.com/iflyelf/ubuntu-lite)（标签 `lite`）。

## 镜像获取

```bash
# Docker Hub（国外）
docker pull iflyelf/ubuntu:latest

# 华为云 SWR（国内推荐）
docker pull swr.cn-east-3.myhuaweicloud.com/iflyelf/ubuntu:latest
```

## 运行

```bash
docker run -it --rm iflyelf/ubuntu:latest
```

默认 shell 为 zsh（已安装 oh-my-zsh）。

## 内置组件

- 时区：`Asia/Shanghai`
- 语言：`zh_CN.UTF-8`
- Shell：zsh + oh-my-zsh
- 开发语言：Go（构建时自动更新至最新稳定版）、Node.js（最新 LTS，含 wrangler）、Python3（含 pip、pycryptodome、lxml、requests 等）
- 编译工具链：gcc、g++、build-essential、autoconf、automake、libtool、cargo、rustc 等
- 网络与排障：iproute2、net-tools、nftables、ipset、ipvsadm、bridge-utils、openvswitch-switch、conntrack、socat、tcpdump、telnet、nmap、iftop、lsof、bind9-dnsutils、iputils-ping
- 系统工具：procps、psmisc、sysstat、htop、lvm2、rsyslog、firewalld、chrony、supervisor、tini
- 常用命令：curl、wget、axel、git、subversion、vim、jq、tree、zip/unzip、tar、openssl、sshpass

完整清单见 [Dockerfile](./Dockerfile) 中的 `PKG_DEPS`。

## 自动构建

以下情况会触发 [GitHub Actions](./.github/workflows/docker-publish.yml) 自动构建并推送到 Docker Hub 与华为云 SWR：

- 推送 `Dockerfile` 或工作流文件变更
- 手动触发（workflow_dispatch）
- Star 仓库
- 定时构建：**中国时间每天早 5 点**（UTC 21:00）

同一分支仅保留最新一次构建（`concurrency` + `cancel-in-progress`），避免多架构构建并发堆积。

### Go 版本自动更新

[update-go-version.yml](./.github/workflows/update-go-version.yml) 每天中国时间早 4 点检查 Go 官方最新稳定版，
若与 Dockerfile 中的 `GO_VERSION` 不同则自动更新并提交，进而触发镜像重建。

### 所需 Secrets

| Secret | 说明 |
| --- | --- |
| `DOCKER_USERNAME` / `DOCKER_PASSWORD` | Docker Hub 凭据 |
| `SWR_USERNAME` / `SWR_PASSWORD` | 华为云 SWR 登录凭据（`区域@AK` / 登录密钥） |
| `SWR_AK` / `SWR_SK` | 华为云账号 AK/SK，用于将 SWR 仓库设为公开（可选） |
