---
title: vue 项目总结一组件开发的配置和例子
date: 2018-01-30 11:21:21
tags: [vue,项目结构]
---
上一篇文章 [vue 项目总结一文件夹结构配置](https://w-e-i.github.io/2018/01/30/vue-%E9%A1%B9%E7%9B%AE%E6%80%BB%E7%BB%93%E4%B8%80%E6%96%87%E4%BB%B6%E5%A4%B9%E7%BB%93%E6%9E%84%E9%85%8D%E7%BD%AE/) 介绍了项目里文件夹的分类和作用，这次主要说明 src 文件夹里具体的文件分类和内容。


----------
先上 src 文件夹的结构图：

![](src.png)
<!-- more -->
文件及文件夹作用
==
---
App.vue
===
> App.vue: 根组件，pages 里的组件会被插入此组件中，此组件再插入 index.html 文件里，形成单页面应用

根组件里面是这样子的：

```vue
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'app',
};
</script>

<style>
  #app {
    width: 100%;
    height: 100%;
    font-family: "Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    min-width: 1440px;
  }
</style>

```

其中，<router-view> 组件是一个 functional 组件，渲染路径匹配到的视图组件。渲染的组件还可以内嵌自己的，根据嵌套路径，渲染嵌套组件。这样，就实现了单页面下，根据不同路由，渲染不同组件的目的。
基本上根组件没什么交互要做，底部的样式其实也可以放在全局的样式表里。


----------
main.js
===
> main.js: 入口 js 文件，影响全局，作用是引入全局使用的库、公共的样式和方法、设置路由等。

这个是负责配置影响全局的内容的文件，具体会有以下几种作用：

1、引入vue 以及相关的库

```Javascript
    import Vue from 'vue'  //引入 vue

    import store from './store'  //引入 vuex

    import router from './router';  //引入路由配置文件

    import App from './App'  //引入根组件
```
2、 引入需要用到的第三方库（注意注册使用方式的区别）
```Javascript
    // 引入element-ui

    import ElementUI from 'element-ui';

    import 'element-ui/lib/theme-default/index.css';

    Vue.use(ElementUI);

    // 引入字体图标样式，这里使用了阿里妈妈的 iconfont 字体库

    import '@assets/iconfont/iconfont.css';

    import '@assets/iconfont/iconfont.js';

    // 引入copy 信息组件

    import VueClipboards from 'vue-clipboards';

    Vue.use(VueClipboards);

    // 引入 axios 库

    import axios from 'axios'

    // 引入 d3 图形库

    import * as d3 from 'd3'

    // 引入国际化的库

    import VueI18n from 'vue-i18n';

    Vue.use(VueI18n);

    //引入自定义的 json 格式中英文对照文件

    import zh from '@assets/lang/zh-CN'

    import en from '@assets/lang/en-US'

    Vue.config.lang = 'zh-cn'  //设置默认中文

    Vue.locale('zh-cn', zh)

    Vue.locale('en', en)

    // 引入时间转换模块

    import moment from 'moment';

    moment.locale('zh-cn');

    Vue.prototype.$moment = moment;  //将 moment 模块转换成 Vue 的原型方法，在组件里可以直接使用 this.$moment(time)

    // 引入图表

    import ECharts from 'vue-echarts';

    Vue.component('chart', ECharts);  //注册 Echarts 成为全局组件，在组件里可以直接调用 <chart></chart>
```
3、 引入自定义的库
```Javascript
    // 引入银行卡图标样式

    import '@assets/icon-bank/iconfont.js';  // iconfont 上收集的银行卡图标

    // 引入自定义的http模块

    import { AjaxApi } from '@http/AjaxApi.js';  //http 文件夹里自定义的处理 api 接口的文件，导出一个包含所有与后台接口交流的函数的对象

    Vue.prototype.$axios = AjaxApi;  //加入 Vue 原型方法，组件里通过 this.$axios.xxx() 调用

    // 引入公共方法

    import commonMixins from '@mixins/common-mixins.js';  //mixins 文件夹里自定义的通用函数的集合

    Vue.mixin(commonMixins);  //全局注册混合
```
4、 引入自定义的公共样式，使得组件内可以用scoped定义自身的样式

```Javascript
    // 引入公共样式以及修改过的 element 样式

    import '@assets/css/common.less'

    import '@assets/css/theme.less'
```
5、 定义一些简短的不需要单独引入的全局修改

```Javascript
     // 在html5 history 模式下，在form表单的组件(input输入框等)里点击enter，会自动将表单数据以get的方式发送到后台，需要阻止默认事件

    document.onkeydown = function(e) {

        var e = e || event;

        if(e.keyCode == 13) {

            e.preventDefault ? e.preventDefault() : (e.returnValue = false);

        }

    }；

    // 格式化金额，每三位加逗号，可选保留几位小数

    Number.prototype.format = function(n, x) {

        var re = '\\d(?=(\\d{' + (x || 3) + '})+' + (n > 0 ? '\\.' : '$') + ')';

        return this.toFixed(Math.max(0, ~~n)).replace(new RegExp(re, 'g'), '$&,');

    };

```
6、 设置vue的全局配置，在启动应用前应用

```Javascript
    Vue.config.productionTip = false;  // 阻止 vue 在启动时生成生产提示
```
7、 指定渲染的文件

```Javascript
     new Vue({

        el: '#app',

        template: '<App/>' ,

        router,

        store,

        components: { App }

    })
```
----------
assets 文件夹
===
> assets: 放置静态资源，包括公共的 css 文件、 js 文件、iconfont 字体文件、img 图片文件以及其他资源类文件。

结构如下：

![](assets.png)

css 文件夹里会有[重置 css 样式的文件][1]以及其他全局样式文件。

js 文件夹里放置了包含银行字典和全国省市的字典文件，在组件里引用之后遍历获取数据。


  [1]: https://www.cnblogs.com/HtmlCss3/p/6061623.html


----------
components 文件夹
===
> components: 放置通用模块组件。项目里总会有一些复用的组件，例如弹出框、发送手机验证码、图片上传等，将它们作为通用组件，避免重复工作；

结构如下：

![](components.png)

可以根据功能模块建立文件夹，放置本功能会用到的通用组件。例如 login 文件夹里可以放置注册、登录、重置密码这几个功能会用的共同模块文件（账号、密码、图形验证码、短信验证码）； account-center 文件夹放置修改账号相关的模块。

全局通用的公共模块可以不需要建立文件夹。


----------

http 文件夹
===
> http: 放置与后台 api 相关的文件。这里面有 axios 库的实例配置文件、使用配置的 axios 实例接入 api 获取数据的函数的集合的文件；

结构如下：

![](http.png)

config.js 是根据项目需求配置的 axios 实例文件，通过 axios.create([config]) 创建，可以配置诸如指定成功的状态码、序列化 params、设置 headers 、修改 token 、设置全局请求/响应拦截器、设置 baseURL 等。

AjaxApi.js 是通过导入 config.js 实例，传入 API 和其他参数，给每个 API 配置一个专属函数，再集合导出成对象的文件。例子如下：

![](http-instance.png)


----------
mixins 文件夹
===
> mixins: 放置混合选项的文件。具体来说，相当于是公用函数的集合，在组件中引用时，可以作用于组件而不必书写重复的方法

个人认为，相当于是没有&lt;template/> 和 &lt;style/> 的组件，例子如下：

![](mixin.png)


----------
pages 文件夹
===
> pages: 放置主要页面的组件。例如登录页、用户信息页等。通常是这里的组件本身写入一些结构，再引入通用模块组件，形成完整的页面

这里面就是会被插入根组件的 '<router-view/>' 节点里的文件，根据路由变化，根组件渲染不同的文件。

都是单文件组件，没有特殊的结构，就不放图了。


----------
router 文件夹
===
> router: 放置路由设置文件，指定路由对应的组件，设置路由钩子

例子如下：
```Javascript
    import Vue from 'vue';

    import Router from 'vue-router';

    import Tab from '@pages/tab';

    import { MessageBox } from 'element-ui';

    Vue.use(Router);

    const router = new Router({  //新建路由

    routes: [

        {

            path: '/',

            redirect: '/signin'  //重定向路由

        },

        {

            path: '/signin',

            name: 'signIn',  //命名路由

            component: (resolve) => {  //按需加载

                require(['@pages/sign-in'], resolve);

              }

          },

        {

            path: '/signup',

            name: 'signUp',

            component: (resolve) => {

                require(['@pages/sign-up'], resolve);

                }

            },

            {

                path: '/tab',

                component: Tab,

                children: [  //嵌套路由

                    {

                        path: 'index',

                        name: 'index',

                        component: (resolve) => {

                        require(['@pages/index'], resolve);

                        }

                    }

                ]

            }

        ]

    });

    router.beforeEach((to, from, next) => {  //检测 token ，跳转登录页

    if (checkPathRequiredAuth(to.path) && !sessionStorage.token) {

        sessionStorage.clear();

        MessageBox({

            title: '跳转提示',

            message: '用户认证已过期或不存在，确认后跳转到登录页',

            confirmButtonText: '确定',

            type: 'warning',

            callback: action => {

                next({

                    path: '/signin'

                });

            }

        });

    } else {

        next();

    }

    });

    export default router;
```
----------
store 文件夹
===
> store: 放置 vuex 需要的状态关联文件，设置公共的 state、mutations 等

1、如果项目结构不是很复杂，多是父子组件的通信，可以使用 props 传递数据，$emit 和 on 事件通信，store 文件夹里就只需创建 js 文件。

结构如下：

![](store.png)

2、反之，主要使用 vuex 来传递数据和通信的话，需要按照模块来划分 modules 。store 文件夹里除了有index.js 和全局相关的 js 文件外，还有 modules 文件夹， 在 modules 文件夹里根据模块创建对应的 js 文件，导出，最后在 store 文件夹下一级的 index.js 里导入。

store 结构如下：


![](store-vuex.png)



modules 结构如下：

![](store-modules.png)

sign-in.js:

![](sign-in.png)

index.js:


![](index.png)

> 要注意的是，使用 modules 分割模块后，组件里获取 state 时要指明对应的 modules。
```Vue
    computed: {
      ...mapState({
        data: state => state.signIn.data,  //sign-in.js 里的 state
        user: state => state.user    //index.js 里的 state
      })
    }
```