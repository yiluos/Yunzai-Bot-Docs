---
title: Docker 部署
icon: structure
order: 1
---

> 在Windows中运行Docker必须保证自己的Windows版本为**2004及更高版本**或**Windows 11**。若要检查 Windows 版本及内部版本号，选择 Windows + R，键入“winver”回车查看。

## 镜像版本

目前，docker 镜像分为 **精简版** 和 **扩展版**，精简版仅有云崽本体（可选加载喵喵插件和图鉴插件），扩展版附带 ffmpeg 环境和 Python 环境（可选加载 Python 插件）。

## 安装 WSL2

右击开始菜单用管理员权限打开PowerShell，输入以下命令，安装完毕后重启

```bash
wsl --install
```

[微软官方文档](https://docs.microsoft.com/zh-cn/windows/wsl/)

## 安装 Docker Desktop

```bash
winget install 'Docker Desktop'
```
[或者点此下载安装]（https://www.docker.com/）

## 部署云崽

新建自己的BOT文件夹，在BOT文件夹下新建文本文档，输入以下代码，重命名文本文档为docker-compose.yaml

::: tabs

@tab 扩展版

version: "3.9"
services:
  yunzai-bot:
    container_name: yunzai-bot
    image: swr.cn-south-1.myhuaweicloud.com/sirly/yunzai-bot:v3plus     # 使用扩展镜像，包含ffmpeg和python
    restart: always
    volumes:
      - ./yunzai/config/:/app/Yunzai-Bot/config/config/                 # Bot基础配置文件
      - ./yunzai/genshin_config:/app/Yunzai-Bot/plugins/genshin/config  # 公共Cookie，云崽功能配置文件
      - ./yunzai/logs:/app/Yunzai-Bot/logs                              # 日志文件
      - ./yunzai/data:/app/Yunzai-Bot/data                              # 数据文件
      # 以下目录是插件目录，安装完插件后需要手动添加映射
      # - ./yunzai/plugins/miao-plugin:/app/Yunzai-Bot/plugins/miao-plugin                  # 喵喵插件
      # - ./yunzai/plugins/py-plugin:/app/Yunzai-Bot/plugins/py-plugin                      # 新py插件
      # - ./yunzai/plugins/xiaoyao-cvs-plugin:/app/Yunzai-Bot/plugins/xiaoyao-cvs-plugin    # 图鉴插件
    depends_on:
      redis: { condition: service_healthy }

  redis:
    container_name: yunzai-redis
    image: redis:alpine
    restart: always
    volumes:
      - ./redis/data:/data
      - ./redis/logs:/logs
    healthcheck:
      test: [ "CMD", "redis-cli", "PING" ]
      start_period: 10s
      interval: 5s
      timeout: 1s

@tab 精简版

version: "3.9"
services:
  yunzai-bot:
    container_name: yunzai-bot
    image: swr.cn-south-1.myhuaweicloud.com/sirly/yunzai-bot:v3         # 使用云端精简镜像
    restart: always
    volumes:
      - ./yunzai/config/:/app/Yunzai-Bot/config/config/                 # Bot基础配置文件
      - ./yunzai/genshin_config:/app/Yunzai-Bot/plugins/genshin/config  # 公共Cookie，云崽功能配置文件
      - ./yunzai/logs:/app/Yunzai-Bot/logs                              # 日志文件
      - ./yunzai/data:/app/Yunzai-Bot/data                              # 数据文件
      # 以下目录是插件目录，安装完插件后需要手动添加映射
      # - ./yunzai/plugins/miao-plugin:/app/Yunzai-Bot/plugins/miao-plugin                  # 喵喵插件
      # - ./yunzai/plugins/py-plugin:/app/Yunzai-Bot/plugins/py-plugin                      # 新py插件
      # - ./yunzai/plugins/xiaoyao-cvs-plugin:/app/Yunzai-Bot/plugins/xiaoyao-cvs-plugin    # 图鉴插件
    depends_on:
      redis: { condition: service_healthy }

  redis:
    container_name: yunzai-redis
    image: redis:alpine
    restart: always
    volumes:
      - ./redis/data:/data
      - ./redis/logs:/logs
    healthcheck:
      test: [ "CMD", "redis-cli", "PING" ]
      start_period: 10s
      interval: 5s
      timeout: 1s

:::

cd进入自己的BOT文件夹，运行命令

```bash
docker-compose up
```
