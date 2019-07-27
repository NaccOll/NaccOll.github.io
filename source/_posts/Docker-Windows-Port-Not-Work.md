---
title: Docker-Windows-Port-Not-Work
date: 2019-06-09 12:20:31
categories:
  - Technology
  - Build
tags:
  - docker
---

在windows上，Docker的部分端口无法使用，有时候会提示无权限，有时候提示是无法操作

1. windows快速启动导致的
  [关闭快速启动](https://github.com/docker/for-win/issues/573#issuecomment-486648315)

1. hype-v保留了端口，无法使用
  [弃用保留端口](https://github.com/docker/for-win/issues/3171#issuecomment-459205576)
  