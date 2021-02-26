---
title: Java-Obfuscate
date: 2019-11-17 01:32:22
tags:
  - Java
categories:
  - Technology
---

# Java 代码混淆备忘

## ProGuard

在各类 Java 混淆器里面 ProGuard 是被介绍最多的，详细的过程不在此介绍。

但在实际的使用中，因为 spring boot 携带有大量的反射，类名获取以及繁琐的规则配置，要对 spring boot 的 bean 进行混淆是个艰难的工作。

最终这一方案被废弃

## obfuscator

[obfuscator](https://github.com/superblaubeere27/obfuscator)提供了一个简单的 gui 工具，使用方便，最后我选择了它作为代码的混淆工具。

但在使用过程中仍有几个注意事项

1. `LineNumberRemover`中的`Add Local Names`不可勾选，这可能导致 spring boot 的依赖注入失败，失败原因为发现两个同名的 bean
1. `LineNumberRemover`中的`Rename Local variables`不可勾选，这会重命名参数变量，若 controller 中没有通过@PathVariable 和@RequestParam 指定读取路径变量与查询参数的名称，可能导致错误
1. `HideMembers`中的`Enabled`需要取消勾选，若勾上，可能导致依赖注入失败
1. 该工具仅限于 jar 包使用，且在前后端分离后端仅提供 api 的项目中使用更易成功
1. 混淆过的 jar 包已经被压缩过了，需要解压后通过 `jar cfM0 tccw.jar 目录` 重新打包才能正常运行

在以上配置中，可以很容易的进行业务代码混淆，生成大量不可读的变量与数字替换
