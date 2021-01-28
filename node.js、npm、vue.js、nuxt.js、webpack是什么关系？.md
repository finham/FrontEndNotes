# node.js、npm、vue.js、nuxt.js、webpack是什么关系？

必须要知道

## 一、node.js

定义：node.js是一个基于Chrome V8引擎的JavaScript运行时。

JavaScript以前是在前端，需要浏览器的解析引擎来执行，需要浏览器的解析器。

node.js也是js，只不过，借助Chrome V8引擎，让js跑在了服务器端。这样，作用就大大地了！js可以做服务器了。

当然，会有一些问题，例如没有window对象。

也就是说，做前端的同学，也可以去写服务器了。

这是node.js的官网：https://nodejs.org/zh-cn/，点进去安装！！！

## 二、npm

定义：a JavaScript package manager

一个javascript库的管理器

学过java或者说会android的同学都知道，package吧，包。比如说jar包，比如说aar包，这些都是包，C/C++里我们常称为库，lib。

而java web里有中央仓库，而npm也可以理解为一个远程仓库，有各种各样的包。

在android/web我们在gradle里添加各种依赖然后sync就下来了

在java web里我们使用maven进行管理的话在pom文件里添加依赖，import包就下来了

在使用npm管理的前端项目里，在package.json里添加依赖，install一下就下来了。或者可以直接安装也可以。

没有学完javaWeb或者其他开发的同学，可以把npm理解为图书馆。里面有很多封装好的javascript库，也就是有各种功能的库。

当然还有其他的例如yml，构建项目时就会让我们选择用什么来管理包。

### 安装npm

安装完node后

使用

```shell
node -v 
```

查看node的版本

使用

```shell
npm -v
```

查看npm的版本

然后但是使用npm下载包的话，可能会比较慢。所以我们要配置cnpm（c就是china的意思）

在命令行输入：

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

安装完成后，再使用npm命令可以替换成cnpm了， 这样子速度会比较快。

官网：[npm官网](https://www.npmjs.com/package/npm)

[npm 是干什么的？](https://zhuanlan.zhihu.com/p/24357770)

## 三、Vue.js

一个渐进式 JavaScript 框架。

这个东西怎么说呢？真的好棒！重点还是我们中国人写的，哈哈！

我们的网站现在这个编辑器，用户中心这个后台就是使用Vue.js写的。

一般我们用来做管理系统的前端，或者类似于小程序那样的应用（或者说也可以放在App上使用的前端界面）。后面我们会在课程里跟大家讲。

Vue是充当MVVM中的VM的作用，用于双向绑定Model和View => 这样是不是好理解了。

## 四、Nuxt.js

这是一个基于Vue.js的框架。我们使用Vue.js写用户前端可以实现吗？可以的，但是呢，对搜索引擎不友好，不利于搜索引擎收录你的网站。（这个其实比较难理解什么意思）

为什么呢？因为Vue.js打包出来的就是.js文件和资源文件，到了客户端再去拉去数据进行渲染。国内的搜索引擎目前还没有这个能力去解析js拉取的数据。

[Vue.js官网](https://cn.vuejs.org/)

所以就有了Nuxt.js

Nuxt.js也是渲染，不同的是它在服务端进行渲染。这样子，到了浏览器的数据已经有了。所以搜索引擎访问的时候，就可以知道你这个网页是什么内容了。有利于收录，别人通过搜索引擎就更容易找到你的网站。

## 五、Webpack

web网络，pack打包。webpack是一个打包工具，有了它，我们就可以把前端当做android开发那样子去做了。

它会把js、css、图片等等给打包成一个东西。而以前是把html、css、js写好后发给后台同学，然后后台同学集成到项目里，我们发起请求可以访问到。

现在前后端分离了，前端的同学也可以像开发Android一样啦！前端就是前端哈哈！（用Android来对比真的是太好理解了），但是不是生成apk，而是生成一个个js。

而不是像从前那样，写一大堆相互关联的文件。不过现在也是，只不过可以管理起来，可以使用一些新的语法，通过webpack转换成浏览器识别的内容。可以打包发布，更加有条理。

可以说这是前端百家争鸣的一个结果吧。现在做前端真的是太幸福了。

## 关系

这些都是javascript的进化产物，npm、webpack为这些产物服务，除此之外，还有angular.js、React.js...

这些产物让前端变得越来越香了，甚至可以抢占移动端的份额。