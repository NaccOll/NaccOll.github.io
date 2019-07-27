---
title: 无奈的"加速"(1)
date: 2019-03-17 01:38:51
categories:
  - Technology
  - Build
tags:
  - npm
---

# npm 源配置

## 当前配置查看

使用 `npm config list` 可以查看到当前 npm 的相关配置信息，其中，`registry`是当前使用 npm 进行 node_module 下载安装时的源。

上述命令也可以直接使用下面的命令替代

```bash
npm config get registry
```

## 源的修改

### 可用源

| name   | address                           |
| ------ | --------------------------------- |
| npm    | https://registry.npmjs.org/       |
| cnpm   | http://r.cnpmjs.org/              |
| taobao | https://registry.npm.taobao.org/   |
| nj     | https://registry.nodejitsu.com/   |
| skimdb | https://skimdb.npmjs.com/registry |

### 临时修改

当某次模块安装出现网络问题是，可以使用以下指令应急

```
npm install module --registry third_registry
```

如下，使用淘宝源下载 webpack

```
npm install webpack --registry https://registry.npm.taobao.org/
```

### 全局修改

`npm config set config_item` 可以用来设置 npm 的配置项，若有需要则可以使用以下指令变更全局的源。

```bash
npm config set registry third_registry
```

如下，全局使用淘宝源

```bash
npm config set registry https://registry.npm.taobao.org/
```

### NRM(NPM registry manager)

[nrm](https://github.com/Pana/nrm) 提供了在各类源进行快速切换的能力，并且可以测试自身网络对不同源的速度

#### 安装

```bash
npm install -g nrm
```

#### 使用

```bash
nrm use third_registry_name
```

如下，使用 cnpm 的源

```bash
nrm use cnpm
```

#### 测试

```bash
nrm test third_registry_name
```

如下，测试当前网络使用 cnpm 的延迟

```bash
nrm use cnpm
```
