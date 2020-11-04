---
title: Docker-Windows-Port-Not-Work
date: 2019-06-09 12:20:31
categories:
  - Technology
  - Build
tags:
  - docker
---

在 windows 上，Docker 的部分端口无法使用，有时候会提示无权限，有时候提示是无法操作

1. windows 快速启动导致的
   [关闭快速启动](https://github.com/docker/for-win/issues/573#issuecomment-486648315)

1. hype-v 保留了端口，无法使用
   [弃用保留端口](https://github.com/docker/for-win/issues/3171#issuecomment-459205576)

1. 开机端口分配，类似于上一条
  netsh int ip set dynamicport tcp start=49152 num=16384
