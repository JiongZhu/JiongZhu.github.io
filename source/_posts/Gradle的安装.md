---
title: Gradle的安装
copyright: true
date: 2017-11-03 15:10:15
tags: Gradle
categories: Gradle
---

## 前言

　　上一篇文章从Gradle与Apache ant、Maven的构建过程进行了横向比较，在此首先总结一下Gradle的优点：

- 按约定声明构建和建设
- 强大的支持多工程的构建
- 强大的依赖管理（基于Apache Ivy），提供最大的便利去构建工程
- 全力支持已有的 Maven 或者Ivy仓库基础建设
- 支持传递性依赖管理，在不需要远程仓库和pom.xml和ivy配置文件的前提下
- 基于groovy脚本构建，其build脚本使用groovy语言编写
- 具有广泛的领域模型支持构建
- 深度 API
- 易迁移
- 自由和开放源码，Gradle是一个开源项目，基于 ASL 许可
<!-- more -->
## 安装

### 安装前的准备

Gradle可以在几乎所有的主流操作系统上运行，但是必须需要JDK7或者JRE7以上版本的支持。为了验证是否支持Gradle，请执行 ：

```
java -version
```

如果命令执行成功，应该会得到类似如下输出：

```
openjdk version "1.8.0_131"
OpenJDK Runtime Environment (build 1.8.0_131-8u131-b11-2ubuntu1.16.04.3-b11)
OpenJDK 64-Bit Server VM (build 25.131-b11, mixed mode)
```

如果没有安装JDK、JRE环境，或者安装的版本低于7，请自行安装。

### 开始安装

#### 使用包管理器安装

基于Unix的系统可以通过SDKMAN安装

```
$ sdk install gradle 4.3
```

MacOS系统可以通过Homebrew安装

```
$ brew update && brew install gradle
```

Windows系统下可以通过Scoop安装

```
$ scoop install gradle
```

#### 下载安装包安装

**第一步：下载最新的Gradle发行版本**

目前Gradle最新版本是于2017年10月30日发布的Gradle 4.3，zip文件下载地址为：

- [二进制文件](https://services.gradle.org/distributions/gradle-4.3-bin.zip)
- [完整版](https://services.gradle.org/distributions/gradle-4.3-all.zip)(包含文档和源代码)

如果不知如何选择，Gradle官方推荐下载二进制文件版本，然后在线浏览[文档](https://docs.gradle.org/current)和[源代码](https://github.com/gradle/gradle)。

如果需要下载更老的版本，可以查看[发行页面](https://gradle.org/releases)。

**第二步：解压发行版**

对于Linux和MacOS用户而言，可以解压zip文件到指定目录下：

```
$ mkdir /opt/gradle
$ unzip -d /opt/gradle gradle-4.3-bin.zip
$ ls /opt/gradle/gradle-4.3
LICENSE  NOTICE  bin  getting-started.html  init.d  lib  media
```

对于Windows用户而言，可以在文件资源管理器中新建一个目录(例如：C:\Gradle)，然后在打开一个资源管理器，进入Gradle发行包所在的目录，双击解压该文件，最后将解压后的gradle-4.0.2文件移动至新创建的目录中。

**第三步：配置系统环境变量**

对于Linux和MacOS用户，运行命令

```
sudo vi /etc/profile
```

或者

```
sudo gedit /etc/profile
```

在文档末尾输入

```
$ export PATH=$PATH:/opt/gradle/gradle-4.3/bin
```

对于Windows用户，可以在*此电脑*上右击选择属性，选择*高级系统设置*，选择*环境变量*，为Path增添Gradle的bin目录(例如：C:\Gradle\gradle-4.0.2\bin)。

**第四步：验证**

打开一个控制台(或Windows命令提示符)并运行

```
gradle -v
```

如果安装正常，会得到如下输出

```
------------------------------------------------------------
Gradle 4.2.1
------------------------------------------------------------

Build time:   2017-10-02 15:36:21 UTC
Revision:     a88ebd6be7840c2e59ae4782eb0f27fbe3405ddf

Groovy:       2.4.12
Ant:          Apache Ant(TM) version 1.9.6 compiled on June 29 2015
JVM:          1.8.0_131 (Oracle Corporation 25.131-b11)
OS:           Linux 4.10.0-38-generic amd64
```
