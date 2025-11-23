---
slug: deploy-clash-with-docker
title: 使用 docker 方式部署 clash
tags:
  - Docker
  - Clash
categories:
  - 技术分享
description: >-
  本文介绍了如何在 Linux 系统下使用 Docker 部署 Clash 服务，以便更方便地访问国外网站和服务。通过 Docker
  部署，不仅简化了安装过程，还提升了系统的可维护性和跨平台兼容性
abbrlink: 42629
date: 2025-03-06 00:54:22
---

在日常工作中，我们经常需要访问一些国外网站或者服务。Windows 和 MacOS 都有图形化的 Clash 客户端，但在 Linux 系统下，特别是 Ubuntu，找到一个稳定可靠的 Clash 客户端并不容易。为了解决这个问题，我们可以利用 Docker 来部署 Clash 服务，这不仅简化了部署过程，还提供了更好的可维护性和跨平台兼容性。

<!-- truncate -->

## 实现

```yaml
services:
  clash:
    image: dreamacro/clash:v1.18.0
    container_name: clash
    ports:
      - 9000:9000
      - 7890:7890
    volumes:
      - ./config.yaml:/root/.config/clash/config.yaml
    mem_limit: ${CLASH_MEM_LIMIT:-64m}
    restart: unless-stopped
```

需要从机场下载配置文件 config.yaml，并放在当前目录下。

```yaml
port: 7890
socks-port: 7890
allow-lan: true
mode: rule
log-level: silent
external-controller: 0.0.0.0:9000
```

## 关键配置说明

配置文件中有两个特别重要的设置：

- `allow-lan: true` - 开启局域网访问，允许同一网络中的其他设备连接到此代理
- `external-controller: 0.0.0.0:9000` - 启用外部控制接口，便于使用 Dashboard 进行可视化管理

## 启动与验证

### 1. 启动服务

使用 Docker Compose 一键启动 Clash 服务：

```sh
docker compose up -d
```

### 2. 验证服务状态

访问 http://localhost:9000 检查 Clash API 是否正常响应。成功时应返回：

```json
{
  "hello": "clash"
}
```

这表明 Clash 服务已正常运行并准备好接受连接。

## 代理设置

### 设置系统代理

将系统 HTTP/HTTPS 代理配置为 `127.0.0.1:7890`。根据不同操作系统，设置方式略有不同：

- **Linux**: 在系统设置或环境变量中配置

  ```bash
  export http_proxy=http://127.0.0.1:7890
  export https_proxy=http://127.0.0.1:7890
  ```

- **Windows/macOS**: 在网络设置中配置代理服务器

### 验证代理连接

配置完成后，可以通过访问一些国外网站或使用以下命令测试代理是否工作：

```bash
curl -I https://www.google.com
```

## 总结与优势

通过 Docker 部署 Clash 的主要优势：

- **跨平台兼容**: 在 Linux、Windows 和 macOS 上都能一致运行
- **隔离环境**: 避免与系统环境发生冲突
- **便捷管理**: 一键启动、停止和更新
- **配置简单**: 只需修改配置文件，无需复杂的安装过程
- **资源控制**: 可以限制内存使用，避免系统资源浪费

Docker 提供的统一运行环境让 Clash 服务的部署和管理变得简单高效，是搭建个人代理服务的理想选择。
