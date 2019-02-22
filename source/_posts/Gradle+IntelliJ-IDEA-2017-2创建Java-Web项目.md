---
title: Gradle+IntelliJ-IDEA-2017-2创建Java-Web项目
copyright: true
date: 2017-11-03 15:11:27
tags:
categories:
---

## 环境准备

- OpenJDK 1.8.0_131
- Gradle 4.3
- Intellij Idea 2017.2

## 开始搭建

1.首先进入Idea的新建项目窗口，选择新建“Gradle”项目，同时进行相应的配置，包括JDK环境、Java和Web环境框架的勾选。next。
<!-- more -->
{% qnimg 20190222/xI7lqdT6tGSN.png?imageslim %}
2.设置项目组织唯一标识、项目唯一标识和项目版本。
GroupID是项目组织唯一的标识符，实际对应JAVA的包的结构，是main目录里java的目录结构。 
ArtifactID就是项目的唯一的标识符，实际对应项目的名称，就是项目根目录的名称。
一般来说，包的命名习惯是域名的反过来,根据个人习惯加上公司或者个人的名称。如：www.jiongzhu.com -> com.jiongzhu.gradleDemo。那么都知道，gradleDemo是项目名称，也是在最后面的，所以项目组织的唯一标识符(groupId)对应com.jiongzhu, 项目的唯一标识符（ArtifactId）对应gradleDemo，这样你就知道大概对应填些什么东西了。next。
{% qnimg 20190222/vQE8Auxc3Wyq.png?imageslim %}
3.这个页面，刚进来默认选择的是Use default gradle wrapper(recommended), 我不选择这个是因为我本地有装gradle，所有不需要用idea默认的，如果选择它，那么创建项目之后，它就会去自动下载gradle，然后配置好，下载过程一般都很慢，使用体验一般。所有我选择的是Use local gradle distribution。然后最上面的一个选项是设置当gradle的依赖配置改变时，会自动进行jar包的删除和导入，这里我们选择手动更新，第二个选项是自动创建空的根目录文件夹，如果不选择，创建完的项目会没有java、resources等文件夹，所以勾选，让它自动创建。next。
{% qnimg 20190222/rLVhhVQBkjND.png?imageslim %}
4.这是项目新建完成的层级结构，已经有了Java Web项目的基本结构。
{% qnimg 20190222/mMJqqC6E9EEW.png?imageslim %}
5.依次选择file->Project Structure->Moudles/Facets，点击Deployment Descriptors右侧的“+”号新建web.xml文件，注意路径设置为\gradleDemo\src\main\webapp\WEB-INF\web.xml。next。
{% qnimg 20190222/PYG4S5nudjeG.png?imageslim %}
6.新建web.xml后，勾选“java”为Source Roots。next。
{% qnimg 20190222/bxTueAy4O4rj.png?imageslim %}
7.build.gradle的初始内容如下图所示。
{% qnimg 20190222/lF4P0xLrbedU.png?imageslim %}
8.如下图, 左侧是成功创建的项目文件目录，如果之前没有勾选Create directories for empty content roots automatically，那么就不会生成java和resource这个两个空文件夹。
{% qnimg 20190222/0uUf4xl4A0Hn.png?imageslim %}
9.创建完成之后，如果你之前没有配置tomcat的话，是没办法运行的，所以也需要配置tomcat，依次点击右上角的"倒三角"->Edit Configurations...->"+"->Tomcat Server->Local。
{% qnimg 20190222/CWeOhdHtJjee.png?imageslim %}
10.Tomcat Server的基本配置，包括服务器的名称，本地tomcat的路径。
{% qnimg 20190222/iEnFN4Hr6fwP.png?imageslim %}
11.点击“Server”右侧的“Deployment”，点击“+”号，选择带有(deploded) 的Artifacts作为部署的选择，配置Application context：/gradleDemo，如果不作输入，则访问地址为：localhost:8080，当前访问地址为localhost:8080/gradleDemo。点击“Apply”应用->“OK”。
{% qnimg 20190222/1AugzAz3LabD.png?imageslim %}
12.编辑index.jsp的内容，用于校验项目搭建是否成功，输入正文内容“Hello World！”。
{% qnimg 20190222/LOQfHTmF9x9U.png?imageslim %}
13.点击右上角服务器后的绿色三角形启动按钮，启动刚刚配置的Tomcat8.5，观察控制台输出。
{% qnimg 20190222/fNVYecJ3SI2L.png?imageslim %}
14.最后一步，进入浏览器观察项目运行结果，项目搭建完成。
{% qnimg 20190222/LtailEOxxe0t.png?imageslim %}
