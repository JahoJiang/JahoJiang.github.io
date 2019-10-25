---
title: Django
date: 2017-09-28 21:32:13
tags: [Django,Server,Python]
categories: Server
---

## Django 总结

​	一直以来想学习Python，索性趁着写外包，现学现卖，从Django入手学习了一下。

​	首先，Django的文档极其全面和清晰，这也是Django大为流行的原因吧。因此文中多处只给出文档链接，这些细节的说明，看文档是更高的选择。

​	以下全文基于个人理解，主要作为一个踩坑梳理，方便自己后续进行查看。如有错误，劳请斧正。

#### 0. Meet Django

> Django is a high-level Python Web framework that encourages rapid development and clean, pragmatic design. Built by experienced developers, it takes care of much of the hassle of Web development, so you can focus on writing your app without needing to reinvent the wheel. It’s free and open source.

#### 1. Personal Opinion

​	Django 是一个非常灵活且强大的框架，它提供了进行后端开发的一站式解决方案。利用它可以快速的进行建站开发。

​	但是由于Python动态数据类型的特性，也使得有很多错误在编译期不能被检查出来。对于开发大型的后端应用来说，个人认为是很不方便的。

​	而开发小型应用，个人认为，使用 Java Web 技术是很不经济的：对于小型应用来说，Java 所有类的Getter, Setter 代码量加起来，可能用Django 都快写完这个项目了。

因此：

**开发小型应用，推荐用 Django。**（当然，Flask 更简单轻便，但是大同小异。）

**开发大型应用，推荐用Java - SSM**

### 2. Usage

#### 2.0 Install

​	详见[这里](http://python.usyiyi.cn/documents/django_182/intro/tutorial01.html)

额外说明：

* wsgi.py — 简单来说，我们需要它进行服务器环境部署。更多细节参见文档[如何利用WSGI进行部署](http://python.usyiyi.cn/documents/django_182/howto/deployment/wsgi/index.html)或者我的另一篇博客**《Django + Nginx + Uwsgi 部署》**。
* settings.py — 项目配置文件。此处推荐插件 **django-cors-headers**，详见我的另一篇博客**《Django 跨域处理》**，利用它可非常方便的进行Django的跨域请求等配置。

#### 2.1 Feature

* Models —  数据库中每一个表结构对应的Class。Django通过抽象化的模型层(models)为你的网络应用提供对于数据的结构化处理和操作处理。Models 是你的数据的唯一的、权威的信息源。它包含你所储存数据的必要字段和行为。通常，每个模型对应数据库中唯一的一张表。

* View — 用于封装负责处理用户请求及返回响应的逻辑。

* Templates — 作为Web 框架，Django 需要一种很便利的方法以动态地生成HTML。最常见的做法是使用模板。模板包含所需HTML 输出的静态部分，以及一些特殊的语法，描述如何将动态内容插入。

  由于我是使用Vue.js 进行前端开发，因此后文对 Templates 不做赘述

* Admin — 查找所有需要了解的自动化管理界面，Django最受欢迎的功能之一。利用它，我们可以极其简单的为后端创建后台管理系统。

#### 2.2 Models

##### 2.2.0 Model 设计

Django为我们设计数据库表结构提供了新思路：直接设计表结构对应的 Model，通过命令，将 Model 映射成为数据库中的一张张表。通过 Model 可以非常简单的管理数据库和进行数据库操作。

直接看一个示例代码：

```python
class Memo(models.Model):
    id = models.IntegerField(primary_key=True)
    m_time = models.DateTimeField(verbose_name='时间')
    memo = models.TextField(blank=True, null=True, verbose_name='备忘内容')

    class Meta:
        verbose_name_plural = "备忘记录"
        verbose_name = "备忘记录"
        db_table = 'memo'
```

1. 这里，创建了一个Memo类，继承自 Django.db.mdels 的Model类，代表它是一个 Django Model

2. id，使用`models.IntegerField(primary_key=True)`，使用`IntegerField`声明类型，参数`primary_key=True`，代表着我们将它设置为主键。

3. `memo = models.TextField(blank=True, null=True, verbose_name='备忘内容')`

   使用models.TextField 声明为TEXT 类型.

   * `blank = True`。如果为`True`，则该字段允许为空白。 默认值是 `False`。
   * `null=True`。如果为`True`，Django将在数据库中将空值存储为`NULL`。默认值是 `False`。
   * `verbose_name='备忘内容'`。一个字段的可读性更高的名称。Admin也会使用这个名称作为这个字段的别名。
   * Class Meta: 作为内部类，它对这个Model进行更多的设置：
     * `verbose_name = "备忘记录"` 表的别名。
     * `verbose_name_plural = "备忘记录"` 对于表中单条记录的别名。
     * `db_table = 'memo' `  Model 对于表结构的名称

##### 2.2.1 Model 创建或修改后的操作

- 运行`python manage.py makemigrations` ，为这些修改创建迁移文件
- 运行`python manage.py migrate`，将这些改变更新到数据库中。

**特别注意**

Model 的 "managed" 属性，Django 会将 Model 的修改映射到数据库对应的表结构中，如果你不希望，则可设置为Fasle，这样你的修改就仅仅是 Model层面的，执行上面的命令不会修改数据库。（我一开始没注意这个属性，结果运行`python manage.py migrate`死活不见数据库改动，我在这查了半天错，最后才发现这居然把一个属性设置为False了。

##### 2.2.2 Model的基本操作：

详见中文文档[这里](http://python.usyiyi.cn/documents/django_182/topics/db/queries.html)，非常详细和易懂。

##### 2.2.3 我们还可以对Model进行更多的设置和封装。不做赘述。

**Django提供的功能非常充足，可以在[这里](https://docs.djangoproject.com/en/1.11/topics/db/models/)了解更多。**



#### 2.3 Views

详见中文文档[这里](http://python.usyiyi.cn/documents/django_182/topics/http/urls.html)

views.py, 这里可以看做是对每一个 HTTP Request 的接收和处理的地方。在这里可定义一个函数，通过URL配置映射到一个特定的请求中，并进行处理。

#### 2.4 URLS

urls.py, 进行 URL 和 view.py 中函数的映射关系配置。

示例：

views.py

```python
def index(request):
    return HttpResponse('Hello World')
```

urls.py

```python
urlpatterns = [
    url(r'^index', index),
]
```

这样，URL 为index的请求就会被 Django 转发给 views.py 的 index 函数，由 index 函数进行处理。

详见文档[这里](http://python.usyiyi.cn/documents/django_182/topics/http/urls.html) 

#### 2.5 Admin

##### 2.5.0 时区。

打开settings文件，找到里面的：

**TIME_ZONE = 'UTC'**

改为：

**TIME_ZONE = 'Asia/Shanghai' **

之后，输出的时间正常了。 

##### 2.5.1 配置

通过为每一个 Model 设定一个继承自admin.ModelAdmind的类，对 Model 进行配置。

示例：

```python
class MjrBscAdmin(admin.ModelAdmin):
    exclude = ['id']
    list_display = ('college', 'name', 'level')
    list_filter = ('college',)
```

* exclude : 隐藏的属性。例如我们不希望主键被随意的修改，则可以将主键属性进行隐藏。
* list_displkay : 在表中，每一单项记录，显示的属性集合。例如，MjrBsc 有十几个属性，但是 'college', 'name', 'level' 这三个属性可以唯一的标识一行记录，我们就可以只显示这三个属性。用户点击某一条记录进行编辑时，才显示所有属性，并进行操作。
* list_filter：在表界面，可以对记录进行筛选的属性。

##### 2.5.2 注册

只有在 admin.py 中进行了注册的 Model 才会在 Admin 后台中进行展示。

```python
admin.site.register(MjrBsc, MjrBscAdmin)
```

第一个参数是需要进行注册的 Model, 第二个参数是对应的配置类，可以为空。



### 3. Think in Django

Django 的开发思想：

1. 将应用的不同功能拆分为App, 进行模块化
2. 对于每一个 App :
   * models.py 创建数据模型，即 Model
   * 利用 Model 进行业务逻辑开发
   * views.py 对业务逻辑进行处理
   * urls.py 配置 URL 和 views.py 的路径 - 函数 映射关系。
   * admin.py 配置后台
   * 测试