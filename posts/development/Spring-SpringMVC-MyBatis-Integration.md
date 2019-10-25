---
title: Spring+SpringMVC+MyBatis整合
date: 2017-05-11 02:20:03
tags: [Java,Web,SSM,Spring,Spring MVC,MyBatis]
categories: Server
---

## Spring + Spring MVC + MyBatis_SSM 框架整合

##### 最近为了写MaomaoChat，开始折腾Java Web的框架：Spring + Spring MVC + MyBatis

### 0. Intro of SSM：

**Spring:**

> 个人理解：
>
> 进行我们项目中的对象进行管理，关联，业务分层解耦的一个中间层框架。
>
> 优势：
>
> Spring的功能极其强大，尤其是将Spring与其他框架整合时，可以让我们非常省心的专注于业务逻辑的实现

**Spring MVC:**

>个人理解：
>
>Spring的子框架，对Java Web 进行 MVC 模式分层的一个框架。
>
>优势：
>
>是我们以极其简单的方式进行 MVC 模式分层，比用原生的Servlet进行开发要容易得多。

**MyBatis:**

>个人理解：
>
>说道MyBatis就不得不说Hibernate，作为Java 数据库持久层的两大框架，MyBatis 和 Hibernate各有优势；
>
>Hibernate是全自动的框架，利用Hibernate，我们可以只书写极其少量的关乎业务逻辑的SQL(其实是HQL)代码就可以完成对数据库的操作。简单的增删改查我们只需要调用Hibernate提供的接口即可。
>
>Mybatis是半自动的框架，利用它我们可以对SQL语句进行封装，减少SQL语句的重复编写。
>
>优势：
>
>也因为上述特性，导致Hibernate的体量比较大，当对数据库的操作极其频繁，数据量又大的时候，Hibernate会比MyBatis慢一些。但是中小项目使用Hibernate是完全没有问题的。



### 1. Configuration Of SSM

#### 1.0 Prepare

*如果你只想使用 SSM 框架，并不想自己完成整个配置的话，可以直接 git clone 我上传至Github的SSM框架项目模板，然后根据 ReadMe 进行一些属性的修改即可开始编写你的项目。*

**Git Clone 地址：git@github.com:JahoJiang/SSM_Template.git**

首先导入所有的Jar，包括 ：Spring、Spring MVC、MyBatis、C3P0(数据库连接池)、MyBatis-Spring(将MyBatis与Spring进行整合)

如果使用IDEA进行项目构建，构建时勾选 Spring MVC，之后在 Project Structure -> Libraries -> From Maven中搜索并添加MyBatis与MyBatis Spring即可。

*本项目添加的Jar包版本为：*

**MyBatis-3.3.0, MyBatis-Spring-1.3.0, C3P0-0.9.5.2**

#### 1.1 Configure

添加好我们所需的Jar包，现在开始进行配置。

在src目录下建立Configuration目录放置我们所有的配置文件。

我们将配置文件进行分层，创建如下文件:

* jdbc.properties — 存储进行数据库连接的变量，包括数据库URL、UserName、PassWord、JDBC Driver
* mybatis-config.xml, 对MyBatis进行配置
* spring-dao.xml, Spring管理DAO层Bean的配置文件
* spring-service.xml, Spring管理Service层Bean的配置文件
* spring-mvc.xml, 对Spring MVC进行配置

接下来逐个文件进行配置：

##### 1.1.0 jdbc.properties 

**完整配置文件**

```properties
#MySQL-JDBC-Connector-Configuration
jdbc.driver = com.mysql.cj.jdbc.Driver
jdbc.url = jdbc:mysql://localhost:3306/MyDataBase?useUnicode=true&characterEncoding=utf8&useSSL=true
jdbc.username = root
jdbc.password = 1234
```

在这里我们定义了四个变量，分别是jdbc.driver，jdbc.url、jdbc.username、jdbc.password、我们将其剥离，用于对数据库相关的配置，从而在后续进行这些属性的修改时，只需要修改jdbc.properties文件中这几个变量，而不需要查找所有相关文件并进行修改。

##### 1.1.1 spring-dao.xml

这是我们需要进行详细配置的文件，是主要工作量，各行配置的功能注释里已经做了说明。

我们说过Spring可以对我们项目中的Bean对象进行管理。

而使用数据持久层框架，我们最麻烦的即是对Session的管理，因此使用Spring对我们的数据库连接池类、MyBatis的Session类进行管理。

配置文件中参数的说明：

*bean*标签添加我们需要Spring进行管理的Java Bean

其中：*id*是Java Bean实例名，*class*是Java Bean的对应的类

*property*标签说明我们需要Spring管理的Java Bean的属性的值。

**例如下面的配置，即表明,我们需要Spring为我们new一个Template类，这个实例名称为template，这个类具体是是jaho包中的Template类，有一个属性叫做name，并且将name属性的值设置为jaho**

```xml
<bean id="template" class="jaho.Template">
<property name="name">
            <value>jaho</value>
</property>
</bean>
```

**请一定要注意xml配置文件的文件头，如果文件头信息不正确，也会导致出错**

**完整配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 整合mybatis过程 -->

    <!-- 1.配置数据库相关参数properties -->
    <context:property-placeholder location="classpath:Configuration/jdbc.properties"/>

    <!-- 2.数据库连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource" destroy-method="close">

        <property name="driverClass">
            <value>${jdbc.driver}</value>
        </property>

        <property name="jdbcUrl">
            <value>${jdbc.url}</value>
        </property>

        <property name="user">
            <value>${jdbc.username}</value>
        </property>

        <property name="password">
            <value>${jdbc.password}</value>
        </property>

        <!--连接池中保留的最大连接数。Default: 15 -->
        <property name="maxPoolSize">
            <value>30</value>
        </property>

        <!--初始化时获取的连接数，取值应在minPoolSize与maxPoolSize之间。Default: 3 -->
        <property name="initialPoolSize">
            <value>10</value>
        </property>

        <!--最大空闲时间,60秒内未使用则连接被丢弃。若为0则永不丢弃。Default: 0 -->
        <property name="maxIdleTime">
            <value>60</value>
        </property>

        <!--当连接池中的连接耗尽的时候c3p0一次同时获取的连接数。Default: 3 -->
        <property name="acquireIncrement">
            <value>5</value>
        </property>

    </bean>

    <!-- 3.配置SqlSessionFactory对象 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">

        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>

        <!-- 配置MyBatis全局配置文件:mybatis-config.xml -->
        <property name="configLocation" value="classpath:Configuration/mybatis-config.xml"/>

        <!-- 扫描entity包 使用别名 -->
        <property name="typeAliasesPackage" value="Dao.Entity"/>

        <!-- 扫描sql配置文件:mapper需要的xml文件 -->
      	<!-- 在此扫描所以Mapper文件
		以保证这些Mapper在使用Session时可以被Spring管理的Bean扫描到-->
        <property name="mapperLocations" value="classpath:Dao/Mapper/*.xml"/>

    </bean>

    <!-- 4.配置扫描Dao接口包，动态实现Dao接口，注入到spring容器中 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">

        <!-- 注入sqlSessionFactory -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>

        <!-- 给出需要扫描Dao接口的包 -->
        <property name="basePackage" value="Dao"/>
      
    </bean>
  
</beans>
```



##### 1.1.2 mybatis-config.xml

这里的MyBatis配置文件就比较简单了，因为我们在上面的**spring-dao.xml**中已经通过数据库连接池等，对Mybatis进行了足够的配置。

**若只需要使用Mybatis而不需要使用其他框架，可以参考另一篇博客：MyBatis基础配置和使用**



**完整配置文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!-- 配置全局属性 -->
    <settings>
        <!-- 使用jdbc的getGeneratedKeys获取数据库自增主键值 -->
        <setting name="useGeneratedKeys" value="true" />

        <!-- 使用列别名替换列名 默认:true -->
        <setting name="useColumnLabel" value="true" />

        <!-- 开启驼峰命名转换:Table{create_time} -> Entity{createTime} -->
        <setting name="mapUnderscoreToCamelCase" value="true" />

    </settings>

</configuration>
```



##### 1.1.3 spring-mvc.xml

这里的配置也比较简单

* SpringMVC注解模式允许我们使用注解进行类的作用的定义，具体使用后面会讲到
* mvc:default-servlet-handler，Spring MVC的静态资源处理器，过滤静态资源请求
* ViewResolver，过滤View请求也即是JSP页面请求，并分发给对应Controller进行处理
* 扫描bean，在指定目录下扫描我们需要Spring处理的使用了注解的Bean

**完整配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置SpringMVC -->

    <!-- 1.开启SpringMVC注解模式 -->
    <mvc:annotation-driven />

    <!-- 2.静态资源默认servlet配置 -->
    <mvc:default-servlet-handler/>

    <!-- 3.配置jsp 显示ViewResolver -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>

    <!-- 4.扫描web相关的bean -->
    <context:component-scan base-package="Controller" />
</beans>
```



##### 1.1.4 spring-service.xml

**完整配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- 引入spring-dao.xml文件，使用其中的Java Bean -->
    <import resource="spring-dao.xml"/>

    <!-- 扫描service包下所有使用注解的类型 -->
    <context:component-scan base-package="Service"/>
    <context:component-scan base-package="Controller"/>

    <!-- 配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置基于注解的声明式事务 -->
    <tx:annotation-driven transaction-manager="transactionManager" proxy-target-class="true"/>

</beans>
```



##### 1.1.5 web.xml

**完整配置文件**

* *context-param*标签为Spring提供上下文环境信息，在此配置为我们所有的spring相关文件的位置

* *DispatcherServlet*Spring MVC的工作原理，使用*DispatcherServlet*这个类，拦截我们需要它拦截的请求，并且将这些请求分发给Spring MVC的Controller，使用Controller进行请求的处理

  在此配置为*<url-pattern>/</url-pattern>*表示使用DispatcherServlet拦截所有请求

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
  
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:Configuration/spring-*.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <description>spring mvc 配置文件</description>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:Configuration/spring-*.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>

    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```



### 2. Usage

接下来是使用：

#### 2.0 MyBatis

##### 2.0.0 Entity

首先我们需要编写数据库中一个表对应的Entity。

**示例为User**

```java
package Dao.Entity;

import java.sql.Date;

/**
 * Created by Jaho on 2017/5/8.
 */

public class User {

    private long user_id;
    private String password;
    private String phone;
    private String name;
    private String avatar;
    private boolean isMale;
    private String token;
    private Date expired_date;

    public String getToken() {
        return token;
    }
	
  /*
   *省略了所有的getter和setter
   */
}
```



##### 2.0.1 Mapper

MyBatis中使用Mapper进行Entity与数据库的实际交互，一个Entity对应一个Mapper的接口和一个实现。

这个实现可以是通过XML文件，也可以是通过注解的方式，MyBatis官方文档说注解实现的功能相比XML要少一些，因此在此示例使用XML文件。

**UserMapper Interface**

```java
package Dao.Mapper;

import Dao.Entity.User;
import java.util.List;

/**
 * Created by Jaho on 2017/5/8.
 */

public interface UserMapper {

    public User getUserById(long user_id);

    public User getUserByPhone(String phone);

    public List<User> getAllUser();

    public void insert(User user);

    public void delete(User user);

    public void update(User user);

    public void updateExpiredDate(User user);

}
```

**UserMapper Interface Implement**

在这里为Mapper Interface中每一个方法进行SQL代码的实现，注意，标签中的 id属性一定要和Mapper Interface中方法名匹配。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper  namespace="Dao.Mapper.UserMapper">
    <insert id="insertUser" parameterType="User" useGeneratedKeys="true" keyColumn="user_id">
        INSERT INTO "user"(phone, name, password, avatar, isMale, token, expired_date) VALUES (#{phone},#{name},#{password},#{avatar},#{isMale}, #{token},#{expired_date})
    </insert>

    <update id="updateUser" parameterType="User">
        UPDATE user
        SET phone = #{phone},name = #{name},password = #{password},avatar = #{avatar},isMale = #{isMale},token = #{token}, expired_date = #{expired_date}
        WHERE user_id = #{user_id}
    </update>

    <update id="updateExpiredDate" parameterType="User">
        UPDATE user
        SET  expired_date = #{expired_date}
        WHERE user_id = #{user_id}
    </update>

    <select id="getUserById" parameterType="long" resultType="User">
        select * from user where user_id = #{user_id}
    </select>

    <select id="getUserByPhone" parameterType="String" resultType="User">
        select * from user where phone = #{phone}
    </select>

    <delete id="deleteUser" parameterType="long">
        delete from user where user_id = #{user_id}
    </delete>

</mapper>
```



##### 2.0.2 Usage

编写一个单元测试进行测试

```java
/**
 * Created by Jaho on 2017/5/8.
 */

import org.junit.Test;
import Dao.Entity.User;
import Dao.Mapper.UserMapper;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;



/**
 * 配置spring和junit整合，junit启动时加载springIOC容器 spring-test,junit
 */

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration({"classpath:Configuration/spring-dao.xml"})
public class UserTest {

  	// 使用 Spring 为我们自动注入 UserMapper，不需要我们自己进行new等操作
    @Autowired
    private UserMapper userMapper;

    @Test
    public void testGetUserById() throws Exception {
        long user_id = 1;
        User user = userMapper.getUserById(user_id);
        System.out.println(user.getName());
    }
}
```

User表中对于数据：

| id   | phone       | name | isMale | token            | expired_date |
| ---- | ----------- | ---- | ------ | ---------------- | ------------ |
| 6    | 01234567890 | Jaho | 1      | u50tlxb0036to26u | 2017-06-10   |

测试输出

```
Jaho
```



#### 2.1 Spring MVC

编写Controller，并且与Mybatis交互

**完整示例Controller**

* @Controller - Spring MVC 注解，表明这是一个Controller类
* @CrossOrigin 允许跨域访问(非常重要)
* @RequestMapping("/Hello")，表明这个Controller处理所有/Hello相关的请求
* @ResponseBody 表明这个方法对Response进行设置，用来返回JSON数据
* @RequestMapping(value = "/User", method = RequestMethod.GET)表明这个方法对应的请求是“/Hello/User”，处理的请求方法是GET
* @RequestParam("id") int id ，从请求中获取名为id的参数，并转为int类型的名为id的变量

```java
package Controller;

import Dao.Entity.User;
import Dao.Mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

/**
 * Created by Jaho on 2017/5/8.
 */
@Controller
@CrossOrigin
@RequestMapping("/Hello")
public class HelloSpring {

    @Autowired
    private UserMapper userMapper;

    @ResponseBody
    @RequestMapping(value = "/User", method = RequestMethod.GET)
    public String helloUser(@RequestParam("id") int id) throws Exception {
        long user_id = id;
        User user = userMapper.getUserById(user_id);
        System.out.println(user.getName());
        return "Hello\t" + user.getName();
    }

	// 上述代码：通过GET请求/Hello/User，将会输出Hello + 数据库User表中对应id为请求的参数id的用户名
}
```



运行Tomcat，访问*http://localhost:8080/SSM_Template/Hello/User?id=6*

输出

```
Hello Jaho
```



**至此，SSM框架已经搭建完成并且可以使用，特此感谢毛兄允许我慢慢折腾后端**

