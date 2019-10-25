---
title: MyBatis基础配置和使用
date: 2017-05-10 17:11:31
tags: [Java,Web,MyBatis]
categories: Server
---

## MyBatis基础配置和使用

本文介绍如何配置并使用MyBatis

### 0.Prepare

导入MyBatis Jar包，示例使用 MyBatis-3.3.0

### 1. Configure

建立jdbc.properties

建立mybatis-config.xml

先说思路，Mybatis是数据持久层框架，使用Mapper进行Java Entitiy和SQL Table的映射，因此我们需要配置数据库链接相关的数据以及注册Mapper。

**配置文件及说明如下:**

#### 1.0 jdbc.properties

```properties
#MySQL-JDBC-Connector-Configuration
jdbc.driver = com.mysql.cj.jdbc.Driver
jdbc.url = jdbc:mysql://localhost:3306/MyDatabase?useUnicode=true&characterEncoding=utf8&useSSL=true
jdbc.username = root
jdbc.password = 1234
```

在这里我们定义了四个变量，分别是jdbc.driver，jdbc.url、jdbc.username、jdbc.password、我们将其剥离，用于对数据库相关的配置，从而在后续进行这些属性的修改时，只需要修改jdbc.properties文件中这几个变量，而不需要查找所有相关文件并进行修改。

**配置文件及说明如下:**

#### 1.1 mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

  	<!-- 引入我们的jdbc.properties文件，在此路径为Configuration目录下 -->
    <properties resource="Configuration/jdbc.properties" />

  	<!-- MyBatis 一些功能的开关和配置-->
  	<!-- 各项配置项说明来自官方文档,具体根据需要进行配置，不做配置则默认使用MyBatis默认配置-->
    <settings>
      	<!-- 该配置影响的所有映射器Mapper中配置的缓存的全局开关 -->
        <setting name="cacheEnabled" value="true" />
      
      	<!-- 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。
 		特定关联关系中可通过设置fetchType属性来覆盖该项的开关状态。 -->
        <setting name="lazyLoadingEnabled" value="true" />
      
      	<!-- 当开启时，任何方法的调用都会加载该对象的所有属性。
		否则，每个属性会按需加载 -->
      	<setting name="aggressiveLazyLoading" value="true" />
      
      	<!-- 是否允许单一语句返回多结果集（需要兼容驱动）-->
        <setting name="multipleResultSetsEnabled" value="true" />
      	
      	<!-- 使用列标签代替列名。不同的驱动在这方面会有不同的表现.
		具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。-->
        <setting name="useColumnLabel" value="true" />
      
      	<!-- 允许 JDBC 支持自动生成主键，需要驱动兼容。 
		如果设置为 true 则这个设置强制使用自动生成主键 -->
        <setting name="useGeneratedKeys" value="false" />
      
      	<!-- 指定 MyBatis 应如何自动映射列到字段或属性。 
		NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 
		FULL 会自动映射任意复杂的结果集（无论是否嵌套）。 -->
        <setting name="autoMappingBehavior" value="PARTIAL" />
      
      	<!-- 指定发现自动映射目标未知列（或者未知属性类型）的行为。
		NONE: 不做任何反应
		WARNING: 输出提醒日志 ('org.apache.ibatis.session.AutoMappingUnknownColumnBehavior' 			的日志等级必须设置为 WARN)
		FAILING: 映射失败 (抛出 SqlSessionException) -->
        <setting name="autoMappingUnknownColumnBehavior" value="WARNING" />
      
      	<!-- 配置默认的执行器。
		SIMPLE 就是普通的执行器；
		REUSE 执行器会重用预处理语句（prepared statements）； 
		BATCH 执行器将重用语句并执行批量更新。 -->
        <setting name="defaultExecutorType" value="SIMPLE" />
      
      	<!-- 设置超时时间，它决定驱动等待数据库响应的秒数。 -->
        <setting name="defaultStatementTimeout" value="25" />
      
      	<!-- 为驱动的结果集获取数量（fetchSize）设置一个提示值。此参数只可以在查询设置中被覆盖。 -->
      	<setting name="defaultFetchSize" value="25" />
      
      	<!-- 允许在嵌套语句中使用分页（RowBounds）。 
			If allow, set the false. -->
        <setting name="safeRowBoundsEnabled" value="false" />
      
      	<!-- 允许在嵌套语句中使用分页（ResultHandler）。 If allow, set the false -->
      	<setting name="safeResultHandlerEnabled" value="false" />
      
      	<!-- 是否开启自动驼峰命名规则（camel case）映射
		即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射 -->
        <setting name="mapUnderscoreToCamelCase" value="false" />
      
      	<!-- MyBatis 利用本地缓存机制（Local Cache）
		防止循环引用（circular references）和加速重复嵌套查询。 
		默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。 
		若设置值为 STATEMENT，本地会话仅用在语句执行上
		对相同 SqlSession 的不同调用将不会共享数据。 -->
        <setting name="localCacheScope" value="SESSION" />
      
      	<!-- 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 
		某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 -->
        <setting name="jdbcTypeForNull" value="equals,clone,hashCode,toString" />
      
      	<!-- 指定动态 SQL 生成的默认语言 -->
        <setting name="defaultScriptingLanguage" 							  		  value="org.apache.ibatis.scripting.xmltags.XMLLanguageDriver" />
      
      	<!-- 指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法
		这对于有 Map.keySet() 依赖或 null 值初始化的时候是有用的。
		注意基本类型（int、boolean等）是不能设置成 null 的。 -->
        <setting name="callSettersOnNull" value="false" />
      
      	<!-- 当返回行的所有列都是空时，MyBatis默认返回null。 
		当开启这个设置时，MyBatis会返回一个空实例。 
		请注意，它也适用于嵌套的结果集 (i.e. collectioin and association)。-->
        <setting name="returnInstanceForEmptyRow" value="false" />
      
      	<!-- 指定 MyBatis 增加到日志名称的前缀 -->
        <setting name="logPrefix" value="MyBatis_Log" />
      
      	<!-- 指定 MyBatis 所用日志的具体实现，未指定时将自动查找 -->
        <setting name="logImpl" value="LOG4J" />
      
    </settings>

  
  	<!-- 换名，将Dao.Entity.User换名为User，之后MyBatis查找User类时会对应Dao.Entity.User-->
    <typeAliases>
        <typeAlias type="Dao.Entity.User" alias="User"/>
    </typeAliases>

  	<!-- 配置数据库连接 -->
  	<!-- 默认的环境 ID（比如:default=”development”）。
		每个 environment 元素定义的环境 ID（比如:id=”development”）。
		事务管理器的配置（比如:type=”JDBC”）。
		数据源的配置（比如:type=”POOLED”）。
		默认的环境和环境 ID 是一目了然的。随你怎么命名，只要保证默认环境要匹配其中一个环境ID。-->
  	<!-- dataSource type 见1.2配置额外说明--> 
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}" />
                <property name="url" value="${jdbc.url}" />
                <property name="username" value="${jdbc.username}" />
                <property name="password" value="${jdbc.password}" />
            </dataSource>
        </environment>
    </environments>
  
	<!-- 注册一个Mapper，对应在Dao.Mapper.UserMapper --> 
    <mappers>
        <mapper class="Dao.Mapper.UserMapper" />
    </mappers>
  
</configuration>
```



#### 1.2 配置文件额外说明

##### 1.2.0 DataSource Type

- UNPOOLED

  > UNPOOLED– 这个数据源的实现只是每次被请求时打开和关闭连接。虽然一点慢，它对在及时可用连接方面有性能要求的简单应用程序是一个很好的选择。 不同的数据库在这方面表现也是不一样的，所以对某些数据库来说使用连接池并不重要，这个配置也是理想的。UNPOOLED 类型的数据源仅仅需要配置以下 5 种属性：

> ###### driver
>
>  这是 JDBC 驱动的 Java 类的完全限定名（并不是JDBC驱动中可能包含的数据源类）。
>
> ###### url
>
>  这是数据库的 JDBC URL 地址。
>
> ###### username
>
>  登录数据库的用户名。
>
> ###### password
>
>  登录数据库的密码。
>
> ###### defaultTransactionIsolationLevel
>
>  默认的连接事务隔离级别。

- POOLED

> POOLED– 这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来，避免了创建新的连接实例时所必需的初始化和认证时间。 这是一种使得并发 Web 应用快速响应请求的流行处理方式。 
>
> 除了上述提到 UNPOOLED 下的属性外，会有更多属性用来配置 POOLED 的数据源：
>
> **poolMaximumActiveConnections** - 在任意时间可以存在的活动（也就是正在使用）连接数量，默认值：10
>
> **poolMaximumIdleConnections** - 任意时间可能存在的空闲连接数。
>
> **poolMaximumCheckoutTime** - 在被强制返回之前，池中连接被检出（checked out）时间，默认值：20000 毫	 秒（即 20 秒）
>
> **poolTimeToWait** - 这是一个底层设置，如果获取连接花费的相当长的时间，它会给连接池打印状态日志并重新	 试获取一个连接（避免在误配置的情况下一直安静的失败），默认值：20000 毫秒（即 20 秒）。
>
> **poolPingQuery** - 发送到数据库的侦测查询，用来检验连接是否处在正常工作秩序中并准备接受请求。默认是“NO PING QUERY SET”，这会导致多数数据库驱动失败时带有一个恰当的错误消息。
>
> **poolPingEnabled** - 是否启用侦测查询。若开启，也必须使用一个可执行的 SQL 语句设置 poolPingQuery 属	   性（最好是一个非常快的 SQL），默认值：false。
>
> **poolPingConnectionsNotUsedFor** - 配置 poolPingQuery 的使用频度。这可以被设置成匹配具体的数据库	  连接超时时间，来避免不必要的侦测，默认值：0（即所有连接每一时刻都被侦测 — 当然仅当 poolPingEnabled 	  为 true 时适用。

- JNDI

> JNDI -  这个数据源的实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的引用。这种数据源配置只需要两个属性：
>
> **initial_context** - 这个属性用来在 InitialContext 中寻找上下文（即，initialContext.lookup(initial_context)）。这是个可选属性，如果忽略，那么 data_source 属性将会直接从 InitialContext 中寻找。`
>
> **data_source** - 这是引用数据源实例位置的上下文的路径。提供了 initial_context 配置时会在其返回的上下文中进行查找，没有提供时则直接在 InitialContext 中查找。



##### 1.2.1 Mapper

需要告诉 MyBatis 到哪里去找到这些Mapper。 Java 在自动查找这方面没有提供一个很好的方法，所以最佳的方式是告诉 MyBatis 到哪里去找映射文件。你可以使用相对于类路径的资源引用， 或完全限定资源定位符（包括 *file:///*的 URL），或类名和包名等。例如：

```xml
<!-- Using classpath relative resources -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

```xml
<!-- Using url fully qualified paths -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```

```xml
<!-- Using mapper interface classes -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
<!-- Register all interfaces in a package as mappers -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

这些配置会告诉了 MyBatis 去哪里找映射文件，剩下的细节就应该是每个 SQL 映射文件了，后面会提到。



### 2.Usage

#### 2.0 Enity

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



#### 2.1 Mapper

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



#### 2.2 Usage

编写一个单元测试进行测试

```java
/**
 * Created by Jaho on 2017/5/8.
 */

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;
import Dao.Entity.User;
import Dao.Mapper.UserMapper;
import java.io.Reader;


/**
 * 配置spring和junit整合，junit启动时加载springIOC容器 spring-test,junit
 */

public class UserTest {

    static SqlSessionFactory sqlSessionFactory=null;
    //Session
    static SqlSession session=null;
    //字符流
    static Reader reader=null;

    @Test
    public void testGetUserById() throws Exception {
        long user_id = 6;
        try {
            reader = Resources.getResourceAsReader("Configuration/mybatis-config.xml");
            //建立SqlSessionFactory
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
            //打开Session
            session=sqlSessionFactory.openSession();
            //获取用户接口对象
            UserMapper userMapper=session.getMapper(UserMapper.class);
            //调用查询方法
            User user = userMapper.getUserById(user_id);
            System.out.println(user.getName()+"..."+user.getPhone()+"..."+user.getExpired_date());
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
```

User表中对于数据：

| id   | phone       | name | isMale | token            | expired_date |
| ---- | ----------- | ---- | ------ | ---------------- | ------------ |
| 6    | 01234567890 | Jaho | 1      | u50tlxb0036to26u | 2017-06-10   |

测试输出

```
Jaho...01234567890...2017-06-10
```



**至此，MyBatis框架已经配置完成并且可以使用**