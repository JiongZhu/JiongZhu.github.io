---
title: Gradle+IntelliJ-IDEA-2017-2搭建SSM项目(二)——build.gradl配置
copyright: true
date: 2017-11-03 15:12:12
tags: [Gradle,SSM]
categories: Gradle
---

## 说明

本篇文章主要展示build.gradle文件的配置信息，代码上有大部分解释说明，如有其他问题请于文章下方讨论处提问。
<!-- more -->
## 文件详细配置

```
group 'com.jiongzhu'
version '1.0-SNAPSHOT'

//添加插件
apply plugin: 'java'
apply plugin: 'war'
apply plugin: 'idea'
apply from: 'https://raw.github.com/akhikhl/gretty/master/pluginScripts/gretty.plugin'

sourceSets.main.resources.srcDirs = ['src/main/resources', 'webapp']
//编码环境jdk版本
sourceCompatibility = 1.8
//编译时jdk版本
targetCompatibility = 1.8
//设置编码
tasks.withType(JavaCompile) {
    options.encoding = 'UTF-8'
}

//仓库的配置
repositories {
    mavenLocal()    //maven本地仓库，需要本地maven环境，当前安装maven-3.5.0
    maven { url "http://uk.maven.org/maven2/" } //镜像不错
    maven { url "http://maven.aliyun.com/nexus/content/groups/public/" }  //maven国内阿里镜像
    jcenter()
    mavenCentral()  //maven中央仓库
}

configurations {
    mybatisGenerator
}

//配置外部属性
ext {
    spring_version = "4.3.10.RELEASE"
    slf4j_version = "1.7.25"
    // 读取config文件夹中对应的配置文件
    prop = new Properties()
    file("src/main/resources/config/datasource.properties")
            .withInputStream {
        prop.load(it)
    }
    file("src/main/resources/config/mybatis/gradle.properties")
            .withInputStream {
        prop.load(it)
    }
    prop.each {
        project.extensions.add("$it.key",it.value)
    }
}

//依赖的配置
dependencies {
    //和Maven类似，gradle导入依赖包也可以定义其作用的生命周期：
    //compile：编译时必须。
    //providedCompile : 编译时必须，不会写入war。
    //runtime：运行时必须，包括编译时。
    //testCompile：测试编译时必须。
    //testRuntime：测试运行时必须，包括编译时。
    //注：此外配置依赖包，还可以模块化配置、导入list、配置是否传递等。

//jsp、servlet
    providedCompile (
            "javax.servlet:javax.servlet-api:3.1.0",
            "javax.servlet.jsp:jsp-api:2.2.1-b03",
    )
//单元测试
    testCompile "junit:junit:4.12"

//编译时依赖
    compile (
    //jsvaee7
            "javax:javaee-api:7.0",
    //jstl标签库
            "taglibs:standard:1.1.2",
            "javax.servlet:jstl:1.2",
    //公共资源包
            "commons-logging:commons-logging:1.2",
            "org.apache.commons:commons-lang3:3.6",
            "org.apache.commons:commons-collections4:4.1",
            "commons-beanutils:commons-beanutils:1.9.3",
            "org.apache.commons:commons-dbcp2:2.1.1",
            "org.apache.commons:commons-pool2:2.4.2",
    //文件上传
            "commons-fileupload:commons-fileupload:1.3.3",
            "commons-io:commons-io:2.5",
    //AspectJ（切点表达式）
            "org.aspectj:aspectjrt:1.8.10",
            "org.aspectj:aspectjweaver:1.8.10",
    //springmvc + Spring Configuration
            "org.springframework:spring-web:$spring_version",
            "org.springframework:spring-webmvc:$spring_version",
            "org.springframework:spring-aop:$spring_version",
            "org.springframework:spring-aspects:$spring_version",
            "org.springframework:spring-beans:$spring_version",
            "org.springframework:spring-context:$spring_version",
            "org.springframework:spring-context-support:$spring_version",
            "org.springframework:spring-core:$spring_version",
            "org.springframework:spring-expression:$spring_version",
            "org.springframework:spring-jdbc:$spring_version",
            "org.springframework:spring-messaging:$spring_version",
            "org.springframework:spring-orm:$spring_version",
            "org.springframework:spring-tx:$spring_version",
            "org.springframework:spring-test:$spring_version",
    //MyBatis
            "org.mybatis:mybatis:3.4.4",
    //mybatis spring 插件
            "org.mybatis:mybatis-spring:1.3.1",
    //mybatis generator 插件
            'org.mybatis.generator:mybatis-generator-core:1.3.5',
    //mybatis basemapper 插件
            'tk.mybatis:mapper:3.4.1',
    //数据库驱动，高版本与basemapper冲突
            "mysql:mysql-connector-java:5.1.42",
    //数据库连接池，选择阿里的druid
            "com.alibaba:druid:1.1.0",
    //json
            "com.google.code.gson:gson:2.8.1",
    //log
            "log4j:log4j:1.2.17",
            "org.slf4j:slf4j-api:$slf4j_version",
            "org.slf4j:slf4j-log4j12:$slf4j_version",
    //lombok，主要用于简化实体类代码，idea需要下载插件，自行百度
            "org.projectlombok:lombok:1.16.18",
    //validator
            "org.hibernate:hibernate-validator:5.4.1.Final",
    //freemarker
            "org.freemarker:freemarker:2.3.26-incubating"
    )
//mybatisGenerator
    mybatisGenerator (
            "mysql:mysql-connector-java:5.1.42",
            "org.mybatis:mybatis:3.4.4",
            "org.mybatis.generator:mybatis-generator-core:1.3.5",
            "tk.mybatis:mapper:3.4.1"
    )
}

// 利用ant创建一个自动生成MyBatis目录文件的task，Gradle Tasks视图other->mybatisGenerate
task mybatisGenerate {
    doLast {
        ant.properties['targetProject'] = projectDir.path
        ant.properties['jarDirection'] = project['jdbc.jarDirection']
        ant.properties['driverClass'] = project['jdbc.driverClassName']
        ant.properties['connectionURL'] = project['jdbc.url']
        ant.properties['userId'] = project['jdbc.username']
        ant.properties['password'] = project['jdbc.password']
        ant.properties['src_main_java'] = sourceSets.main.java.srcDirs[0].path
        ant.properties['src_main_resources'] = sourceSets.main.resources.srcDirs[0].path
        ant.properties['plugin'] = project['mapper.plugin']
        ant.properties['mapper'] = project['mapper.mapper']
        ant.properties['modelPackage'] = project['modelPackage']
        ant.properties['mapperPackage'] = project['mapperPackage']
        ant.properties['sqlMapperPackage'] = project['sqlMapperPackage']
        ant.properties['tableName'] = project['gererator.tableName']
        ant.properties['objectName'] = project['gererator.objectName']
        ant.properties['schema'] = project['gererator.schema']

        ant.taskdef(
                name: 'mbgenerator',
                classname: 'org.mybatis.generator.ant.GeneratorAntTask',
                classpath: configurations.mybatisGenerator.asPath
        )

        ant.mbgenerator(overwrite: true,
                configfile: 'src/main/resources/config/mybatis/generatorConfig.xml',
                verbose: true) {
            propertyset {
                propertyref(name: 'targetProject')
                propertyref(name: 'jarDirection')
                propertyref(name: 'userId')
                propertyref(name: 'driverClass')
                propertyref(name: 'connectionURL')
                propertyref(name: 'password')
                propertyref(name: 'src_main_java')
                propertyref(name: 'src_main_resources')
                propertyref(name: 'plugin')
                propertyref(name: 'mapper')
                propertyref(name: 'modelPackage')
                propertyref(name: 'mapperPackage')
                propertyref(name: 'sqlMapperPackage')
                propertyref(name: 'tableName')
                propertyref(name: 'objectName')
                propertyref(name: 'schema')
            }
        }
    }
}
//gretty配置，从gradle4.0开始，配置jetty不能再直接使用plugin:jetty，需要使用gretty
//启动项目，Gradle Tasks视图gretty->jettyRun/tomcatRun
gretty {
    servletContainer = 'tomcat8' //支持jetty7/8/9，tomcat7/8
    contextPath = '/'
}

```

