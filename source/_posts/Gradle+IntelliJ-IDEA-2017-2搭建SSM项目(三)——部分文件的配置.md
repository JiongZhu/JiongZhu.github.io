---
title: Gradle+IntelliJ-IDEA-2017-2搭建SSM项目(三)——部分文件的配置
copyright: true
date: 2017-11-03 15:12:28
tags: [Gradle,SSM]
categories: Gradle
---

## web.xml配置

基于web3.1配置，主要包含log4j的监听与配置、编码过滤、spring监听、springMVC与mybatis配置文件的定位、错误页面配置等。
<!-- more -->

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <display-name>GradleSSMDemo</display-name>

    <!--   log4j配置文件   -->
    <context-param>
        <param-name>webAppRootKey</param-name>
        <param-value>webapp.root</param-value>
    </context-param>
    <context-param>
        <param-name>log4jConfigLocation</param-name>
        <param-value>classpath:config/log4j.properties</param-value>
        <!--   载入log4j配置文件   -->
    </context-param>
    <context-param>
        <param-name>log4jRefreshInterval</param-name>
        <param-value>60000</param-value>
        <!--  Spring刷新Log4j配置文件的间隔60秒,单位为millisecond  -->
    </context-param>

    <!--   Spring和mybatis的配置文件   -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:config/spring/spring-mybatis.xml</param-value>
    </context-param>

    <!--   编码过滤器   -->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>
            org.springframework.web.filter.CharacterEncodingFilter
        </filter-class>
        <async-supported>true</async-supported>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!-- log4j监听 -->
    <listener>
        <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
    </listener>

    <!--   Spring监听器   -->
    <listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>

    <!--   防止Spring内存溢出监听器   -->
    <listener>
        <listener-class>
            org.springframework.web.util.IntrospectorCleanupListener
        </listener-class>
    </listener>

    <!--   Spring MVC servlet   -->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:/config/spring/spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
        <async-supported>true</async-supported>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <!--   此处可以配置成*.do，对应struts的后缀习惯   -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!-- 后台程序异常错误跳转页面 -->
    <!-- 403跳转页面 -->
    <error-page>
        <error-code>403</error-code>
        <location>/html/403.htm</location>
    </error-page>

    <!-- 404跳转页面 -->
    <error-page>
        <error-code>404</error-code>
        <location>/html/404.htm</location>
    </error-page>

    <!-- 500跳转页面-->
    <error-page>
        <error-code>500</error-code>
        <location>/html/500.htm</location>
    </error-page>
    <error-page>
        <exception-type>java.lang.Exception</exception-type>
        <location>/jsp/500.jsp</location>
    </error-page>
    <error-page>
        <exception-type>java.lang.Throwable</exception-type>
        <location>/jsp/500.jsp</location>
    </error-page>
</web-app>
```

## datasource.properties配置

主要针对阿里的druid数据库连接池进行参数设置

```
#jdbc druid config
jdbc.driverClassName = com.mysql.jdbc.Driver
#需要填写磁盘上mysql jar包的具体位置
jdbc.jarDirection = E:\\Maven\\.gradle\\caches\\modules-2\\files-2.1\\mysql\\mysql-connector-java\\5.1.42\\80a448a3ec2178b649bb2e3cb3610fab06e11669\\mysql-connector-java-5.1.42.jar
jdbc.url = jdbc:mysql://127.0.0.1:3306/jiongzhu?useUnicode=true&characterEncoding=UTF-8&useSSL=false
jdbc.username = root
jdbc.password = zhxy
#定义初始连接数
jdbc.initialSize = 1
#定义最大连接数
jdbc.maxActive = 10
#定义最小空闲
jdbc.minIdle = 1
#定义最长等待时间
jdbc.maxWait = 10000
#定义间隔多久才需要检测关闭的空闲连接
jdbc.timeBetweenEvictionRunsMillis = 60000
#定义一个连接在池中的最小生存时间
jdbc.minEvictableIdleTimeMillis = 300000

jdbc.testWhileIdle = true
jdbc.testOnBorrow = true
jdbc.testOnReturn = true

#打开PSCache，并且指定每个连接上PSCache的大小
jdbc.poolPreparedStatements = true
jdbc.maxPoolPreparedStatementPerConnectionSize = 20
#配置提交方式，默认为TRUE，可以不用配置
jdbc.defaultAutoCommit = true
#配置连接有效与否的SQL,不同的数据配置不同
jdbc.validationQuery = SELECT 1
jdbc.filters = stat
```

## spring-mvc.xml配置

主要配置了控制器自动扫描、启用注解驱动、静态资源映射、springMVC注解、jsp与htm视图解析等。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描控制器 -->
    <context:component-scan base-package="com.jiongzhu.controller"/>

    <!--  添加注解驱动，扩充了注解驱动，可以将请求参数绑定到控制器参数  -->
    <mvc:annotation-driven enable-matrix-variables="true"/>
    <!-- 静态资源映射器 -->
    <mvc:resources mapping="/css/**" location="/WEB-INF/statics/css/" />
    <mvc:resources mapping="/fonts/**" location="/WEB-INF/statics/fonts/" />
    <mvc:resources mapping="/img/**" location="/WEB-INF/statics/img/" />
    <mvc:resources mapping="/js/**" location="/WEB-INF/statics/js/" />
    <mvc:resources mapping="/html/**" location="/WEB-INF/views/html/" />
    <mvc:resources mapping="/jsp/**" location="/WEB-INF/views/jsp/" />

    <!-- 配置首页访问 -->
    <mvc:view-controller path="/" view-name="index" />

    <!-- 避免IE执行AJAX时，返回JSON出现下载文件 -->
    <bean id="gsonHttpMessageConverter" class="org.springframework.http.converter.json.GsonHttpMessageConverter">
        <property name="supportedMediaTypes">
            <list>
                <value>text/html;charset=UTF-8</value>
            </list>
        </property>
    </bean>

    <!--  启动SpringMVC的注解功能，完成请求和注解POJO的映射  -->
    <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
        <property name="messageConverters">
            <list>
                <ref bean="gsonHttpMessageConverter"/>
                <!--  JSON转换器  -->
            </list>
        </property>
    </bean>

    <!-- jsp视图解析 -->
    <bean id="internalResourceViewResolver"
          class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <property name="prefix" value="/WEB-INF/views/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- html视图解析 -->
    <bean id="freeMarkerConfigurer"
          class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
        <property name="templateLoaderPath" value="/WEB-INF/views/html/" />
        <property name="freemarkerSettings" >
            <props>
                <prop key="tag_syntax">auto_detect</prop>
                <prop key="template_update_delay">60</prop>
                <prop key="default_encoding">UTF-8</prop>
                <prop key="output_encoding">UTF-8</prop>
                <prop key="locale">zh_CN</prop>
                <prop key="date_format">yyyy-MM-dd</prop>
                <prop key="time_format">HH:mm:ss</prop>
                <prop key="datetime_format">yyyy-MM-dd HH:mm:ss</prop>
                <prop key="number_format">#.##</prop>
                <prop key="classic_compatible">true</prop>
                <prop key="template_exception_handler">ignore</prop>
            </props>
        </property>
    </bean>
    <bean id="freeMarkerViewResolver"
          class="org.springframework.web.servlet.view.freemarker.FreeMarkerViewResolver">
        <property name="contentType" value="text/html;charset=UTF-8"/>
        <property name="exposeRequestAttributes" value="true" />
        <property name="exposeSessionAttributes" value="true" />
        <property name="cache" value="true" />
        <property name="suffix" value=".html" />
        <property name="order" value="0"/>
    </bean>
</beans>
```

## spring-mybatis.xml配置

主要配置了service的自动扫描、数据库连接池的配置、mybatis实体接口与实体映射文件的扫描等。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <!--  自动扫描  -->
    <context:component-scan base-package="com.jiongzhu.service"/>
    <!--  引入配置文件  -->
    <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="location" value="classpath:config/datasource.properties"/>
    </bean>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="driverClassName" value="${jdbc.driverClassName}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <!--  初始化连接大小  -->
        <property name="initialSize" value="${jdbc.initialSize}"/>
        <!--  连接池最大数量  -->
        <property name="maxActive" value="${jdbc.maxActive}"/>
        <!--  连接池最小空闲  -->
        <property name="minIdle" value="${jdbc.minIdle}"/>
        <!--  获取连接最大等待时间  -->
        <property name="maxWait" value="${jdbc.maxWait}"/>
        <!--  配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒  -->
        <property name="timeBetweenEvictionRunsMillis" value="${jdbc.timeBetweenEvictionRunsMillis}"/>
        <!--  配置一个连接在池中最小生存的时间，单位是毫秒  -->
        <property name="minEvictableIdleTimeMillis" value="${jdbc.minEvictableIdleTimeMillis}"/>
        <property name="testWhileIdle" value="${jdbc.testWhileIdle}"/>
        <!--  这里建议配置为TRUE，防止取到的连接不可用  -->
        <property name="testOnBorrow" value="${jdbc.testOnBorrow}"/>
        <property name="testOnReturn" value="${jdbc.testOnReturn}"/>
        <!--  打开PSCache，并且指定每个连接上PSCache的大小  -->
        <property name="poolPreparedStatements" value="${jdbc.poolPreparedStatements}"/>
        <property name="maxPoolPreparedStatementPerConnectionSize" value="${jdbc.maxPoolPreparedStatementPerConnectionSize}"/>
        <!--  这里配置提交方式，默认就是TRUE，可以不用配置  -->
        <property name="defaultAutoCommit" value="${jdbc.defaultAutoCommit}"/>
        <!--  验证连接有效与否的SQL，不同的数据配置不同  -->
        <property name="validationQuery" value="${jdbc.validationQuery}"/>
        <property name="filters" value="${jdbc.filters}"/>
        <property name="proxyFilters">
            <list>
                <ref bean="logFilter"/>
            </list>
        </property>
    </bean>
    <bean id="logFilter" class="com.alibaba.druid.filter.logging.Slf4jLogFilter">
        <property name="statementExecutableSqlLogEnable" value="false"/>
    </bean>
    <!-- TKmybatis自动扫描注册Mybatis mapper,当使用一个以上的DataSource时可能需要配置<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" /> -->
    <bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.jiongzhu.mapper"/>
    </bean>
    <!--  spring和MyBatis完美整合，不需要mybatis的配置映射文件  -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--  自动扫描mapper.xml文件  -->
        <property name="mapperLocations" value="classpath*:config/mapper/*.xml"/>
    </bean>
    <!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

## gradle.properties配置

mybatis实体类、实体接口、实体映射文件等包路径的参数配置，需要自动生产的数据库表的配置等，使用到basemapper插件。

```
#自定义basemapper插件
mapper.plugin=tk.mybatis.mapper.generator.MapperPlugin
#自定义basemapper接口
mapper.mapper=tk.mybatis.mapper.common.Mapper
# 生成的model类所在包
modelPackage=com.jiongzhu.model
# 生成的mapper接口类所在包
mapperPackage=com.jiongzhu.mapper
# 生成的mapper xml文件所在包
sqlMapperPackage=config.mapper
# 数据库表
gererator.tableName=user
# 生成的实体类
gererator.objectName=User
# 数据库
gererator.schema=jiongzhu
```

## generatorConfig.xml配置

mybatisGenerator的具体配置，关于mybatisGenerator相关运用可以自行百度。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!-- mysql数据库jar包 -->
    <classPathEntry location="${jarDirection}"/>
    <!-- 一个数据库一个context -->
    <context id="MysqlContext" targetRuntime="MyBatis3Simple" defaultModelType="flat">
        <!-- 生成文件的编码 (eclipse插件的时候这里并没有什么卵用，需要在eclipse根目录的eclipse.ini最后添加 -Dfile.encoding=UTF-8  )-->
        <property name="javaFileEncoding" value="UTF-8"/>
        <!-- 当表名或者字段名为SQL关键字的时候，可以设置该属性为true，MBG会自动给表名或字段名添加**分隔符**  -->
        <property name="autoDelimitKeywords" value="true"/>
        <!-- 由于beginningDelimiter和endingDelimiter的默认值为双引号(")，在Mysql中不能这么写，所以还要将这两个默认值改为**反单引号(`)** -->
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <!-- 通过插件生成自定义basemapper接口 -->
        <plugin type="${plugin}">
            <property name="mappers" value="${mapper}"/>
        </plugin>

        <!-- 注释 -->
        <commentGenerator>
            <!-- 是否取消注释 -->
            <property name="suppressAllComments" value="false"/>
            <property name="addRemarkComments" value="true"/>
            <!-- 是否取消时间戳 -->
            <property name="suppressDate" value="true" />
        </commentGenerator>

        <!-- jdbc连接 -->
        <jdbcConnection driverClass="${driverClass}"
                        connectionURL="${connectionURL}"
                        userId="${userId}"
                        password="${password}">
        </jdbcConnection>

        <!-- 类型转换 -->
        <javaTypeResolver >
            <!-- 是否使用bigDecimal， false可自动转化以下类型（Long, Integer, Short, etc.） -->
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>

        <!-- 生成实体类地址 --> 
        <javaModelGenerator targetPackage="${modelPackage}"
                              targetProject="${src_main_java}">
            <!-- 是否在当前路径下新加一层schema,eg：fase路径com.zhxy.model， true:com.zhxy.model.[schemaName] -->
            <property name="enableSubPackages" value="false" />
            <!-- 是否针对string类型的字段在set的时候进行trim调用 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        
        <!-- 生成mapper xml文件 --> 
        <sqlMapGenerator targetPackage="${sqlMapperPackage}"  
                          targetProject="${src_main_resources}">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>

        <!-- 生成mapper xml对应client，也就是接口dao -->
        <javaClientGenerator type="XMLMAPPER"
        targetPackage="${mapperPackage}"  
        targetProject="${src_main_java}">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>

        <!-- 列出要生成代码的所有表，这里配置的是不生成Example文件 -->
        <!-- schema即为数据库名 tableName为对应的数据库表 domainObjectName是要生成的实体类
        enable*ByExample是否生成 example类   -->
        <table tableName="${tableName}" domainObjectName="${objectName}"
               schema="${schema}">
            <!-- 忽略列，不生成bean 字段
            <ignoreColumn column="FRED" />-->
            <!-- 指定列的java数据类型 -->
            <!-- <columnOverride column="LONG_VARCHAR_FIELD" jdbcType="VARCHAR" />  -->
            <!-- 用于指定生成实体类时是否使用实际的列名作为实体类的属性名。默认false是 Camel Case风格-->
            <!--<property name="useActualColumnNames" value="false" />-->
            <generatedKey column="id" sqlStatement="Mysql" identity="true"/>
        </table>
    </context>
</generatorConfiguration>
```

## 自动生成的User.java、UserMapper.java、UserMapper.xml展示

首先是User.java实体类，手动加入了lombok，代码得到简化，关于lombok使用可以自行百度，比如这里我在类上使用了lombok包的@Getter、@Setter注解，则在编译生成.class文件时会对类下的所有成员变量自动生产set与get方法。

```java
package com.jiongzhu.model;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

@Getter
@Setter
public class User {
    @Id
    @Column(name = "Id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    @Column(name = "userNo")
    @NotNull(message = "账号不能为空！")
    @Size(min = 10, max = 10,message = "账号长度为10个字符！")
    private String userno;

    @Column(name = "userName")
    private String username;

    @Column(name = "userPass")
    @NotNull(message = "密码不能为空！")
    @Size(min = 4,max = 8,message = "密码长度为4-8个字符！")
    private String userpass;

    private String sex;
    private Integer age;
    private String phone;
    private String address;
}
```

然后是UserMapper.java接口，继承自通用Mapper，通用Mapper中包含了许多常用的增删改查与统计方法。

```java
package com.jiongzhu.mapper;

import com.jiongzhu.model.User;
import tk.mybatis.mapper.common.Mapper;

public interface UserMapper extends Mapper<User> {
}
```

最后是UserMapper.xml实体映射文件，

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.jiongzhu.mapper.UserMapper">
  <resultMap id="BaseResultMap" type="com.jiongzhu.model.User">
    <!--
      WARNING - @mbg.generated
    -->
    <id column="Id" jdbcType="INTEGER" property="id" />
    <result column="userNo" jdbcType="VARCHAR" property="userno" />
    <result column="userName" jdbcType="VARCHAR" property="username" />
    <result column="userPass" jdbcType="VARCHAR" property="userpass" />
    <result column="sex" jdbcType="CHAR" property="sex" />
    <result column="age" jdbcType="INTEGER" property="age" />
    <result column="phone" jdbcType="VARCHAR" property="phone" />
    <result column="address" jdbcType="VARCHAR" property="address" />
  </resultMap>
</mapper>
```

## BaseService.java配置

结合通用mapper接口自行编写，使用起来非常方便。

```java
package com.jiongzhu.service;

import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.session.RowBounds;
import org.springframework.beans.factory.annotation.Autowired;
import tk.mybatis.mapper.common.Mapper;

import java.util.List;

public abstract class BaseService<T>{

    private Mapper<T> mapper;

    @Autowired
    public void setMapper(Mapper<T> mapper) {
        this.mapper = mapper;
    }

    /**
     * 根据实体属性作为条件进行删除，查询条件使用等号
     *
     * @param record 参数
     * @return 返回值
     */
    public int delete(T record){
        return mapper.delete(record);
    }
    /**
     * 根据主键字段进行删除，方法参数必须包含完整的主键属性
     *
     * @param key 参数
     * @return 返回值
     */
    public int deleteByPrimaryKey(Object key){
        return mapper.deleteByPrimaryKey(key);
    }
    /**
     * 保存一个实体，null的属性也会保存，不会使用数据库默认值
     *
     * @param record 参数
     * @return 返回值
     */
    public int insert(T record){
        return mapper.insert(record);
    }
    /**
     * 保存一个实体，null的属性不会保存，会使用数据库默认值
     *
     * @param record 参数
     * @return 返回值
     */
    public int insertSelective(T record){
        return mapper.insertSelective(record);
    }
    /**
     * 根据主键字段查询总数，方法参数必须包含完整的主键属性，查询条件使用等号
     *
     * @param key 参数
     * @return 返回值
     */
    public boolean existsWithPrimaryKey(Object key){
        return mapper.existsWithPrimaryKey(key);
    }
    /**
     * 查询全部结果
     *
     * @param () 无参数
     * @return 返回值
     */
    public List<T> selectAll(){
        return mapper.selectAll();
    }
    /**
     * 根据主键字段进行查询，方法参数必须包含完整的主键属性，查询条件使用等号
     *
     * @param key 参数
     * @return 返回值
     */
    public T selectByPrimaryKey(Object key){
        return mapper.selectByPrimaryKey(key);
    }
    /**
     * 根据实体中的属性查询总数，查询条件使用等号
     *
     * @param record 参数
     * @return 返回值
     */
    public int selectCount(T record){
        return mapper.selectCount(record);
    }
    /**
     * 根据实体中的属性值进行查询，查询条件使用等号
     *
     * @param record 参数
     * @return 返回值
     */
    public List<T> select(T record){
        return mapper.select(record);
    }
    /**
     * 根据实体中的属性进行查询，只能有一个返回值，有多个结果是抛出异常，查询条件使用等号
     *
     * @param record 参数
     * @return 返回值
     */
    public T selectOne(T record){
        return mapper.selectOne(record);
    }
    /**
     * 根据主键更新实体全部字段，null值会被更新
     *
     * @param record 参数
     * @return 返回值
     */
    public int updateByPrimaryKey(T record){
        return mapper.updateByPrimaryKey(record);
    }
    /**
     * 根据主键更新属性不为null的值
     *
     * @param record 参数
     * @return 返回值
     */
    public int updateByPrimaryKeySelective(T record){
        return mapper.updateByPrimaryKeySelective(record);
    }
    /**
     * 根据Example条件删除数据
     *
     * @param example 参数
     * @return 返回值
     */
    public int deleteByExample(Object example){
        return mapper.deleteByExample(example);
    }
    /**
     * 根据Example条件进行查询
     *
     * @param example 参数
     * @return 返回值
     */
    public List<T> selectByExample(Object example){
        return mapper.selectByExample(example);
    }
    /**
     * 根据Example条件进行查询总数
     *
     * @param example 参数
     * @return 返回值
     */
    public int selectCountByExample(Object example){
        return mapper.selectCountByExample(example);
    }
    /**
     * 根据Example条件更新实体`record`包含的全部属性，null值会被更新
     *
     * @param record 参数1
     * @param example 参数2
     * @return 返回值
     */
    public int updateByExample(@Param("record") T record, @Param("example") Object example){
        return mapper.updateByExample(record,example);
    }
    /**
     * 根据Example条件更新实体`record`包含的不是null的属性值
     *
     * @param record 参数1
     * @param example 参数2
     * @return 返回值
     */
    public int updateByExampleSelective(@Param("record") T record, @Param("example") Object example){
        return mapper.updateByExampleSelective(record, example);
    }
    /**
     * 根据example条件和RowBounds进行分页查询
     *
     * @param example 参数1
     * @param rowBounds 参数2
     * @return 返回值
     */
    public List<T> selectByExampleAndRowBounds(Object example, RowBounds rowBounds){
        return mapper.selectByExampleAndRowBounds(example, rowBounds);
    }
    /**
     * 根据实体属性和RowBounds进行分页查询
     *
     * @param record 参数1
     * @param rowBounds 参数2
     * @return 返回值
     */
    public List<T> selectByRowBounds(T record, RowBounds rowBounds){
        return mapper.selectByRowBounds(record, rowBounds);
    }
}

```

下面展示一下，UserService.java内容，当继承了通用service之后，已经继承了所有的通用方法，当然自己可以重写任意方法和自己添加父类没有实现的方法。

```java
package com.jiongzhu.service;

import com.jiongzhu.model.User;
import org.springframework.stereotype.Service;

@Service(value = "userService")
public class UserService extends BaseService<User>{
    @Override
    public User selectOne(User record) {
        return super.selectOne(record);
    }
}

```

## log4j.properties配置

本项目使用到的日志系统为log4j，具体配置信息如下：

```
### log4j\u8BBE\u7F6E### log4j.rootLogger = debug,stdout,D,E,W,I
log4j.rootLogger = debug,D,E,W,I

### \u8F93\u51FA\u4FE1\u606F\u5230\u63A7\u5236\u62AC ### \u4E0D\u63A8\u8350\uFF0C\u8981\u517B\u6210\u770B\u65E5\u5FD7\u6587\u4EF6\u7684\u4E60\u60EF
#log4j.appender.stdout = org.apache.log4j.ConsoleAppender
#log4j.appender.stdout.Target = System.out
#log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
#log4j.appender.stdout.layout.ConversionPattern = [%d{yyyy-MM-dd HH:mm:ss a}]:%p %l%m%n

### \u8F93\u51FADEBUG \u7EA7\u522B\u4EE5\u4E0A\u7684\u65E5\u5FD7\u5230${webapp.root}/logs/debug.log ###
log4j.logger.debug = D
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = ${webapp.root}/logs/debug.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG
log4j.appender.D.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n

### \u8F93\u51FAERROR \u7EA7\u522B\u4EE5\u4E0A\u7684\u65E5\u5FD7\u5230${webapp.root}/logs/error.log ###
log4j.logger.error = E
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File =${webapp.root}/logs/error.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR
log4j.appender.E.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n

### \u8F93\u51FAWARN \u7EA7\u522B\u4EE5\u4E0A\u7684\u65E5\u5FD7\u5230${webapp.root}/logs/warn.log ###
log4j.logger.warn = W
log4j.appender.W = org.apache.log4j.DailyRollingFileAppender
log4j.appender.W.File = ${webapp.root}/logs/warn.log
log4j.appender.W.Append = true
log4j.appender.W.Threshold = WARN
log4j.appender.W.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.W.layout = org.apache.log4j.PatternLayout
log4j.appender.W.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n

### \u8F93\u51FAINFO \u7EA7\u522B\u4EE5\u4E0A\u7684\u65E5\u5FD7\u5230${webapp.root}/logs/info.log ###
logger.logger.info = I
log4j.appender.I = org.apache.log4j.DailyRollingFileAppender
log4j.appender.I.File =${webapp.root}/logs/info.log
log4j.appender.I.Append = true
log4j.appender.I.Threshold = INFO
log4j.appender.I.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.I.layout = org.apache.log4j.PatternLayout
log4j.appender.I.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n

### mybatis\u6253\u5370sql\u8BED\u53E5,\u6307\u5B9Amapper\u914D\u7F6E\u6587\u4EF6\u4E2D\u7684namespace<-mapperNS
#log4j.logger.mapperNS =TRACE
```

到这里，所有的配置文件内容已经展示完毕，在我的环境中可以正常使用。
