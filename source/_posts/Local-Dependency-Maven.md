---
title: 非公开的maven依赖处理
date: 2019-03-18 22:50:32
categories:
  - Technology
  - Build
tags:
  - mvn
---

## 1. 手动安装 jar 包

该方式通过手工安装 jar 包，从而使得 pom 能够在本地依赖库里面找到它，简而言之就是自己手工完成了 maven 帮忙处理的依赖下载步骤。

```
mvn install:install-file -Dfile=mylib.jar -DgroupId=com.example -DartifactId=mylib -Dversion=1.0 -Dpackaging=jar
```

## 2. 将本地文件夹指定为 maven repo

这一方式通过将本地的某个文件夹视为一个 maven repo，当在官方 repo 无法下载指定的包时他会去本地仓库下载。

当然，这个方式对于本地文件夹的组织结构有所要求，如下

```
yourproject
+- pom.xml
+- src
+- repo
   +- com
      +- example
         +- mylib
            +- maven-metadata.xml
            +- ...
            +- 1.0
               +- mylib-1.0.jar
               +- mylib-1.0.pom
               +- ...
```

他要求必须以 groupId/artifactId/version/jarname-version.jar 的方式存储文件，从而让 maven 可以通过 pom.xml 文件来寻找指定 jar 包进行下载(即最后这个 jar 包会被下载，如同从远程仓库下载那样)

```xml
<repositories>
    <!--other repositories if any-->
    <repository>
        <id>project.local</id>
        <name>project</name>
        <url>file:${project.basedir}/repo</url>
    </repository>
</repositories>

<dependency>
    <groupId>com.example</groupId>
    <artifactId>mylib</artifactId>
    <version>1.0</version>
</dependency>
```

这一方式可能会遭遇以下问题

- 关于验证策略的警告
- 因名称不规范的问题导致无法安装
  - jar 包的名字中 jarname 与 version 并不以`-`分割
  - groupId 试图以 com.example 作为文件夹名而非嵌套结构

关于名称不规范的问题，可以使用 maven install 的方式去解决它

```bash
mvn install:install-file \
    -Dfile=《Jar Path》 \
    -DgroupId=《Group Id》 \
    -DartifactId=《Artifact Id》 \
    -Dversion=《Version》 \
    -Dpackaging=jar \
    -DlocalRepositoryPath=《${project.basedir}》/libs/local-maven-repository
```

它将把一个依赖安装到你指定的文件夹（本地仓库）中，并按规范的方式进行命名，而无需担心名称不规范的问题

## 3. 使用 system 的 scope

```xml
<dependency>
  <groupId>com.example</groupId>
  <artifactId>mylib</artifactId>
  <version>1.0</version>
  <scope>system</scope>
  <systemPath>\${project.basedir}/src/main/resources/lib/mylib-1.0.jar</systemPath>
</dependency>
```

这一方式可能会遭遇以下问题

- maven 在打包时会发出警告
- 若不将 jar 包放置于`src`文件夹内，在打包时不会被打包，故而运行时将找不到该 jar 包从而发生异常， 可通过以下方式补偿
  ```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <executions>
          <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <descriptorRefs>
               <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
              <finalName>xxx-jar-with-dependencies</finalName>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
    <resources>
      <resource>
        <targetPath>repo/</targetPath>
        <directory>repo/</directory>
        <includes>
          <include>\*\*/mylib.jar</include>
        </includes>
      </resource>
    </resources>
  </build>
  ```
- 部分情形下可能失效，JDBCDriver 通过 Class.forName("xxx.Driver")的方式会找不到类

## 总结

综合来看使用手动安装的方式最为安全和稳定，但需要使用文档的方式告知其他开发者这一事项。

而使用本地 repo 的方式次之，在使用前务必确认可以正确安装避免给他人带来麻烦。

最不推荐使用 scope 为 system 的方式，maven 已经警告了该方案，并且当使用一些特殊 jar 包时可能发生问题。
