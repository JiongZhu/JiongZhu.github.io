---
title: Gradle+IntelliJ-IDEA-2017-2搭建SSM项目(一)——创建项目
copyright: true
date: 2017-11-03 15:11:56
tags: [Gradle,SSM]
categories: Gradle
---
## 环境准备
- OpenJDK 1.8.0_131
- Gradle 4.3
- Intellij Idea 2017.2

为了便于管理Gradle本地仓库，首先创建系统变量GRADLE_USER_HOME，并将值指向自定义的.gradle目录(如/home/jiongzhu/文档/repository/.gradle)。
在.gradle文件夹下创建文件gradle.properties，内容如下：
<!-- more -->

```
org.gradle.daemon=true
```
据说可以提高Gradle的编译速度。
然后在.gradle文件夹下创建文件init.gradle，内容如下:
```groovy
allprojects{
    repositories {
        def REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public/'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                    remove repo
                }
            }
        }
        maven {
            url REPOSITORY_URL
        }
    }
}
```
将速度比较慢的mavenCentral()与jcenter()远程仓库镜像替换为速度较快的镜像，这里配置的是aliyun的maven中央仓库。
另外我收集的可用镜像还有如下：
```http
http://maven.aliyun.com/nexus/content/groups/public/
http://repo1.maven.org/maven2/
http://repo2.maven.org/maven2/
http://uk.maven.org/maven2/
http://repository.sonatype.org/content/groups/public/
http://repository.jboss.org/nexus/content/groups/public/
http://maven.ibiblio.org/maven2/
http://maven-central.storage.googleapis.com
```
前期准备工作完成。
## 开始创建
### 创建Java Web项目
首先需要创建一个基于Gradle的Java Web项目，可以参考我的上一篇文章[Gradle+IntelliJ-IDEA-2017-2创建Java-Web项目](http://zhuxuanyu.top/2017/08/04/Gradle+IntelliJ-IDEA-2017-2创建Java-Web项目/)。创建完的项目结构如下图所示。
{% qnimg 20190222/0uUf4xl4A0Hn.png %}

### 创建基本包结构与资源文件结构
在src/main/java下，创建包com.jiongzhu，然后在其下创建如下几个包控制器controller，实体接口mapper，实体model，服务service，工具util。
在src/main/resources下，创建包config，然后在其下创建如下几个包mapper(实体映射配置文件)，mybatis(自动生成实体、实体映射、实体接口)，spring(springMVC配置)。
{% qnimg 20190222/PUoGg0fQySDR.png?imageslim %}

### 创建部分资源配置文件和静态资源文件结构
在mybatis包下创建generatorConfig.xml，用于mybatisGenerator的相关配置，通过mybatisGenerator配合通用BaseMapper接口可以自动生成实体、实体映射、实体接口，自行创建BaseService后，用户自定义的业务Service可以继承BaseService，直接使用其相关的增删改查等操作，用户只需要编写BaseService没有的方法即可。
在mybatis包下创建gradle.properties，用于generatorConfig.xml的部分参数配置。
在spring包下创建spring-mvc.xml，主要用于控制器的自动注入，静态资源映射配置，启用注解，视图解析器的配置等。
在spring包下创建spring-mybatis.xml，主要用于service的自动注入，数据库连接池的配置，mapper接口与mapper映射的自动扫描注册，日志管理，事务管理等。
在config包根路径下创建datasource.properties，用于数据库连接池的参数配置。
在config包根路径下创建log4j.properties，用于日志的配置。
基本文件结构如下。
{% qnimg 20190222/IllqWAWnr1mX.png?imageslim %}
如上图所示，我们还在webapp/WEB-INF目录下创建了statis、views两个文件夹，在statics文件夹下创建了css、fonts、img、js文件夹，在views文件夹下创建了html、jsp文件夹，用来放置相关的静态资源文件。

### IDEA项目结构与模块配置
File->Project Structure->Moudles->GradleSSMDemo->Spring->点击"+"号，勾选已经创建的spring-mvc.xml文件和spring-mybatis.xml文件。
{% qnimg 20190222/W4w71fumI6mv.png?imageslim %}

下篇文章主要展示我的build.gradle配置文件的内容。
