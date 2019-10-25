---
layout: django
title: Django跨域处理
date: 2017-09-28 23:37:47
tags: [Django,Python]
categories: Server
---

## Django跨域处理

使用Django开发时，经常遇到跨域和需要对views.py每个函数进行配置的情况。

**在此使用插件：django-cors-headers** 进行统一配置



### 0. Install

```python
pip install django-cors-headers
```



### 1. Configure

**settings.py**

引入：

```python
INSTALLED_APPS = [
    ...
    'corsheaders'，
    ...
 ] 

MIDDLEWARE_CLASSES = (
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware', # 注意顺序
    ...
)
```

配置：

```python
#跨域增加忽略
CORS_ALLOW_CREDENTIALS = True
CORS_ORIGIN_ALLOW_ALL = True
CORS_ORIGIN_WHITELIST = (
    '*'
)

CORS_ALLOW_METHODS = (
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
    'VIEW',
)

CORS_ALLOW_HEADERS = (
    'XMLHttpRequest',
    'X_FILENAME',
    'accept-encoding',
    'authorization',
    'content-type',
    'dnt',
    'origin',
    'user-agent',
    'x-csrftoken',
    'x-requested-with',
    'Pragma',
)
```



