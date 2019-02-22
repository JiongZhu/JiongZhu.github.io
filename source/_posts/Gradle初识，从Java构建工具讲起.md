---
title: Gradle初识，从Java构建工具讲起
copyright: true
date: 2017-11-03 15:09:43
tags: [构建工具,Gradle]
categories: Gradle
---

### 背景

　　既然说到Gradle，我们首先要明白Gradle到底是个啥。维基百科这样定义：

> 　　Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化建构工具。它使用一种基于Groovy的特定领域语言来声明项目设置，而不是传统的XML。当前其支持的语言限于Java、Groovy和Scala，计划未来将支持更多的语言。

　　没错它是一种构建工具，所以我们先从构建工具讲起。创世之初，世上只有Make一种构建工具，后来，其发展为GNU Make。但是，由于需求的不断涌现，码农的世界里逐渐演化出了千奇百怪的构建工具。

　　当前，JVM生态圈由三大构建工具所统治：

- Apache Ant带着Ivy
- Maven
- Gradle
<!-- more -->
### 构建工具

#### Ant with Ivy

　　Ant是第一个“现代”构建工具，在很多方面它有些像Make。2000年发布，在很短时间内成为Java项目上最流行的构建工具。它的学习曲线很缓，因此不需要什么特殊的准备就能上手。它基于过程式编程的idea。在最初的版本之后，逐渐具备了支持插件的功能。
　　主要的不足是用XML作为脚本编写格式。 XML，本质上是层次化的，并不能很好地贴合Ant过程化编程的初衷。Ant的另外一个问题是，除非是很小的项目，否则它的XML文件很快就大得无法管理。
后来，随着通过网络进行依赖管理成为必备功能，Ant采用了Apache Ivy。

　　Ant的主要优点在于对构建过程的控制上。

#### Maven

　　Maven发布于2004年。目的是解决码农使用Ant所带来的一些问题。
　　Maven仍旧使用XML作为编写构建配置的文件格式，但是，文件结构却有巨大的变化。Ant需要码农将执行task所需的全部命令都一一列出，然而Maven依靠约定（convention）并提供现成的可调用的目标（goal）。不仅如此，有可能最重要的一个补充是，Maven具备从网络上自动下载依赖的能力（Ant后来通过Ivy也具备了这个功能），这一点革命性地改变了我们开发软件的方式。
　　但是，Maven也有它的问题。依赖管理不能很好地处理相同库文件不同版本之间的冲突（Ivy在这方面更好一些）。XML作为配置文件的格式有严格的结构层次和标准，定制化目标（goal）很困难。因为Maven主要聚焦于依赖管理，实际上用Maven很难写出复杂、定制化的构建脚本，甚至不如Ant。
用XML写的配置文件会变得越来越大，越来越笨重。在大型项目中，它经常什么“特别的”事还没干就有几百行代码。
　　Maven的主要优点是生命周期。只要项目基于一定之规，它的整个生命周期都能够轻松搞定，代价是牺牲了灵活性。

#### Gradle

　　Gradle结合了前两者的优点，在此基础之上做了很多改进。它具有Ant的强大和灵活，又有Maven的生命周期管理且易于使用。最终结果就是一个工具在2012年华丽诞生并且很快地获得了广泛关注。例如，Google采用Gradle作为Android OS的默认构建工具。
　　Gradle不用XML，它使用基于Groovy的专门的DSL，从而使Gradle构建脚本变得比用Ant和Maven写的要简洁清晰。Gradle样板文件的代码很少，这是因为它的DSL被设计用于解决特定的问题：贯穿软件的生命周期，从编译，到静态检查，到测试，直到打包和部署。
　　它使用Apache Ivy来处理Jar包的依赖。
　　Gradle的成就可以概括为：约定好，灵活性也高。

### 代码示例

　　我们写一段构建脚本来完成从编译、静态检查、单元测试到最终打Jar包的过程。我们分别使用三个框架（Ant，Maven和Gradle）来完成这件事情，比较一下语法。通过比较每一个任务的代码，我们能够对差异有一个更好的理解，并对选择构建工具做出一个周全的决策。

#### Ant with Ivy

　　Ivy的依赖需要在ivy.xml中指定。我们的例子很简单，只需要依赖JUnit和Hamcrest。

ivy.xml

```xml
<ivy-module version="2.0">  
    <info organisation="org.apache" module="java-build-tools"/>  
    <dependencies>  
        <dependency org="junit" name="junit" rev="4.11"/>  
        <dependency org="org.hamcrest" name="hamcrest-all" rev="1.3"/>  
    </dependencies>  
</ivy-module> 
```

　　现在我们来创建Ant脚本，任务只是编译一个Jar文件。最终文件是下面的build.xml。

build.xml

```xaml
<project xmlns:ivy="antlib:org.apache.ivy.ant" name="java-build-tools" default="jar">
	
	<property name="src.dir" value="src"/>  
    <property name="build.dir" value="build"/>  
    <property name="classes.dir" value="${build.dir}/classes"/>  
    <property name="jar.dir" value="${build.dir}/jar"/>  
    <property name="lib.dir" value="lib" />  
    <path id="lib.path.id">  
    	<fileset dir="${lib.dir}" />  
    </path>  
       
    <target name="resolve">  
    	<ivy:retrieve />  
    </target>  
       
    <target name="clean">  
        <delete dir="${build.dir}"/>  
    </target>  
       
    <target name="compile" depends="resolve">  
        <mkdir dir="${classes.dir}"/>  
        <javac srcdir="${src.dir}" destdir="${classes.dir}" classpathref="lib.path.id"/>  
    </target>  
       
    <target name="jar" depends="compile">  
        <mkdir dir="${jar.dir}"/>  
        <jar destfile="${jar.dir}/${ant.project.name}.jar" basedir="${classes.dir}"/>  
    </target>
    
</project>  
```

　　首先，我们设置了几个属性，然后是一个接一个的task。我们用Ivy来处理依赖，清理，编译和打包，这是几乎所有的Java项目都会进行的task，配置有很多。
运行Ant task来生成Jar文件，执行下面的命令。

```
ant jar
```

#### Maven

pom.xml

```xaml
<project xmlns="http://maven.apache.org/POM/4.0.0"  
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
http://maven.apache.org/maven-v4_0_0.xsd">
   
    <modelVersion>4.0.0</modelVersion>  
    <groupId>com.technologyconversations</groupId>  
    <artifactId>java-build-tools</artifactId>  
    <packaging>jar</packaging>  
    <version>1.0</version>  
   
    <dependencies>  
        <dependency>  
            <groupId>junit</groupId>  
            <artifactId>junit</artifactId>  
            <version>4.11</version>  
        </dependency>  
        <dependency>  
            <groupId>org.hamcrest</groupId>  
            <artifactId>hamcrest-all</artifactId>  
            <version>1.3</version>  
        </dependency>  
    </dependencies>  
   
    <build>  
        <plugins>  
            <plugin>  
                <groupId>org.apache.maven.plugins</groupId>  
                <artifactId>maven-compiler-plugin</artifactId>  
                <version>2.3.2</version>  
            </plugin>  
        </plugins>  
    </build>  
   
</project>
```

　　通过执行下面的命令来运行Maven goal生成Jar文件。

```
mvn package
```

　　主要的区别在于Maven不需要指定执行的操作。我们没有创建task，而是设置了一些参数（有哪些依赖，用哪些插件...)。Maven推行使用约定并提供了开箱即用的goals。Ant和Maven的XML文件都会随时间而变大，为了说明这一点，我们加入CheckStyle，FindBugs和PMD插件来进行静态检查，三者是Java项目中使用很普遍的的工具。我们希望将所有静态检查的执行以及单元测试一起作为一个单独的targetVerify。当然我们还应该指定自定义的checkstyle配置文件的路径并且确保错误时能够提示。更新后的Maven代码如下：

pom.xml

```xml
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-checkstyle-plugin</artifactId>  
    <version>2.12.1</version>  
    <executions>  
        <execution>  
            <configuration>  
                <configLocation>config/checkstyle/checkstyle.xml</configLocation>  
                <consoleOutput>true</consoleOutput>  
                <failsOnError>true</failsOnError>  
            </configuration>  
            <goals>  
                <goal>check</goal>  
            </goals>  
        </execution>  
    </executions>  
</plugin>  
<plugin>  
    <groupId>org.codehaus.mojo</groupId>  
    <artifactId>findbugs-maven-plugin</artifactId>  
    <version>2.5.4</version>  
    <executions>  
        <execution>  
            <goals>  
                <goal>check</goal>  
            </goals>  
        </execution>  
    </executions>  
</plugin>  
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-pmd-plugin</artifactId>  
    <version>3.1</version>  
    <executions>  
        <execution>  
            <goals>  
                <goal>check</goal>  
            </goals>  
        </execution>  
    </executions>  
</plugin>
```

　　通过执行下面的命令来运行Maven goal，包括单元测试，静态检查，如CheckStyle，FindBugs和PMD。

```
mvn verify
```

　　我们需要写很多XML来进行基本的常用的任务。在实际项目中有更多的依赖和task，Maven的pom.xml很容易就有成百上千行的配置。

#### Gradle

build.gradle

```groovy
apply plugin: 'java'  
apply plugin: 'checkstyle'  
apply plugin: 'findbugs'  
apply plugin: 'pmd'  
       
version = '1.0'  
       
repositories {  
	mavenCentral()  
}  
       
dependencies {  
	testCompile group: 'junit', name: 'junit', version: '4.11'  
    testCompile group: 'org.hamcrest', name: 'hamcrest-all', version: '1.3'  
}
```

　　Gradle不仅代码少了很多，对于熟悉的人来说，比Maven要更容易理解，而且它还包括很多Maven代码没有覆盖的有用task。请执行下面的命令来得到Gradle在当前配置下能够支持的task列表。

```
gradle tasks --all
```

### 清晰度，复杂性和学习曲线

　　对于初学者，Ant是最清晰的。只要读懂XML配置文件你就能够理解它干了些什么，但是，Ant文件很容易变复杂。Maven和Gradle，尤其是Gradle，有很多开箱即用的插件。例如，下面这行代码可能会令那些对Gradle一知半解的人有些困惑，不知道通过这句话就能够使用哪些task。

build.gradle

```groovy
apply plugin: 'java'
```

　　简单的这一行增加了20多个可用的任务.
　　Ant的可读性和Maven的简单性，在我看来，是伪命题，只适用于Gradle学习曲线的初级阶段。一旦用上了Gradle的DSL，就会发现语法比Ant和Maven更加短小精悍、易于理解。不仅如此，只有Gradle既提供了约定也提供了自定义命令。如果说Maven可以通过Ant task来扩展，这似乎有些滑稽可笑，而且低效，Groovy写的Gradle将其提升了一个层次。



**原文链接：[http://technologyconversations.com/2014/06/18/build-tools/](http://technologyconversations.com/2014/06/18/build-tools/)**
