---
title: Django + Nginx + Uwsgi 部署
date: 2017-09-28 23:36:07
tags: [Django,Nginx,Uwsgi,Python]
categories: Server
---

## Django + Nginx + Uwsgi 部署

开发完 Django 应用，我们需要对 Django 进行部署。本文的解决方案为 Django + Nginx + Uwsgi

### 0. Nginx

[中文官网](http://www.nginx.cn)

[英文官网]()

#### 0.0 Install

```
sudo apt-get install nginx
```

#### 0.1 Command

```
nginx -s reload|reopen|stop|quit  #重新加载配置|重启|停止|退出 nginx
nginx -t   #测试配置是否有语法错误
-c filename  # 通过配置文件启动（默认是：/usr/local/etc/nginx/nginx.conf）
```

#### 0.2 Configure

Nginx 的配置很简洁。

编辑 nginx.conf 文件，后续结合 Uwsgi 进行说明。



### Uwsgi

#### 1.0 Install

通过pip安装uwsgi。

```
python3 -m pip install uwsgi
```

在Django 项目中创建一个配置文件，可自行命名，后缀为.ini

示例：

```python
# myweb_uwsgi.ini file
[uwsgi]

# Django-related settings
# uwsgi 监听的端口
socket = :8099 

# 项目目录路径 (若是服务器环境，需要是服务器上的路径)
# the base directory (full path)
chdir           = /Users/Jaho/Desktop/CodeSpace/PythonSpace/cms

# Django s wsgi file
# 项目的wsgi文件指向
module          = cms.wsgi

# process-related settings
# master
master          = true

# maximum number of worker processes
processes       = 4

# ... with appropriate permissions - may be needed
# chmod-socket    = 664
# clear environment on exit
vacuum          = true
```

如何运行？

进入项目目录，执行命令：

```python
uwsgi --ini filename.ini
```

就可看到 uwsgi 开始运行了 Django 项目。 



### Nginx + Uwsgi

编辑 nginx.conf 文件，Ubuntu 环境下，默认为 /etc/nginx/nginx.conf 文件

在文件中 http 块中添加配置如下：

```python
# 新建一个配置
server {
  # 监听 8000 端口
  listen         8000; 
  # 域名为 localhost (也可以是你自己的域名)
  server_name    localhost; 
  charset UTF-8;

  client_max_body_size 75M;

  # 很重要！！！Django Admin 的样式是静态添加进去的，需要自行添加路径，否则出现没有样式的情况
  location /static/ {
      alias /usr/local/lib/python3.5/dist-packages/django/contrib/admin/static/;
  }	

  # 将对后端的请求转发至uwsgi
  location / { 
      include uwsgi_params;
      # uwsgi 监听端口
      uwsgi_pass 127.0.0.1:8099;
      # timeout 配置
      uwsgi_read_timeout 30;
  } 
}
```


最后，通过配置文件启动或者reload Nginx 就可以了啦！