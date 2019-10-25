---
title: Tomcat域名绑定
date: 2017-05-11 13:17:32
tags: [Java,Web,Tomcat]
categories: Server
---

## Tomcat域名绑定

将Java Web App部署到远程服务器Tomcat时，有时候我们有这样的需求：

通过不同的域名直接访问的我们的Web App

例如：

最近和毛兄写MaomaoChat，我们买了域名www.maomaochat.tech，绑定我们的服务器IP后，使用Tomcat的默认配置，我们的应用API前缀为：

www.maomaochat.tech:8080/MaomaoChat/api

可以看出，我们必须要加端口号8080，而且输入两次MaomaoChat，这是极其不优雅的。

我们期望的应用API前缀应该为：

maomaochat.tech/api

解决办法：

配置Tomcat目录下的conf文件夹里的server.xml**(后续配置均在此文件中)**

#### 0. 去除端口号

找到*Connector*标签，修改port 8080为80端口(需要root用户权限才能生效)

修改后：

```xml
<Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

使用 netstat命令可查看是否成功监听80端口

终端输入：

```
netstat -anp | grep java
```

如果输出列表有80端口表明修改生效，这时，只通过IP就可以访问到WebApp目录



#### 1. 配置域名绑定

##### 1.0查看hosts文件，一般在/etc路径下

一般有如下配置:

```properties
127.0.0.1 localhost
```

这段配置即是增添了一个域名名为 localhost，对应的IP为127.0.0.1

这就是为什么我们能够通过localhost访问本地资源的原因，这有助于我们理解后续配置



##### 1.1找到*Host*标签

默认配置

* name - 虚拟主机名localhost

* appbase - WebApp的放置目录，Tomcat默认为webapps目录下，根据官方文档:

  > You may specify an absolute pathname for this directory, or a pathname that is relative to the *$CATALINA_BASE*directory.

  说明我们可以使用$CATALINA_BASE对于目录的相对路径

  **补充说明:**

  Tomcat允许我们只安装一个Tomcat但是有多个Tomcat实例，这样就牵扯到Tomcat的数据共享。

  还记得一般我们要给Tomcat配环境变量吗，其中两个环境变量：

  $CATALINA_HOME 

  $CATALINA_BASE

  * $CATALINA_HOME  - **指向Tomcat实例之间公用信息的位置，就是bin和lib的父目录。**
  * $CATALINA_BASE - - **每个Tomcat实例私有信息的位置，就是conf、logs、temp、webapps和work的父目录。**

  因此，按照默认Tomcat配置，webapps与conf文件夹相较于$CATALINA_BASE来说，处于同一级，默认配置只需要目录名webapps

```xml
<Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Valve 	className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
```

*Host*下还有一个*Context*标签：

官方文档：

>Any XML file in the *$CATALINA_HOME/conf/[engine_name]/[host_name]* directory is assumed to contain a [Context](http://tomcat.apache.org/tomcat-5.5-doc/config/context.html) element (and its associated subelements) for a single web application. The *docBase* attribute of this *<Context>*element will typically be the absolute pathname to a web application directory, or the absolute pathname of a web application archive (WAR) file (which will not be expanded). The path attribute will be automatically set as defined in the [Context](http://tomcat.apache.org/tomcat-5.5-doc/config/context.html) documentation.

若添加Context标签

* path是虚拟路径，也就是我们在Host name后需要添加的路径名


* docBase目录是指向我们的WebApp的绝对路径，是 path对应的实际路径名

默认localhost没有配置Context，它的默认配置相当于：

```xml
<Context 
path="/" 
docBase="usr/local/tomcat/webapps/ROOT" 
reloadable="true"/> 
```

即，在localhost后不需要添加路径名，指向ROOT目录，这也就是我们只输入localhost却可以跳转至ROOT目录下的index.jsp默认文件的原因。

**注意：一个Host只能有一个path为空的对于目录**

**搞清楚原理，来添加自己的域名绑定。**

为了方便，我们添加一个WebApp目录，和Tomcat的默认 webapps目录同级，命名为maomaochat

添加*Host*标签：

```xml
<Host name="www.maomaochat.tech"  appBase="maomaochat"
            unpackWARs="true" autoDeploy="true">

            <Alias>maomaochat.tech</Alias>

        <!-- <Context path="" docBase="MaomaoChat" reloadable="true"/> -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="MaomaoChat_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
```

看到：

* 我们虚拟主机名为我们的域名，"www.maomaochat.tech"，对应的WebApp目录maomaochat
* 我们使用换名，将 maomaochat.tech同样映射到我们的这个Host下，你也可以添加多个配置。
* 修改了*Valve*标签，将Log文件前缀改为MaomaoChat_access_log
* 不添加*Context*标签，而是在maomaochat目录下新建了ROOT目录，将我们的项目文件直接传到ROOT目录下，这样即是使用默认配置。
* 你也可以通过添加*Context*标签，在daoBase目录下制定项目的目录名来进行配置



**至此，完成Tomcat的域名绑定，我们可以只通过域名访问我们的Web App**

