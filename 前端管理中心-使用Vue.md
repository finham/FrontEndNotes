# 前端管理中心-使用Vue

[TOC]

## 课程内容

准备工作

* 搭建开发环境
* 项目初始化

登录模块

仪表盘（index）（叫index或者空都行）

* 概况
  * 浏览量
  * 文章数量
  * 评论数量
* 快捷操作
  * 写文章
  * 管理文章
  * 管理评论

内容管理（content）

* 发表文章（post-article）
* 管理文章（manage-article）
* 管理评论（manage-comment）
* 管理图片（manage-image）

用户管理（user）

* 用户列表（list）
* 重置密码（reset-password）
* 管理员邮箱地址（email）
* 更新管理员信息（info）

运营（operation）

* 分类管理（category）
* 轮播图管理（loop）

设置（settings）

* 网站信息（website-info）
* 友情链接（friend-link）

## 一、Vue准备工作

1、安装好node.js和npm

2、安装脚手架Vue CLI：https://cn.vuejs.org/v2/guide/installation.html#%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7-CLI

```shell
cnpm install -g @vue/cli
```

-g什么意思：global全局的意思

3、然后输入vue，如果有输出，那么证明安装成功了

4、使用图形化界面：https://cli.vuejs.org/zh/guide/

```shell
vue ui
```

就会在浏览器中打开Vue 项目管理器。

所以我们后续就不通过如下命令来创建项目

```shell
vue create hello-world
```

而是通过浏览器上的项目管理器的UI界面来进行项目的创建和管理！这样更容易理解哈。

我要向专业Android + 前端迈进！思想都是相通的，凭借着原有的一些知识，上手前端能很快的。

## 二、项目创建

在cmd / PowerShell 命令行中输入 vue ui 然后创建新项目

包管理器选择 npm。

git填上 init project

预设选择默认

使用WebStorm打开项目

左边的npm有serve（编译和热更新），build，lint等等。

add configuration一下，让server显示。

## 三、Vue文件的基本结构

1、现在的前端开发是很幸福的，跟Android开发一样当成一个App来开发就好了。

2、入口为main.js，通过new Vue载入App.vue

然后打开main.js发现有错误，解决方案如下：https://www.sunofbeach.net/a/1279987679290761216

3、vue server是支持热更新的，也就是改完代码直接刷新一下浏览器即可，不用重新run。

4、.vue 文件里面从上到下是template、script、style。（默认生成的是这样）

* template：显示UI。在这里面只能有一个div容器标签，其他的都不行。嵌套div是允许的，同层级的div只能有一个。
* script：存放脚本
* style：文本样式等

5、主要看项目的代码注释，写得挺清晰的了。

```javascript
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false;

new Vue({
  render: h => h(App),
}).$mount('#app') //mount挂载，#是选择器的意思，app是App.vue里id为app
```

```vue
<template>
  <div id="app">
    <!--main.js里挂载app跳到这里后，一步步往下走，你看显示img，然后HelloWorld，继续跳转
        但是只能有一个div-->
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

//以下的HelloWorld定义在上面了，是vue的语法内容
export default {
  //el:'#app',  这句其实是作用域挂载，不过已经写在了main.js里，用mount挂载了
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

```vue
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank" rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank" rel="noopener">eslint</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a></li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
```

## 四、路由

1、为什么要使用路由呢？因为要完成的界面是这样的：点击导航可以切换不同的页面。

2、vue中的路由：https://cn.vuejs.org/v2/guide/routing.html

专门的router文档：https://router.vuejs.org/zh/installation.html

3、安装路由：

```shell
cnpm install vue-router --save
```

添加完这句会自动在package.json这个文件里添加上vue-router这个开源库，很明显类似于Android的build.gradle的意思哈。

然后在main.js里添加如下代码：

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

添加后的完整main.js如下：

```javascript
import Vue from 'vue'
import App from './App.vue'
import VueRouter from 'vue-router'

Vue.config.productionTip = false;
//不使用的话import语句会报错，这是eslint的检查机制
Vue.use(VueRouter);

new Vue({
  render: h => h(App),
}).$mount('#app') //mount挂载，#是选择器的意思，app是App.vue里id为app
```

4、实现路由。在component文件夹下添加两个文件：home.vue和article.vue。

## 五、抽取路由

把路由这些全部写在main.js里那显得很不合理，整个js文件会非常臃肿。

所以我们要把路由部分抽取出来。

新建index.js文件

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'

//不使用的话import语句会报错，这是eslint的检查机制
Vue.use(VueRouter);

//这个语法是懒加载
const home = () => import("@/components/home");
const article = () => import("@/components/article");

const routes = [
    {path: '/home', component: home},
    {path: '/article', component: article}
]

const router = new VueRouter({
    routes // (缩写) 相当于 routes: routes
});

export default router;
```

main.js改一下引用方式：

```javascript
import router from './router'
```

## 六、划分路由界面

WordPress。

就是给各个页面取名字，设置命名。

## 七、创建各页面

路由分好类后，创建对应的文件。

在page文件夹下创建对应的文件夹，再在每个文件夹下创建对应的vue文件。

文件夹为content、user、login、operation、settings等。

## 八、把页面导入到路由里

把各个页面都在 router 文件夹下的 index.js 里定义一番。

```javascript
const login = () => import("@/page/login/login");
```

完整的 index.js 如下：

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'

//不使用的话import语句会报错，这是eslint的检查机制
Vue.use(VueRouter);

//这个语法是懒加载
const home = () => import("@/components/home");
const article = () => import("@/components/article");
const login = () => import("@/page/login/login");
const manageArticle = () => import("@/page/content/manage-article");
const manageComment = () => import("@/page/content/manage-comment");
const manageImage = () => import("@/page/content/manage-image");
const postArticle = () => import("@/page/content/post-article");
const manageCategory = () => import("@/page/operation/manage-category");
const manageLoop = () => import("@/page/operation/manage-loop");
const friendLink = () => import("@/page/settings/friend-link");
const websiteInfo = () => import("@/page/settings/website-info");
const email = () => import("@/page/user/email");
const info = () => import("@/page/user/info");
const list = () => import("@/page/user/list");
const resetPassword = () => import("@/page/user/reset-password");


const routes = [
    {path: '/home', component: home},
    {path: '/article', component: article}
]

const router = new VueRouter({
    routes // (缩写) 相当于 routes: routes
});

export default router;
```

## 九、配置路由表

这一节比较重要。

首先要明确，我们要做成的布局是由侧栏、标题、内容区域这三块组成的。

点击侧栏要显示不同的标题&内容区域。

将 App.vue里原本的内容抽取成base-view.vue。

> 这边再新建一个layout文件夹，然后底下新建base-view.vue文件，然后这里存放基础的view布局。

然后在index.js里去设置routes的各个子控件。

http://localhost:8080/#/ 这个是默认的地址

http://localhost:8080/#/index 这个就是仪表盘的界面

然后我们希望输入 http://localhost:8080 的时候就自动跳到index仪表盘界面。所以此处使用一个 redirect: 来进行配置。

## 十、引入elementUI

这是一个UI框架

npm导入：

```shell
cnpm i element-ui -S 
```

-S其实就是--save的意思。

下载完成可以去package.json查看一下，你就发现在dependencies里导入进来了！

## 十一、后台管理中心划分区域

直接使用ElementUI的 Container 布局容器。

划分为header、main、aside、footer等区域（根据见过的大多数网页来看，也确实大致就是这样的划分）

## 十二、引入侧栏菜单模块

使用的还是饿了么组件中【NavMenu】的代码：

```html
<el-row class="tac">
  <el-col :span="12">  //这里需要把 :span="12"给去掉，在本项目中
    <h5>默认颜色</h5>
    <el-menu
      default-active="2"
      class="el-menu-vertical-demo"
      @open="handleOpen"
      @close="handleClose">
      <el-submenu index="1">
        <template slot="title">
          <i class="el-icon-location"></i>
          <span>导航一</span>
        </template>
        <el-menu-item-group>
          <template slot="title">分组一</template>
          <el-menu-item index="1-1">选项1</el-menu-item>
          <el-menu-item index="1-2">选项2</el-menu-item>
        </el-menu-item-group>
        <el-menu-item-group title="分组2">
          <el-menu-item index="1-3">选项3</el-menu-item>
        </el-menu-item-group>
        <el-submenu index="1-4">
          <template slot="title">选项4</template>
          <el-menu-item index="1-4-1">选项1</el-menu-item>
        </el-submenu>
      </el-submenu>
      <el-menu-item index="2">
        <i class="el-icon-menu"></i>
        <span slot="title">导航二</span>
      </el-menu-item>
      <el-menu-item index="3" disabled>
        <i class="el-icon-document"></i>
        <span slot="title">导航三</span>
      </el-menu-item>
      <el-menu-item index="4">
        <i class="el-icon-setting"></i>
        <span slot="title">导航四</span>
      </el-menu-item>
    </el-menu>
  </el-col>
</el-row>
```

不要给自己设限！因为我是有无限的可能！

## 十三、抽取侧栏菜单模块

layout文件夹下创建left-menu.vue，将上一节的侧栏代码移过去，不要耦合在一起一大堆。

然后去掉< el-row>和< el-col>这两个标签。

在main.js里添加如下代码引入这个vue组件：

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'
/*饿了么UI库*/
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import leftMenu from './layout/left-menu'  // 新增1

Vue.config.productionTip = false;
Vue.use(ElementUI);
Vue.component("leftMenu", leftMenu);      // 新增2
new Vue({
    router,
    render: h => h(App),
}).$mount('#app') //mount挂载，#是选择器的意思，app是App.vue里id为app
```

接着你就可以在其他vue文件里使用 leftMenu 了！

怎么用呢？在base-view.vue里原本的位置加上 < left-menu>< /left-menu> 即可。

## 十四、修改菜单结构

default-active 要作用在 el-menu-item 这个标签上才有效果。

这一节做饿了么组件的侧栏的实现，注意 < el-menu-item>、< el-submenu>、< el-menu>标签还有其下属性的使用。

## 十五、补全和获取路由表中的数据

通过获取到routes里的

在index.js的routes里可以自己添加字段，例如这里要针对不同权限的用户做一个隐藏，就可以添加一个hidden的标志位（或者shown也行），通过这个boolean值来控制显示和隐藏。

再添加个 name 字段！

icon也直接在饿了么组件里寻找。

js的语法也是比较难懂：

left-menu.vue

```html
<script>
import {routes} from '../router';

export default {
  data() {
    return {
      menuList: []   //这种也是定义对象的方式
    }
  },
  mounted() {
    // routes[1]的话就是login
    let menuList = routes[0];
    this.menuList = menuList.children; //this不能少
    console.log(this.menuList);
  }
}
</script>
```

## 十六、渲染侧栏菜单

一开始侧栏的做法是固定写死的，后续会改成根据登录状态、账号信息等动态添加侧栏item，那样才是正确的。代码如下：

```html
<template>
  <div>
    <!--遍历菜单内容：一种是有子内容的，另一种是没有的-->
    <el-menu default-active="1-1" unique-opened='true' class="el-menu-vertical-demo" @open="handleOpen" @close="handleClose">
        <el-submenu index="1">
        <template slot="title">
          <i class="el-icon-location"></i>
          <span>用户</span>
        </template>
        <el-menu-item index="1-1">
          <i class="el-icon-star-on"></i>
          <span slot="title">子选项1</span>
        </el-menu-item>
        <el-menu-item index="1-2">
          <i class="el-icon-star-off"></i>
          <span slot="title">子选项2</span>
        </el-menu-item>
        <el-menu-item index="1-3">
          <i class="el-icon--left"></i>
          <span slot="title">子选项3</span>
        </el-menu-item>
      </el-submenu>

      <el-submenu index="2">
        <template slot="title">
          <i class="el-icon-location"></i>
          <span>运营</span>
        </template>
        <el-menu-item index="2-1">
          <i class="el-icon-star-on"></i>
          <span slot="title">子选项1</span>
        </el-menu-item>
        <el-menu-item index="2-2">
          <i class="el-icon-star-off"></i>
          <span slot="title">子选项2</span>
        </el-menu-item>
        <el-menu-item index="2-3">
          <i class="el-icon--left"></i>
          <span slot="title">子选项3</span>
        </el-menu-item>
      </el-submenu>

      <el-submenu index="3">
        <template slot="title">
          <i class="el-icon-location"></i>
          <span>设置</span>
        </template>
        <el-menu-item index="3-1">
          <i class="el-icon-star-on"></i>
          <span slot="title">子选项1</span>
        </el-menu-item>
        <el-menu-item index="3-2">
          <i class="el-icon-star-off"></i>
          <span slot="title">子选项2</span>
        </el-menu-item>
        <el-menu-item index="3-3">
          <i class="el-icon--left"></i>
          <span slot="title">子选项3</span>
        </el-menu-item>
      </el-submenu>

      <el-menu-item index="4">
        <i class="el-icon-menu"></i>
        <span slot="title">导航二</span>
      </el-menu-item>
    </el-menu>
  </div>
</template>
```

然后接着是根据menuList中的值来进行动态渲染所得到的（注意v-for），最终left-menu代码如下：

```html
<template>
  <div>
    <!--遍历菜单内容：一种是有子内容的，另一种是没有的-->
    <el-menu default-active="1-1" unique-opened='true' class="el-menu-vertical-demo" @open="handleOpen" @close="handleClose">
      <template v-for="(item,index) in menuList">
        <el-menu-item :index="index" v-if="!item.children" :key="index">
          <i :class="item.icon"></i>
          <span slot="title">{{item.name}}</span>
        </el-menu-item>

        <el-submenu v-else :index="index" :key="index">
          <template slot="title">
            <i :class="item.icon"></i>
            <span>{{item.name}}</span>
          </template>
          <el-menu-item v-for="(subItem,subIndex) in item.children" :index="index+'-'+subIndex" :key="subIndex">
            <i :class="subItem.icon"></i>
            <span slot="title">{{subItem.name}}</span>
          </el-menu-item>
        </el-submenu>
      </template>
    </el-menu>
  </div>
</template>
```

## 十七、菜单的切换与显示隐藏的控制

给那些需要添加v-for的元素加上 router-link 标签，并且添加上 to 属性来表示指向哪个页面：

```html
<template>
  <div>
    <!--这种做法是固定写死的，后续会改成根据登录状态、账号信息等动态添加侧栏item，那样才是正确的-->

    <!--遍历菜单内容：一种是有子内容的，另一种是没有的-->
    <el-menu default-active="0" class="el-menu-vertical" unique-opened='true' @open="handleOpen" @close="handleClose">
      <template v-for="(item,index) in menuList">
          <!-- 注意这里！-->
        <router-link :to='item.path' v-if="!item.children && !item.hidden" :key="index">
          <el-menu-item :index="index">
            <i :class="item.icon"></i>
            <span slot="title">{{item.name}}</span>
          </el-menu-item>
        </router-link>

        <el-submenu v-if='item.children && !item.hidden' :index="index" :key="index">
          <template slot="title">
            <i :class="item.icon"></i>
            <span>{{item.name}}</span>
          </template>
          <router-link :to='item.path+"/"+subItem.path' v-for="(subItem,subIndex) in item.children" :key="subIndex">
            <el-menu-item :index="index+'-'+subIndex" v-if='!subItem.hidden'>
              <i :class="subItem.icon"></i>
              <span slot="title">{{subItem.name}}</span>
            </el-menu-item>
          </router-link>
        </el-submenu>
      </template>
    </el-menu>
  </div>
</template>
```

注意如何判断要不要在html中的元素加 : 呢？看你是不是要绑定数据（v-bind），如果你需要用到数据动态改变，那很明显要绑定。（或者这么理解：只要不是静态写死的，你都得绑定）

同样见上面代码的 hidden 属性。

## 十八、管理中心顶部UI

很明显每个模块都独立出来，所以把 base-view.vue 中的 < el-header> 抽取出来，命名为 top-header.vue：

```vue
<template>
    <div>
      蔡芳汉的博客
    </div>
</template>

<script>

</script>

<style>

</style>
```

然后就是在 main.js 里去注册为组件：

```javascript
import topHeader from './layout/top-header'

Vue.component("topHeader", topHeader);
```

然后在 base-view.vue 里使用：

```html
<el-header id="admin-header">
     <top-header></top-header>
</el-header>
```

查看页面颜色使用Elements查看（Console旁边的那个）

注意使用class来操控对应的标签。

### 注意，到这里当前的代码可以作为一个通用模板！也就是导航菜单的模板~

## 十九、登录界面的UI

输入 http://localhost:8080/#/login ，可以发现登录是和 http://localhost:8080/#/content/manage-image 同级的一个页面。（注意看Url）

这一节主要是去制作登录界面，去调整UI，理解div和css配合搭建页面的情况。

Form代表表单，同样也是采用 element-UI 来实现：https://element.eleme.cn/#/zh-CN/component/form

http://192.168.220.141:2020/swagger-ui.html

![](C:\Users\Fin\WebProjects\FrontEndNotes\img\用户接口.PNG)

login.vue 代码如下：

```vue
<template>
    <div class="login-box">
        <!--首页页面-->
        <div class="login-header">
            <div class="header-center center">
                <div class="login-logo">
                    吐司单词 | 登录
                </div>
            </div>
        </div>
        <div class="login-content">
            <div class="content-center center">
                <el-row :gutter='20'>
                    <el-col :span='8'>
                        <el-form :label-position="left" label-width="100px">
                            <el-form-item label="帐号" required>
                                <el-input v-model="user.account"></el-input>
                            </el-form-item>
                            <el-form-item label="密码" required>
                                <el-input v-model="user.password" placeholder="xxx"></el-input>
                            </el-form-item>
                            <el-form-item label="验证码" required>
                                <el-input v-model="loginInfo.verifyCode"></el-input>
                            </el-form-item>
                            <el-form-item>
                                <el-button type="primary" @click="login" size='small'>登 录</el-button>
                            </el-form-item>
                        </el-form>
                    </el-col>
                </el-row>
            </div>
        </div>
        <div class="login-bottom">

        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                user: {
                    account: '',
                    password: ''
                },
                loginInfo: {
                    verifyCode: ''
                }
            }
        },
        method: {
            login() {

            }
        }
    }

</script>

<style>
    .center {
        margin: 0 auto;
        width: 1140px;
    }

    .login-header {
        background-color: dodgerblue;
        height: 46px;
        width: 100%;
        margin-bottom: 20px;
    }

    .header-center {
        line-height: 46px;
    }

    .login-logo {
        color: white;
        font-size: 16px;
        font-weight: 600;
    }

    .content-center {
        width: 1146px;
        height: 300px;
        background: white;
        box-shadow: 0 1px 10px #afafaf;
        border-radius: 6px;
        padding-top: 20px;
        padding-left: 20px;
    }

    /* 使用label也是可以覆盖到的，这个label是查看网页获取到的，因为是饿了么的组件 */
    .el-form-item__label {
        background: #FBFBFB;
        text-align: center;
        border: solid #E6E6E6 1px;
        border-radius: 6px;
    }

    .el-input__inner {
        height: 42px;
        margin-left: 2px;
    }
</style>
```

## 二十、登录UI的验证码

反正就加 class 来获取到对应标签，然后给它添加样式。

代码如下：

```vue
<template>
    <div class="login-box">
        <!--首页页面-->
        <div class="login-header">
            <div class="header-center center">
                <div class="login-logo">
                    吐司单词 | 登录
                </div>
            </div>
        </div>
        <div class="login-content">
            <div class="content-center center">
                <el-row :gutter='20'>
                    <el-col :span='8'>
                        <el-form :label-position="left" label-width="100px">
                            <el-form-item label="帐号" required>
                                <el-input v-model="user.account"></el-input>
                            </el-form-item>
                            <el-form-item label="密码" required>
                                <el-input v-model="user.password" placeholder="xxx"></el-input>
                            </el-form-item>
                            <el-form-item label="验证码" required>
                                <el-input v-model="loginInfo.verifyCode"></el-input>
                                <img :src="imgPath" @click="updateVerifyCode">
                            </el-form-item>
                            <el-form-item>
                                <el-button type="primary" @click="login" size='small'>登 录</el-button>
                            </el-form-item>
                        </el-form>
                    </el-col>
                </el-row>
            </div>
        </div>
        <div class="login-bottom">

        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                user: {
                    account: '',
                    password: ''
                },
                loginInfo: {
                    verifyCode: '',
                    from: '',
                    to: ''
                },
                imgPath: '',
                key:''
            }
        },
        method: {
            login() {

            },
            updateVerifyCode() {
                this.imgPath = 'http://192.168.220.141:2020//user/captcha?captcha_key=' + this.key;
                console.log(this.imgPath)
            }
        },
        // 在mounted的时候就要去初始化验证码图片路径
        mounted() {
            // 注意这里，建信的项目也是这样写前端的
            this.key = Date.parse(new Date())
            this.updateVerifyCode
        }
    }

</script>

<style>
    .center {
        margin: 0 auto;
        width: 1140px;
    }

    .login-header {
        background-color: dodgerblue;
        height: 46px;
        width: 100%;
        margin-bottom: 20px;
    }

    .header-center {
        line-height: 46px;
    }

    .login-logo {
        color: white;
        font-size: 16px;
        font-weight: 600;
    }

    .content-center {
        width: 1146px;
        height: 300px;
        background: white;
        box-shadow: 0 1px 10px #afafaf;
        border-radius: 6px;
        padding-top: 20px;
        padding-left: 20px;
    }

    /* 使用label也是可以覆盖到的，这个label是查看网页获取到的，因为是饿了么的组件 */
    .el-form-item__label {
        background: #FBFBFB;
        text-align: center;
        border: solid #E6E6E6 1px;
        border-radius: 6px;
    }

    .el-input__inner {
        height: 42px;
        margin-left: 2px;
    }
</style>
```

我对这个去拿后台的url的形式有点陌生，这个地址需要认真学习。

## 二十一、axios进行网络数据请求

如何提交数据呢？使用axios：https://cn.vuejs.org/v2/cookbook/using-axios-to-consume-apis.html

在使用npm install时，--save：将保存配置信息到pacjage.json。默认为dependencies节点中。

```javascript
const axios = require('axios')s

axios
    .get("'https://api.coindesk.com/v1/bpi/currentprice.json'")
    .then(response => {
        console.log(response)
        this.dream = response.data.bpi
})
```

除此之外，还可以参考：https://www.sunofbeach.net/a/1201366916766224384 拿这个来做练手，看看怎么拿到数据。

## 二十二、发起登录请求

注意哥跟后端的接口一致！

也是用axios。

## 二十三、解决访问跨域问题

配置文件：https://cli.vuejs.org/config/

在README.md同级下面创建vue.config.js文件，复制以下代码到文件里：

```javascript
module.exports = {
  devServer: {
    proxy: {
      '^/api': {
        target: '<url>',
        ws: true,
        changeOrigin: true
      },
      '^/foo': {
        target: '<other_url>'
      }
    }
  }
}
```

更改以上模板，如下：

```javascript
module.exports = {
    devServer: {
        proxy: {
            '/user': { //修改1
                target: 'http://192.168.220.141:2020/', //修改2
                //ws: true, 
                changeOrigin: true
            },
            // '^/foo': {
            //     target: '<other_url>'
            // }
        }
    }
}
```

然后在login.vue里把相关的url都删掉，只保留最后的拼接符。

登录成功后查看 console 里的application→cookies，可以找到本地的。

## 二十四、

