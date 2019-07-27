---
title: 无奈的"加速"(2)
date: 2019-03-18 16:57:55
categories:
  - Technology
  - Build
tags:
  - docker
---

# Docker 源配置

## 源列表

| name     | address                                  |
| -------- | ---------------------------------------- |
| official | `https://registry.docker-cn.com`(已废弃) |
| netease  | `http://hub-mirror.c.163.com`            |
| ustc     | `https://docker.mirrors.ustc.edu.cn`     |
| azure    | `http://dockerhub.azk8s.cn`              |
| tencent  | `https://mirror.ccs.tencentyun.com`      |

## 临时使用其他源

```bash
docker pull mirror/username/image:tag
```

```bash
docker pull registry.docker-cn.com/library/ubuntu:16.04
```

## 长期使用

通过在 Docker 守护进程启动时传入 --registry-mirror 参数，达到无需每次拉取时指定 mirror 的目的

```bash
docker --registry-mirror=https://registry.docker-cn.com daemon
```

除此之外还可以直接修改配置文件，但因为系统的不同，具体参见 docker[官方文档](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)
