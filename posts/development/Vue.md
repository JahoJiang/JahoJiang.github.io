---
title: Vue.js + Axios
date: 2017-09-29 00:11:55
tags: [Vue, Axios]
categories: Web
---

## Vue.js + Axios

### Vue.js

#### 0. Intro

>Vue.js (读音 /vjuː/，类似于 **view**) 是一套构建用户界面的**渐进式框架**。与其他重量级框架不同的是，Vue 采用自底向上增量开发的设计。Vue 的核心库只关注视图层，它不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)和 [Vue 生态系统支持的库](https://github.com/vuejs/awesome-vue#libraries--plugins)结合使用时，Vue 也完全能够为复杂的单页应用程序提供驱动。

对我来说， Vue 是一个让我组件化开发前端单页面应用的框架，避免了编写大量重复的代码。特点是轻量化，强大。是国人尤雨溪开发的！！！所以官方文档有中文版，[戳这里](https://cn.vuejs.org/v2/guide/)，而且比较详尽。

#### 1. Usage

只介绍如何使用单文件组件形式进行开发。

思想：通过单文件编写组件，利用 vue-router 进行组件的组合：通过配置，将 URL 与组件进行映射关系的配置，从而选择需要进行渲染的组件。

##### 1.0 Vue-Cli

  Vue-cli是快速构建这个单页应用的脚手架，帮助我们快速构架 Vue 项目的模板。

###### 1.0.0 Intall

```
npm install --global vue-cli
```

###### 1.0.1 Start

```
vue init webpack projectname
```

webpack 是项目模板名称

projectname 是项目名称

按照指引进行配置，之后 cd 到项目目录，执行

```
npm install # 按照依赖库
npm run dev # 启动开发用服务器
npm run build # 在 dist 目录下生成部署用的文件，将其中所有文件放在服务器环境就完成了部署，当然自己得配置服务器
```

###### 1.0.2 Files

项目结构如下：

```
├── README.md
├── build
│   ├── build.js
│   ├── check-versions.js
│   ├── dev-client.js
│   ├── dev-server.js
│   ├── utils.js
│   ├── vue-loader.conf.js
│   ├── webpack.base.conf.js
│   ├── webpack.dev.conf.js
│   ├── webpack.prod.conf.js
│   └── webpack.test.conf.js
├── config
│   ├── dev.env.js
│   ├── index.js
│   ├── prod.env.js
│   └── test.env.js
├── index.html
├── package.json
├── src
│   ├── App.vue
│   ├── assets
│   ├── components
│   ├── main.js
│   └── router
├── static
└── test
    ├── e2e
    └── unit
```

主要关注：

* index.html，应用入口，最后 build 只剩下 index.html 和静态文件。

附：配置网页图标：

```html
<head>

    <meta charset="utf-8">

    <title>课程管理系统</title>

    <link rel="icon" href="/static/logo.png" type="image/x-icon"/>

  </head>
```

* src 目录，我们进行代码编写的目录：

  * App.vue —  模板示例文件，一个 .vue 文件即是一个单文件组件，后续会讲到。
  * assets — 放置资源的文件夹。
  * components — 编写组建的文件夹。
  * main.js — 项目入口的配置。
  * router - 配置路由，模板中已有 index.js ，可在此进行配置。

  ​

##### 1.1. Component

创建一个 .vue 文件进行单文件组件的开发：

一个 .vue 文件分为三部分：

1. template: html 编写组件的结构
2. script: js 编写组件的业务逻辑
3. style: css 编写组件样式

示例：

```javascript
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

至于 js 部分业务逻辑的编写，详见官方文档，[戳这里](https://cn.vuejs.org/v2/guide/)



##### 1.2 Configure

index.js 配置路由

```javascript
# 引入组件
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/components/Home'

import Memo from '@/components/record/Memo'
import Record from '@/components/record/Record'
import Modify from '@/components/record/Modify'

Vue.use(Router)

# 路由配置
export default new Router({
  routes: [
    {
      path: '/', # 路径
      name: 'Home', # 名称
      component: Home # 上方对应的引入的组件名
    },

    # 可多对一配置
    {
      path: '/Home',
      name: 'Home',
      component: Home
    },

    # 利用 children 属性实现二级路由，常用与二级局部刷新
    {
      path: '/Record',
      name: 'Record',
      component: Record,
      children: [
        {
          path: '/',
          name: 'Modify',
          component: Modify
        },

        {
          path: '/Modify',
          name: 'Modify',
          component: Modify
        },

        {
          path: '/Memo',
          name: 'Memo',
          component: Memo
        }
      ]
    }
  ]
})

```

main.js 配置

```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.

# 资源的引入
import Vue from 'vue'
import App from './App'
import router from './router'
import iView from 'iview'
import 'iview/dist/styles/iview.css'
import axios from 'axios'

# 全局配置
Vue.use(iView)
# 配置使用 axios
Vue.prototype.axios = axios
# axios 基础 URL
axios.defaults.baseURL = 'http://127.0.0.1:8000/'
Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
})
```



### Axios

#### 0. Intro

>



#### 1. Install

```
npm install axios --save
```



#### 2. Usage

GET:

```javascript
this.axios({
  method: 'post',
  url: 'get/mjr',
  data: this.college
})
  .then(function (response) {
  this.$Notice.success({
    title: '成功',
    desc: '获取专业信息成功'
  })
  this.majors = response.data
}.bind(this))
// bind this 很重要，否则无法读取
  .catch(function (error) {
  console.log(error)
})
```

POST:

```javascript
this.axios({
  method: 'post',
  url: 'record/',
  data: {record: this.changes}
})
  .then(function (response) {
  this.$Notice.success({
    title: '上传成功',
    desc: '课程修改记录已经上传成功'
  })
}.bind(this))
// bind this 很重要，否则无法读取
  .catch(function (error) {
  console.log(error)
})
```

