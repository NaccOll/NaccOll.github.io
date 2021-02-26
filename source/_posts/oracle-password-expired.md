---
title: Oracle 密码过期解决方案
date: 2019-12-09 00:55:22
tags:
  - oracle
  - database
categories:
  - Technology
---

# Oracle 密码过期解决方案

## 背景

本地使用 docker 来启动 oracle，但遭遇密码过期的情况，因账号已过期无法使用 gui 访问

## 解决方案

首先 docker 使用 exec 进入容器内部

其次运行，进入 oracle 命令行工具

```
export ORACLE_SID=YourDB
sqlplus /nolog
```

最后运行

```
connect sys(用户名) as sysdba
```

将提醒你重置密码

## 后续处理

```sql
select profile from DBA_USERS where username = '<username>';
alter profile <profile_name> limit password_life_time UNLIMITED;
select resource_name,limit from dba_profiles where profile='<profile_name>';
```
