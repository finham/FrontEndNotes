# Vue基础

[TOC]

本次完成一个ToDo的网页。源代码地址：https://github.com/mdn/todo-vue

## 一、项目结构 & Vue基础语法

| 文件/文件夹        | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| .eslintrc.js       | 这个是 [eslint](https://eslint.org/) 的配置文件，可以通过它来管理你的校验规则。 |
| babel.config.js    | 这个是 [Babel](https://babeljs.io/) 的配置文件，可以在开发中使用 JavaScript 的新特性，并且将其转换为在生产环境中可以跨浏览器运行的旧语法代码。你也可以在这个里配置额外的 babel 插件。 |
| .browserslistrc    | 这个是 [Browserslist](https://github.com/browserslist/browserslist) 的配置文件，可以通过它来控制需要对哪些浏览器进行支持和优化。 |
| public             | 这个目录包含一些在 [Webpack](https://webpack.js.org/) 编译过程中没有加工处理过的文件（有一个例外：index.html 会有一些处理）。 |
| public/favicon.ico | 项目的图标，当前就是一个 Vue 的 logo。                       |
| public/index.html  | 这是应用的模板文件，Vue 应用会通过这个 HTML 页面来运行，也可以通过 lodash 这种模板语法在这个文件里插值。**注意：这个不是负责管理页面最终展示的模板，而是管理 Vue 应用之外的静态 HTML 文件，一般只有在用到一些高级功能的时候才会修改这个文件。** |
| src                | Vue 应用的核心代码目录                                       |
| src/main.js        | 这是应用的入口文件。目前它会初始化 Vue 应用并且指定将应用挂载到 `index.html` 文件中的哪个 HTML 元素上。通常还会做一些注册全局组件或者添额外的 Vue 库的操作。 |
| src/App.vue        | 这是 Vue 应用的根节点组件                                    |
| src/components     | 这是用来存放自定义组件的目录，目前里面会有一个示例组件。     |
| src/assets         | 这个目录用来存放像 CSS 、图片这种静态资源，但是因为它们属于代码目录下，所以可以用 webpack 来操作和处理。意思就是你可以使用一些预处理比如 [Sass/SCSS](https://sass-lang.com/) 或者 [Stylus](https://stylus-lang.com/)。 |
|                    |                                                              |
|                    |                                                              |
|                    |                                                              |

注意：根据创建项目时候的一些配置项，可能会有一些其他的预设目录（例如，如果你选择了路由配置，会看到一个 views 的文件夹）

每个Vue应用都通过实例化Vue来实现，语法如下：

```vue
<div id="vue_det">
    <h1>site : {{site}}</h1>
    <h1>url : {{url}}</h1>
    <h1>{{details()}}</h1>
</div>
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue_det',
        data: {
            site: "菜鸟教程",
            url: "www.runoob.com",
            alexa: "10000"
        },
        methods: {
            details: function() {  //这里也可以直接写details(){}
                return  this.site + " - 学的不仅是技术，更是梦想！";
            }
        }
    })
</script>
```

可以看到在 Vue 构造器中有一个el 参数，它是 DOM 元素中的 id。在上面实例中 id 为 vue_det，在 div 元素中：

```html
<div id = "vue_det"></div>
```

这意味着我们接下来的改动全部在以上指定的 div 内部，div 外部不受影响。

如何定义数据对象：

* data：用于定义属性，实例中有三个属性分别为：site、url、alexa。
* methods：用于定义的函数，可以通过 return 来返回函数值。
* {{ }}：用于输出对象属性和函数返回值。

当一个 Vue 实例被创建时，它向 Vue 的响应式系统中加入了其 data 对象中能找到的所有的属性。当这些属性的值发生改变时，html 视图将也会产生相应的变化。怎么理解呢？看如下代码：

```vue
<div id="vue_det">
    <h1>site : {{site}}</h1>
    <h1>url : {{url}}</h1>
    <h1>Alexa : {{alexa}}</h1>
</div>
<script type="text/javascript">
// 我们的数据对象
var data = { site: "菜鸟教程", url: "www.runoob.com", alexa: 10000}
var vm = new Vue({
    el: '#vue_det',
    data: data
})

// 它们引用相同的对象！
document.write(vm.site === data.site) // true
document.write("<br>")
// 设置属性也会影响到原始数据
vm.site = "Runoob"
document.write(data.site + "<br>") // Runoob
 
// 反之亦然
data.alexa = 1234
document.write(vm.alexa) // 1234
</script>
```

除了数据属性，Vue 实例还提供了一些有用的实例属性与方法。**它们都有前缀 $**，以便与用户定义的属性区分开来。例如：

```vue
<div id="vue_det">
    <h1>site : {{site}}</h1>
    <h1>url : {{url}}</h1>
    <h1>Alexa : {{alexa}}</h1>
</div>
<script type="text/javascript">
// 我们的数据对象
var data = { site: "菜鸟教程", url: "www.runoob.com", alexa: 10000}
var vm = new Vue({
    el: '#vue_det',
    data: data
})
 
document.write(vm.$data === data) // true
document.write("<br>") 
document.write(vm.$el === document.getElementById('vue_det')) // true，注意这里
</script>
```

* 也就是说，如果你要访问vue自带的el，data，甚至props？，都得加上$来区分

Vue.js 使用了基于 HTML 的模版语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。

Vue.js 的核心是一个允许你采用简洁的模板语法来声明式的将数据渲染进 DOM 的系统。

结合响应系统，在应用状态改变时， Vue 能够智能地计算出重新渲染组件的最小代价并应用到 DOM 操作上。

### 插值

#### 文本

数据绑定最常见的形式就是使用 {{...}}（双大括号）的文本插值：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://unpkg.com/vue/dist/vue.js"></script>
</head>
<body>
<div id="app">
  <p>{{ message }}</p>
</div>

<script>
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!'
  }
})
</script>
</body>
</html>
```

#### Html

使用 v-html 指令用于输出 html 代码：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <div v-html="message"></div>
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
    message: '<h1>菜鸟教程</h1>'
  }
})
</script>
</body>
</html>
```

#### 属性

HTML 属性中的值应使用 v-bind 指令。

以下实例判断 use 的值，如果为 true 使用 class1 类的样式，否则不使用该类：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
</head>
<style>
.class1{
  background: #444;
  color: #eee;
}
</style>
<body>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>

<div id="app">
  <label for="r1">修改颜色</label><input type="checkbox" v-model="use" id="r1">
  <br><br>
  <div v-bind:class="{'class1': use}">
    v-bind:class 指令
  </div>
</div>
    
<script>
new Vue({
    el: '#app',
  data:{
      use: false
  }
});
</script>
</body>
```

#### 表达式

Vue.js 都提供了完全的 JavaScript 表达式支持。

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
	{{5+5}}<br>
	{{ ok ? 'YES' : 'NO' }}<br>
	{{ message.split('').reverse().join('') }}
	<div v-bind:id="'list-' + id">菜鸟教程</div>
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
	ok: true,
    message: 'RUNOOB',
	id : 1
  }
})
</script>
</body>
</html>
```

输出结果：

```shell
10
YES
BOONUR
菜鸟教程
```

### 指令

指令是带有 v- 前缀的特殊属性。

指令用于在表达式的值改变时，将某些行为应用到 DOM 上。如下例子：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <p v-if="seen">现在你看到我了</p>
    <template v-if="ok">
      <h1>菜鸟教程</h1>
      <p>学的不仅是技术，更是梦想！</p>
      <p>哈哈哈，打字辛苦啊！！！</p>
    </template>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    seen: true,
    ok: true
  }
})
</script>
</body>
</html>
```

```html
现在你看到我了

菜鸟教程
学的不仅是技术，更是梦想！

哈哈哈，打字辛苦啊！！！
```

这里， v-if 指令将根据表达式 seen 的值(true 或 false )来决定是否插入 p 元素。

#### 参数

参数在指令后以冒号指明。例如， v-bind 指令被用来响应地更新 HTML 属性：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <pre><a v-bind:href="url">菜鸟教程</a></pre>
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
    url: 'http://www.runoob.com'
  }
})
</script>
</body>
</html>
```

在这里 href 是参数，告知 v-bind 指令将该元素的 href 属性与表达式 url 的值绑定。

另一个例子是 v-on 指令，它用于监听 DOM 事件：

```html
<a v-on:click="doSomething">
```

在这里参数是监听的事件名。

#### 修饰符

修饰符是以半角句号 **.** 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。

例如，**.prevent** 修饰符告诉 **v-on** 指令对于触发的事件调用 **event.preventDefault()**：

```html
<form v-on:submit.prevent="onSubmit"></form>
```

### 用户输入

在 input 输入框中我们可以使用 v-model 指令来实现双向数据绑定：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <p>{{ message }}</p>
    <input v-model="message">
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
    message: 'Runoob!'
  }
})
</script>
</body>
</html>
```

**v-model 指令用来在 input、select、textarea、checkbox、radio 等表单控件元素上创建双向数据绑定，根据表单上的值，自动更新绑定的元素的值。**

按钮的事件我们可以使用 v-on 监听事件，并对用户的输入进行响应。

以下实例在用户点击按钮后对字符串进行反转操作：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <p>{{ message }}</p>
    <button v-on:click="reverseMessage">反转字符串</button>
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
    message: 'Runoob!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>
</body>
</html>
```

this.message.split('')：[ "R", "u", "n", "o", "o", "b", "!" ]

this.message.split('').reverse()：[ "!", "b", "o", "o", "n", "u", "R" ]

this.message.split('').reverse().join('')：!boonuR

### 过滤器

Vue.js 允许你自定义过滤器，被用作一些常见的文本格式化。

由"管道符"指示, 格式如下：

```html
<!-- 在两个大括号中 -->
{{ message | capitalize }}

<!-- 在 v-bind 指令中 -->
<div v-bind:id="rawId | formatId"></div>
```

过滤器函数接受表达式的值作为第一个参数。

以下实例对输入的字符串第一个字母转为大写：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
  {{ message | capitalize }}
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
	message: 'runoob'
  },
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
})
</script>
</body>
</html>
```

过滤器可以串联：

```html
{{ message | filterA | filterB }}
```

过滤器是 JavaScript 函数，因此可以接受参数：

```html
{{ message | filterA('arg1', arg2) }}
```

这里，message 是第一个参数，字符串 'arg1' 将传给过滤器作为第二个参数， arg2 表达式的值将被求值然后传给过滤器作为第三个参数。

### 缩写

Vue.js 为两个最为常用的指令提供了特别的缩写：

```html
<!-- 完整语法 -->
<a v-bind:href="url"></a>
<!-- 缩写 -->
<a :href="url"></a>
```

```html
<!-- 完整语法 -->
<a v-on:click="doSomething"></a>
<!-- 缩写 -->
<a @click="doSomething"></a>
```

### 条件判断

条件判断使用 v-if 指令：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <p v-if="seen">现在你看到我了</p>
    <template v-if="ok">
      <h1>菜鸟教程</h1>
      <p>学的不仅是技术，更是梦想！</p>
      <p>哈哈哈，打字辛苦啊！！！</p>
    </template>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    seen: true,
    ok: true
  }
})
</script>
</body>
</html>
```

这里， v-if 指令将根据表达式 seen 的值(true 或 false )来决定是否插入 p 元素。

在字符串模板中，如 Handlebars ，我们得像这样写一个条件块：

```html
<!-- Handlebars 模板 -->
{{#if ok}}
  <h1>Yes</h1>
{{/if}}
```

可以用 v-else 指令给 v-if 添加一个 "else" 块：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
	<div v-if="Math.random() > 0.5">
	  Sorry
	</div>
	<div v-else>
	  Not sorry
	</div>
</div>
	
<script>
new Vue({
  el: '#app'
})
</script>
</body>
</html>
```

v-else-if 在 2.1.0 新增，顾名思义，用作 v-if 的 else-if 块。可以链式的多次使用：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <div v-if="type === 'A'">
      A
	</div>
	<div v-else-if="type === 'B'">
	  B
	</div>
	<div v-else-if="type === 'C'">
	  C
	</div>
	<div v-else>
	  Not A/B/C
	</div>
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
    type: 'C'
  }
})
</script>
</body>
</html>
```

我们也可以使用 v-show 指令来根据条件展示元素：

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <h1 v-show="ok">Hello!</h1>
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
    ok: true
  }
})
</script>
</body>
</html>
```

### 循环

循环使用 v-for 指令。

v-for 指令需要以 **site in sites** 形式的特殊语法， sites 是源数据数组并且 site 是数组元素迭代的别名。

v-for 可以绑定数据到数组来渲染一个列表：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
  <ol>
    <li v-for="site in sites">
      {{ site.name }}
    </li>
  </ol>
</div>

<script>
new Vue({
  el: '#app',
  data: {
    sites: [
      { name: 'Runoob' },
      { name: 'Google' },
      { name: 'Taobao' }
    ]
  }
})
</script>
</body>
</html>
```

输出：

```shell
1.Runoob
2.Google
3.Taobao
```

在模板中使用 v-for：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
  <ul>
    <template v-for="site in sites">
      <li>{{ site.name }}</li>
      <li>--------------</li>
    </template>
  </ul>
</div>

<script>
new Vue({
  el: '#app',
  data: {
    sites: [
      { name: 'Runoob' },
      { name: 'Google' },
      { name: 'Taobao' }
    ]
  }
})
</script>
</body>
</html>
```

输出如下：

- Runoob
- \--------------
- Google
- \--------------
- Taobao
- \--------------

#### 用v-for来迭代对象

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
  <ul>
    <li v-for="value in object">
    {{ value }}
    </li>
  </ul>
</div>

<script>
new Vue({
  el: '#app',
  data: {
    object: {
      name: '菜鸟教程',
      url: 'http://www.runoob.com',
      slogan: '学的不仅是技术，更是梦想！'
    }
  }
})
</script>
</body>
</html>
```

- 菜鸟教程
- http://www.runoob.com
- 学的不仅是技术，更是梦想！

你也可以提供第二个的参数为键名：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
  <ul>
    <li v-for="(value, key) in object">
    {{ key }} : {{ value }}
    </li>
  </ul>
</div>

<script>
new Vue({
  el: '#app',
  data: {
    object: {
      name: '菜鸟教程',
      url: 'http://www.runoob.com',
      slogan: '学的不仅是技术，更是梦想！'
    }
  }
})
</script>
</body>
</html>
```

- name : 菜鸟教程
- url : http://www.runoob.com
- slogan : 学的不仅是技术，更是梦想！

第三个参数为索引：

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
  <ul>
    <li v-for="(value, key, index) in object">
     {{ index }}. {{ key }} : {{ value }}
    </li>
  </ul>
</div>

<script>
new Vue({
  el: '#app',
  data: {
    object: {
      name: '菜鸟教程',
      url: 'http://www.runoob.com',
      slogan: '学的不仅是技术，更是梦想！'
    }
  }
})
</script>
</body>
</html>
```

- 0. name : 菜鸟教程
- 1. url : http://www.runoob.com
- 2. slogan : 学的不仅是技术，更是梦想！

#### v-for迭代证书

```vue
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
  <ul>
    <li v-for="n in 10">
     {{ n }}
    </li>
  </ul>
</div>

<script>
new Vue({
  el: '#app'
})
</script>
</body>
</html>
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10

## 二、.vue文件

就像很多其他的前端框架一样，组件是构建 Vue 应用中非常重要的一部分。

组件可以把一个很大的应用程序拆分为独立创建和管理的不相关区块，然后彼此按需传递数据，这些小的代码块可以方便更容易的理解和测试。

在其他框架都鼓励把模板、逻辑和样式的代码区分成不同文件的时候，Vue 却反其道行之。

使用[单文件组件](https://vuejs.org/v2/guide/single-file-components.html)，Vue 把模板、相关脚本和 CSS 一起整合放在 `.vue` 结尾的一个单文件中。这些文件最终会通过 JS 打包工具（例如 Webpack）处理，这意味着你可以使用构建时工具。你可以使用比如 Babel、TypeScript、SCSS 等来创建更多复杂的组件。

另外，使用 Vue CLI 创建的项目被配置为在开箱即用的情况下借助 Webpack 使用 .vue 文件。实际上，如果您查看我们使用 CLI 创建的项目中的 src 文件夹，会看到第一个.vue 文件：App.vue。

打开 App.vue 文件，可以看到有三部分组成 `<template>`, `<script>`, and `<style>`，分别包含了组件的模板、脚本和样式相关的内容。所有的单文件组件都是这种类似的基本结构。

`<template>` 包含了所有的标记结构和组件的展示逻辑。**template 可以包含任何合法的 HTML，以及一些我们接下来要讲的 Vue 特定的语法。**

> 注意：通过设置 `<template>` 标签的 `lang` 属性，例如可以通过设置 `<template lang="pug">` 就可以在使用 Pub 模板来替代标准 HTML。在本教程中我们依然会使用标准 HTML，但是这种方式可行你还是值得知道。

`<script>`包含组件中所有的**非显示逻辑**。最重要的是， `<script>` 标签需要默认导出一个 JS 对象。该对象是您在本地注册组件、定义属性、处理本地状态、定义方法等的地方。在构建阶段这个对象会被处理和转换（包含 template 模板）成为一个有 `render()` 函数的 Vue 组件。

```vue
<script>
import HelloWorld from './components/HelloWorld.vue';

export default {
  name: 'app',
  components: {
    //You can register components locally here.
    HelloWorld
  }
};
</script>
```

对于 `App.vue`，我们的默认导出将组件的名称设置为 `app` ，并通过将 `HelloWorld` 组件添加到 `components` 属性中来注册它。

以这种方式注册组件时，就是在本地注册。

本地注册的组件只能在注册它们的组件内部使用，因此您需要将其导入并注册到使用它们的每个组件文件中。

这对于拆包 / tree shaking（译者注：一种减小包体积优化方式）很有用，因为并不是应用程序中的每个页面都不一定需要每个组件。

组件的 CSS 应该写在 `<style>` 标签里，如果你添加了 `scoped` 属性，形如 `<style scoped>` ，Vue 会把样式的范围限制到单文件组件的内容里。这个类似 CSS-in-JS 的解决方案，不过允许你书写文本格式的 CSS了。

> `<style lang ="scss">` 将允许您在样式信息中使用 SCSS 语法。

Vue CLI 带有内置的开发服务器。这样一来，您就可以在本地运行您的应用程序，这样就可以轻松对其进行测试，而无需自己配置服务器。 CLI 会以 npm 脚本的形式将 `serve` 命令添加到项目的 `package.json `文件中，因此您可以轻松地运行它。（WebStorm上使用Add Configuration来添加运行配置）

在您的终端中，尝试运行 `npm run serve`。

## 三、开始编码——认识旧文件

新项目里有如下几个文件：

components / HelloWorld.vue

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

App.vue

```vue
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
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

删掉HelloWorld的使用。

## 四、创建一个ToDoItem组件

1. 创建一个`ToDoItem.vue`的新文件。在你的代码编辑器中打开该文件。

2. 通过在文件顶部添加`<template></template>`来创建组件的模板部分。

3. 在你的模板部分下面创建一个`<script></script>`部分。在`<script>`标签内，添加一个默认导出对象`export default {}`，这是你的组件对象。

4. 现在我们可以开始为`ToDoItem`添加实际内容了。Vue模板目前只允许一个根元素--一个元素需要包裹模板内的所有内容（Vue 3 发布后会改变这种情况）。我们将为该根元素使用一个`<div>`。现在在你的组件模板中添加一个空的`<div>`。

5. 在那个`<div>`里面，让我们添加一个`checkbox`和一个对应的`label`。给复选框添加一个`id`，并添加一个`for`属性，将复选框映射到标签上，如下图所示。

6. 目前代码如下：

   ```vue
   <template>
     <div>
       <input type="checkbox" id="todo-item" checked="false" />
       <label for="todo-item">My Todo Item</label>
     </div>
   </template>
   ```

7. 把ToDoItem添加到App.vue去：

   ```vue
   <script>
   import ToDoItem from "@/components/ToDoItem";
   export default {
     name: 'App',
     components: {
       ToDoItem
     }
   }
   </script>
   ```

8. 要在应用程序中实际展示`ToDoItem`组件，你需要在`<template>`模板内添加一个`<to-do-item>/to-do-item>`元素。请注意，组件文件名及其在JavaScript中的表示方式总是用大写驼色（例如`ToDoList`），而等价的自定义元素总是用连字符小写（例如`<to-do-list>`）。现在App.vue的template标签内应该如下：

   ```html
   <div id="app">
     <h1>To-Do List</h1>
     <ul>
       <li> 
         <ToDoItem></ToDoItem>
         <to-do-item></to-do-item>
       </li>
     </ul>
   </div>
   ```
   
   注意上述两种定义方式均可。

## 五、使用props让组件变得更有交互性

我们需要的是拥有组件状态。这可以通过添加props给我们的组件来实现。

You can think of props as being similar to inputs in a function. The value of a prop gives components an initial state that affects their display.

有两种方式来注册props：

*  just list props out as an array of strings. Each entry in the array corresponds to the name of a prop.
* define props as an object, with each key corresponding to the prop name. Listing props as an object allows you to specify default values, mark props as required, perform basic object typing (specifically around JavaScript primitive types), and perform simple prop validation.

对于本次的例子，我们采用第二种方法：

```html
<script>
  export default {
    props: {
      label: { required: true, type: String },//注意大写的String
      done: { default: false, type: Boolean } //done代表完成了没
    }
  };
</script>
```

以上代表我们我们期望每个ToDoItem组件都有一个label变量 & done变量指的是当没有一个done元素传递给ToDoItem时，done变量会有一个false的默认值。

现在让我们使用我们写好的props，添加如下代码：

```html
<template>
  <div>
    <input type="checkbox" id="todo-item" checked="false" />
    <label for="todo-item">{{label}}</label>
  </div>
</template>
```

注意{{}}是vue中特殊的语法规则，让我们把JS中表达式的结果在我们的template标签中给打印出来。

我们必须知道包含在{{}}中的是文本而不是HTML。

打开浏览器会发现现在和之前渲染的一致，但是没有一个Label来表示这个待办事项的名字！打开浏览器的console，会发现有如下的log：

```log
[Vue warn]: Missing required prop: "label"

found in

---> <ToDoItem> at src/components/ToDoItem.vue
       <App> at src/App.vue
         <Root>
```

This is because we marked the `label` as a required prop, but we never gave the component that prop — we've defined where inside the template we want it used, but we haven't passed it into the component when calling it. Let’s fix that.

在 App.vue 里给< to-do-item>添加上一个label标签，重新渲染，你会发现已经有对应的Label展示在ToDoItem旁边了。

```html
<template>
  <div id="app">
    <h1>It's my To Do List.</h1>
    <ul>
      <li>
        <ToDoItem label="这是我的第一项todo"></ToDoItem>
      </li>
    </ul>
    <ToDoItem label="xxx"></ToDoItem>
    <to-do-item label="yyy"></to-do-item>
  </div>
</template>
```

> **注意区分**：
>
> 子组件中的所有 props中的数据，都是通过父组件传递给子组件的，是只读的。
>
> 子组件中的data数据，不是通过父组件传递的，是子组件私有的，是可读可写的。
>
> 为啥props要这样做，有很多原因：让组件可以编辑props值会让debug变得困难：There are a lot of reasons for this. In part, components editing props can make debugging a challenge. If a value is passed to multiple children, it could be hard to track where the changes to that value were coming from. In addition, changing props can cause components to re-render. So mutating props in a component would trigger the component to rerender, which may in-turn trigger the mutation again.

## 六、Vue的数据对象

We have a checkbox, with an updatable label. However, we're currently not doing anything with the "done" prop — we can check the checkboxes in the UI, but nowhere in the app are we recording whether a todo item is actually done.

要实现这个功能，我们要将组件的done prop和 input 标签的 checked 元素绑定起来。但是很遗憾的是props这个东西只能单向的绑定（注意上面说的注意区分）

我们可以使用 data 来解决这个问题。The `data` property is where you can manage local state in a component, it lives inside the component object

```html
<script>
    data() {
        return {
            key: value
        }
    }
</script>
```

你会注意到 data 是个函数。This is to keep the data values unique for each instance of a component at runtime — the function is invoked separately for each component instance.

If you declared data as just an object, all instances of that component would share the same values. This is a side-effect of the way Vue registers components and something you do not want.

使用this来获取组件的props还有其他的prop。

> **Note**: Because of the way that `this` works in arrow functions (binding to the parent’s context), you wouldn’t be able to access any of the necessary attributes from inside `data` if you used an arrow function. So don’t use an arrow function for the `data` property.

在ToDoItem.vue里添加如下代码：

```html
<script>
export default {
  name: "ToDoItem",
  props: {
    label: {required: true, type: String},
    done: {default: false, type: Boolean}
  },
  data() {
    return {
      isDone: this.done  //this一定要加 & 没有分号
    }
  }
}
</script>
```

在这里Vue做了一点东西：

it binds all of your props directly to the component instance, so we don’t have to call `this.props.done`. It also binds other attributes (`data`, which you’ve already seen, and others like `methods`, `computed`, etc.) directly to the instance.

这代表着可以在template中都可以获取到，但是缺点就是需要让key都唯一，例如我们需要使用isDone来代替done。

现在我们需要将isDone attch到组件上。除了使用{{}}来绑定JS表达式到template上之外，vue还有一个方法：v-bind。

```html
v-bind:attribute="expression"
```

我们添加v-bind到我们的代码里：

```html
<input type="checkbox" id="todo-item" v-bind:checked="isDone" />
//等价于下面这个
<input type="checkbox" id="todo-item" :checked="isDone" />
```

v-bind有时候可以缩写。目前初学就不要缩写了。

然后在App.vue里面去传递done，这样就可以了。

```html
<template>
  <div id="app">
    <h1>It's my To Do List.</h1>

        <ToDoItem label="这是我的第一项todo" v-bind:done="false"></ToDoItem>

    <ToDoItem label="xxx"></ToDoItem>
    <to-do-item label="yyy"></to-do-item>
  </div>
</template>
```

注意要使用v-bind语法，否则false会被认为是字符串传过去。

## 七、给Todos一个唯一的id

我们现在给checkbox设置一个可以正常工作的checkbox了。

但是为了让辅助技术生效，必须要给

使用lodash的包来给组件加唯一的id：This package exports a function that takes in a string and appends a unique integer to the end of the prefix.

```shell
npm install --save lodash.uniqueid
```

在ToDoItem.vue中添加如下代码：

```javascript
import uniqueId from 'lodash.uniqueid'; //注意不要把这句放在export default的代码块里
```

```javascript
import uniqueId from 'lodash.uniqueid';

export default {
  props: {
    label: { required: true, type: String },
    done: { default: false, type: Boolean }
  },
  data() {
    return {
      isDone: this.done,
      id: uniqueId('todo-')
    };
  }
};
```

`uniqueId()` returns the specified prefix — `todo-` — with a unique string appended to it

然后接下来将id绑定到checkbox的id和label的for属性：

```vue
<template>
  <div>
    <input type="checkbox" :id="id" :checked="isDone" />
    <label :for="id">{{label}}</label>
  </div>
</template>
```

上面的: 都可以写详细点为v-bind:

到此，this component can be passed a label to display, will store its checked state, and will be rendered with a unique `id` each time it is called.

## 八、使用 v-for 来渲染列表

一个有效的待办事项列表需要有多个被渲染的to-do项，Vue中的`v-for` 可以用来实现这种效果。

> 它是Vue自带的指令，用于在template中实现循环，我们可以利用它将数组中的各项重复渲染成指定的特征。我们将利用它迭代待办事项列表，将其中的每一项展示为单独的ToDoItem组件。

首先我们需要准备一个待办事项数组。添加 `data` 属性到 `App.vue` 组件对象中， 它包含一个 `ToDoItems` 字段，其值是待办事项数组。在最终完成添加新的待办事项功能之前，我们可以先mock一些待办项目，每个待办项目可以用一个对象表示，这个对象含有 `name` 和 `done` 属性。

在 App.vue 里像下面这样添加一些待办项目让我们可以利用`v-for` 来对它们进行渲染。

```javascript
export default {
  name: 'app',
  components: {
    ToDoItem
  },
  data() {
    return {
      ToDoItems: [
        { label: 'Learn Vue', done: false },
        { label: 'Create a Vue project with the CLI', done: true },
        { label: 'Have fun', done: true },
        { label: 'Create a to-do list', done: false }
      ]
    };
  }
};
```

现在我们有了一个列表，可以用`v-for`去展示它们了。

指令的作用方式和元素的属性类似，就v-for而言，它类似js中的`for...in`循环，`v-for="item in items"` — `items`是你要迭代的列表， `item` 是数组中当前元素的引用。

`v-for`获取每个迭代的元素，并渲染它和它的子元素。在我们的例子中，我们用`<li>`的形式展示每一个待办事项，接下来我们会通过每个待办事项传递数据到其对应的`ToDoItem`组件。

### Key属性

在进行数据传递之前，我们要了解下`key`属性，它和`v-for`使用，用来帮助Vue标识列表中的元素，这样Vue就不需要在列表变化时重新创建它们。但是Vue需要一个唯一的标识，即`key`来识别哪些元素是被复用的。

为了让Vue能正确地比较`key` ，key属性需要是numeric或者string类型。用name字段不是个好主意，因为这个字段会被用户输入控制，无法保证唯一性。

我们使用lodash.uniqueId来实现该功能：

1. 导入 `lodash.uniqueid` 到 `App` 组件。

   ```javascript
   import uniqueId from 'lodash.uniqueid';
   ```

2. 添加 id 字段到 ToDoItems 数组的每一个元素中, 并且将他们赋值为 `uniqueId('todo-')。`

   ```javascript
   import ToDoItem from './components/ToDoItem.vue';
   import uniqueId from 'lodash.uniqueid'
   
   export default {
     name: 'app',
     components: {
       ToDoItem
     },
     data() {
       return {
         ToDoItems: [
           { id: uniqueId('todo-'), label: 'Learn Vue', done: false },
           { id: uniqueId('todo-'), label: 'Create a Vue project with the CLI', done: true },
           { id: uniqueId('todo-'), label: 'Have fun', done: true },
           { id: uniqueId('todo-'), label: 'Create a to-do list', done: false }
         ]
       };
     }
   };
   ```

3. 添加 `v-for` 指令和 `key` 属性到 `<li>` 元素：

   ```html
   <ul>
     <li v-for="item in ToDoItems" :key="item.id">
       <to-do-item label="My ToDo Item" :done="true"></to-do-item>
     </li>
   </ul>
   ```

   这样改后，`<li>`标签中的js脚本就可以访问`item`了，这意味着我们可以使用`v-bind`来传递`item`对象的字段给`ToDoItem`组件了。这非常有用，我们想让列表中的待办事项的`label`值展示到它的label中，而不是显示一个静态的"My Todo Item"。此外，我们想让它们的checked状态反应它们的`done`字段，而不是默认的`done="false"`。

4. 代码修改为如下：

   ```html
   <ul>
     <li v-for="item in ToDoItems" :key="item.id">
        <to-do-item :label="item.label" :done="item.done"></to-do-item>
     </li>
   </ul>
   ```

现在当你去看运行着的app时，你会发现待办事项显示了它们自己正确的名字；如果你查看源码的话，你会发现输入都有了唯一的id。

## 九、做一点重构

我们可以做一点代码重构。 因为我们已经要为每一个待办事项创建一个唯一id，所以不妨把id作为ToDoItem的一个prop，而不是在每个checkbox里生成它。

添加一个新的prop `id` 到 `ToDoItem` 组件。

1. 标记它为required，类型是 `String` 。
2. 为防止命名冲突，删除掉`data`属性中的`id`字段。
3. 删除掉 `import uniqueId from 'lodash.uniqueid';` 这行。

ToDoItem.vue中的script如下：

```javascript
export default {
    props: {
        label: {required: true, type: String},
        done: {default: false, type: Boolean},
        id: {required: true, type: String}
    },
    data() {
        return {
           isDone : this.done,
        }
    },
}
```

现在，在 `App.vue` 组件中将 `item.id` 作为一个prop传递给 `ToDoItem` 组件。你的 `App.vue` template如下所示：

```html
<template>
  <div id="app">
    <h1>My To-Do List</h1>
    <ul>
      <li v-for="item in ToDoItems" :key="item.id">
        <to-do-item :label="item.label" :done="item.done" :id="item.id"></to-do-item>
      </li>
    </ul>
  </div>
</template>
```

你渲染后的站点看起来是没有变化的，但是这次重构使得`item.id`像其他参数一样，作为prop从`App.vue`传递给`ToDoItem`。

现在代码变得更有逻辑性和一致。

接下来我们需要让用户可以输入它们自己的待办事项，想做到这一点，我们需要一个文本输入`<input>`，当用户输入数据时触发一个事件，在事件响应函数中需要将数据添加到待办事项列表并且重新渲染列表，我们还需要一个模型操控数据。我们将在下一篇文章中获取这些知识。

## 十、创建一个新的ToDoForm

我们的app可以展示待办事项列表，但是我们不能更新该列表，除非手动更改代码。让我们新建一个组件来允许我们添加新的待办项。

在components目录下, 新建文件 `ToDoForm.vue`：

1. 创建文件。

2. 新建一个 HTML 表单来允许我们输入新的待办项并把它提交到app。我们需要一个 `<form>` ，它里面包含 `<label>`、一个 `<input>`、 一个 `<button>`。更新后的模版如下：

   ```html
   <template>
     <form>
       <label for="new-todo-input">What needs to be done?</label>
       <input type="text" id="new-todo-input" name="new-todo" autocomplete="off"/>
       <button type="submit">Add</button>
     </form>
   </template>
   ```

   现在我们有一个form组件可以用来输入新的待办项的标题，它最终会渲染成`ToDoItem`的label。

3. 我们把这个组件添加到app中，返回 `App.vue` 然后在 `<script>` 添加下面的语句：

   ```javascript
   import ToDoForm from './components/ToDoForm';
   ```

4. 在你的App组件中注册它

   ```javascript
   components: {
     ToDoItem,
     ToDoForm
   }
   ```

5. 最后将 `ToDoForm` 组件添加到App中的`<template>` 中，像下面这样：

   ```html
   <template>
     <div id="app">
       <h1>My To-Do List</h1>
       <to-do-form></to-do-form>
       <ul>
         <li v-for="item in ToDoItems" :key="item.id">
           <to-do-item :label="item.label" :done="item.done" :id="item.id"></to-do-item>
         </li>
       </ul>
     </div>
   </template>
   ```

运行一下你可以看到添加了新的布局。

如果填上了todo的名字并点击了Add按钮，在一个网页正常操作应该是把这个回传给服务器。这里不做这部分，我们只做添加后显示新的ToDoItem，为了实现这个我们需要添加方法。

## 十一、创建方法并将其绑定到v-on

this is done inside a `methods` property to our component, which goes in the same place as `data()`, `props`, etc.

The `methods` property holds any methods we might need to call in our component. When referenced, methods are fully run, so it's not a good idea to use them to display information inside the template. 

For displaying data that comes from calculations, you should use a `computed` property, which we'll cover later.

1. 代码如下：

   ```javascript
   export default {
      methods: {
          onSubmit() {
             console.log('form submitted')
          }
      }
   }
   ```

2. 接下来是就是把方法绑定给form标签。

   Much like how Vue uses the `v-bind` syntax for binding attributes, Vue has a special directive for event handling: `v-on`. The `v-on` directive works via the `v-on:event="method"` syntax. And much like `v-bind`, there’s also a shorthand syntax: `@event="method"`.

   ```html
   <form @submit="onSubmit">
       
   <form v-on:submit="onSubmit">
   ```

3. 当运行这个方法时，默认是会把数据提交给服务器，然后让页面刷新（这是Vue已经封装好了的功能）。因为这个例子是纯本地的，没有服务器让我们提交，所以在页面刷新时会丢失掉所有数据。为了避免向服务器提交数据，我们需要停止event的默认事件（Event.preventDefault()）。Vue有一种称作event modifiers的语法可以来处理这种情况。

4. 修正符用一个点加在event的末尾：@event.modifier

   | 修正符   | 说明                                                         |
   | -------- | ------------------------------------------------------------ |
   | .stop    | Stops the event from propagating. Equivalent to `Event.stopPropagation()` in regular JavaScript events. |
   | .prevent | Prevents the event's default behavior. Equivalent to `Event.preventDefault()`. |
   | .self    | Triggers the handler only if the event was dispatched from this exact element. |
   | {.key}   | Triggers the event handler only via the specified key. [MDN has a list of valid key values](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values); multi-word keys just need to be converted to kebab case (e.g. `page-down`). |
   | .native  | Listens for a native event on the root (outer-most wrapping) element on your component. |
   | .once    | Listens for the event until it's been triggered once, and then no more. |
   | .left    | Only triggers the handler via the left mouse button event.   |
   | .right   | Only triggers the handler via the right mouse button event.  |
   | .middle  | Only triggers the handler via the middle mouse button event. |
   | .passive | Equivalent to using the `{ passive: true }` parameter when creating an event listener in vanilla JavaScript using `addEventListener()`. |

5. In this case, we need to use the `.prevent` handler to stop the browser’s default submit action. Add `.prevent` to the `@submit` handler in your template like so:

   ```html
   <form @submit.prevent="onSubmit">
   ```

   If you try submitting the form now, you'll notice that the page doesn't reload. If you open the console, you can see the results of the `console.log()` we added inside our `onSubmit()` method.

## 十二、通过v-model绑定数据到input标签

下一步我们需要一种方式来获取到input里的值，以便让给我们添加新的todo

第一步需要一个 data 财富在我们的form里来跟踪我们的todo的值

1. 添加data()在ToDoForm组件里，使其返回一个label

   ```javascript
   export default {
     methods: {
       onSubmit() {
         console.log("form submitted");
       }
     },
     data() {
       return {
         label: ""
       };
     }
   };
   ```

2. Attach the value of the `new-todo-input` `<input>` field to the `label` field.  Vue有专门处理这种事情的方式：v-model。

   > `v-model` binds to the data property you set on it and keeps it in sync with the `<input>`. 
   >
   > `v-model` works across all the various input types, including check boxes, radios, and select inputs. 
   >
   > To use `v-model`, you add an attribute with the structure `v-model="variable"` to the `<input>`.

   在我们代码里添加上：

   ```html
   <input type="text" id="new-todo-input" name="new-todo" autocomplete="off" v-model="ToDoName"/>
   ```

   > 注意：您还可以通过事件和v-bind属性的组合将数据与<input>值同步。事实上，这就是v-model在幕后所做的。但是，确切的事件和属性组合会因输入类型的不同而有所不同，需要的代码比仅使用v-model快捷方式要多。

3. 让我们测试一下v-model。In components, data attributes are accessed using the `this` keyword. So we access our `label` field using `this.label`.

   ```javascript
   methods: {
     onSubmit() {
       console.log('Label value: ', this.label);
     }
   },
   ```

## 十三、通过修饰符改变v-model的行为

跟 event modifiers 类似，我们也可以通过添加修饰符来改变v-model的行为。

在这个情况下，考虑两个细节：

* The first, `.trim`, will remove whitespace from before or after the input. We can add the modifier to our `v-model` statement like so: `v-model.trim="label"`.

* The second modifier we should consider is called `.lazy`.

  This modifier changes when `v-model` syncs the value for text inputs. 

  As mentioned earlier, `v-model` syncing works by updating the variable using events. For text inputs, this sync happens using the [`input` event](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event). Often, this means that Vue is syncing the data after every keystroke. 

  The `.lazy` modifier causes `v-model` to use the [`change` event](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/change_event) instead. This means that Vue will only sync data when the input loses focus or the form is submitted. 

  **For our purposes, this is much more reasonable since we only need the final data.**

为了同时使用两个修饰符，可以写成如下形式：

```html
v-model.lazy.trim="ToDoName".
```

## 十四、通过定制的event传递数据给parents

我们已经非常接近我们想要的功能了。

下一步就是我们需要传递新创建的item给我们的 App.vue 组件。

为了实现这个，我们可以让我们的ToDoForm发起一个定制化event携带有数据，并且让 App.vue 监听这个event。

> This works very similarly to native events on HTML elements: a child component can emit an event which can be listened to via `v-on`.

In the `onSubmit` event of our `ToDoForm`, let's add a `todo-added` event.

Custom events are emitted like this: `this.$emit("event-name")`. 

It's important to know that event handlers are case sensitive and cannot include spaces. 

Vue templates also get converted to lowercase, which means Vue templates cannot listen for events named with capital letters.

1. 在onSubmit()中添加如下代码：

   ```javascript
   this.$emit("todo-added");
   ```

2. 去 App.vue 里添加 methods 财富，添加一个叫 addToDo() 的方法。

   ```javascript
   export default {
     name: 'app',
     components: {
       ToDoItem,
       ToDoForm
     },
    data() {
       return {
         ToDoItems: [
           { id:uniqueId('todo-'), label: 'Learn Vue', done: false },
           { id:uniqueId('todo-'), label: 'Create a Vue project with the CLI', done: true },
           { id:uniqueId('todo-'), label: 'Have fun', done: true },
           { id:uniqueId('todo-'), label: 'Create a to-do list', done: false }
         ]
       };
     },
     methods: {
       addToDo() {
         console.log('To-do added');
       }
     }
   };
   ```

3. 然后给这个todo added事件添加一个event listener，sing the `@` shorthand, the listener would look like this: `@todo-added="addToDo"`:

   ```html
   <to-do-form @todo-added="addToDo"></to-do-form>
   ```

4. 运行可以看到log中打印出了相关的日志。但是我们还需要传递数据，而不是仅仅通知。We can do that by passing additional arguments to the `this.$emit()` function back in the `ToDoForm` component.

5. 这个例子中，我们希望在回调事件的时候同时传递 ToDoName 数据。 data along with it. this is done by including the data you want to pass as another parameter in the `$emit()` method: `this.$emit("todo-added", this.label)`.

   > This is similar to how native JavaScript events include data, except custom Vue events include no event object by default. This means that the emitted event will directly match whatever object you submit. So in our case, our event object will just be a string.

   ```javascript
   onSubmit() {
     this.$emit('todo-added', this.label)
   }
   ```

6. 那如何在App.vue里获取到数据呢？我们需要在addToDo()里添加一个参数来包括这个新增ToDoItem的名称。

   ```javascript
   methods: {
     addToDo(toDoName) {
       console.log("a ToDo is created whose name is " + toDoName)
     }
   }
   ```

## 十五、添加新的ToDo到我们的列表里

现在什么都拿到了，就只需要去添加一个代表它的item到列表里。

This can be done by pushing a new to-do item object to the array containing our new data.

1. 代码如下：

   ```javascript
     methods: {
       addToDo(toDoName) {
         console.log("a ToDo is created whose name is " + toDoName)
         let newToDoItem = {}
         newToDoItem.id = uniqueId('todo-')
         newToDoItem.label = toDoName
         newToDoItem.done = false
         this.ToDoItems.push(newToDoItem)
       }
     }
   ```

   这时候运行就会发现可以添加到列表里了。

2. 做个改善：对输入的内容进行一个判断，如果为空就返回：

   ```javascript
     methods: {
       onSubmit() {
         console.log("form submitted: ", this.ToDoName)
         if (this.ToDoName === "") {
           return
         }
         this.$emit("todo-added", this.ToDoName)
       }
     }
   ```

## 十六、使用v-model来更新输入值

There's one more thing to fix in our `ToDoForm` component — after submitting, the `<input>` still contains the old value. 

But this is easy to fix — because we're using `v-model` to bind the data to the `<input>` in `ToDoForm`, if we set the name parameter to equal an empty string, the input will update as well.

```javascript
  methods: {
    onSubmit() {
      console.log("form submitted: ", this.ToDoName)
      if (this.ToDoName === "") {
        return
      }
      this.$emit("todo-added", this.ToDoName)
      this.ToDoName = ""
    }
  }
```

到此结束。

下一节专注于looks and feels。

## 十七、使用CSS样式化Vue组件

在本文中，我们将探讨使用CSS样式Vue组件的不同方法。

在继续向我们的应用程序添加更多高级功能之前，我们应该添加一些基本的CSS使其看起来更好。

Vue具有三种样式化应用程序的方法：

- 外部CSS文件。
- 单个文件组件（`.vue`文件）中的全局样式。
- 单个文件组件中组件范围的样式。

### 外部CSS文件

您可以包括外部CSS文件，并将其全局应用于您的应用程序。让我们看看这是如何完成的。

在src/assets中创建一个名为reset.css的文件。

> Webpack将处理此文件夹中的文件。这意味着我们可以使用CSS预处理器（如SCSS）或后处理器（如PostCSS）。

本教程不会使用此类工具，但很高兴知道在assets文件夹中包含此类代码后，它将自动进行处理。

```css
/*reset.css*/
/* RESETS */
*,
*::before,
*::after {
    box-sizing: border-box;
}

*:focus {
    outline: 3px dashed #228bec;
}

html {
    font: 62.5% / 1.15 sans-serif;
}

h1,
h2 {
    margin-bottom: 0;
}

ul {
    list-style: none;
    padding: 0;
}

button {
    border: none;
    margin: 0;
    padding: 0;
    width: auto;
    overflow: visible;
    background: transparent;
    color: inherit;
    font: inherit;
    line-height: normal;
    -webkit-font-smoothing: inherit;
    -moz-osx-font-smoothing: inherit;
    -webkit-appearance: none;
}

button::-moz-focus-inner {
    border: 0;
}

button,
input,
optgroup,
select,
textarea {
    font-family: inherit;
    font-size: 100%;
    line-height: 1.15;
    margin: 0;
}

button,
input {
    /* 1 */
    overflow: visible;
}

input[type="text"] {
    border-radius: 0;
}

body {
    width: 100%;
    max-width: 68rem;
    margin: 0 auto;
    font: 1.6rem/1.25 "Helvetica Neue", Helvetica, Arial, sans-serif;
    background-color: #f5f5f5;
    color: #4d4d4d;
    -moz-osx-font-smoothing: grayscale;
    -webkit-font-smoothing: antialiased;
}

@media screen and (min-width: 620px) {
    body {
        font-size: 1.9rem;
        line-height: 1.31579;
    }
}

/*END RESETS*/
```

接下来，在`src/main.js`文件中，如下导入`reset.css`文件：

```javascript
import './assets/reset.css';
```

这会自动将该样式添加到我们的网页中。

| 之前                                                         | 之后                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![](https://mdn.mozillademos.org/files/17247/todo-app-unstyled.png) | ![](https://mdn.mozillademos.org/files/17248/todo-app-reset-styles.png) |

主要包括了以下内容：

* 删除列表项目符号
* 更改背景颜色
* 更改基本按钮和输入样式

### 向单个文件/组件添加全局样式

现在，我们已将CSS设置为在浏览器之间统一，我们需要对样式进行更多自定义。

我们希望将某些样式应用于应用程序中的各个组件。虽然可以直接将这些文件添加到`reset.css`样式表中，但是我们将它们添加到的`<style>`标签中，`App.vue`演示如何使用它们。

文件中已经存在一些样式。让我们删除它们，并用下面的样式替换它们。这些样式可以做一些事情-为按钮和输入添加一些样式，并自定义`#app`元素及其子元素。

更新`App.vue`文件的`<style>`元素，如下所示：

```css
/* Global styles */
.btn {
  padding: 0.8rem 1rem 0.7rem;
  border: 0.2rem solid #4d4d4d;
  cursor: pointer;
  text-transform: capitalize;
}
.btn__danger {
  color: #fff;
  background-color: #ca3c3c;
  border-color: #bd2130;
}
.btn__filter {
  border-color: lightgrey;
}
.btn__danger:focus {
  outline-color: #c82333;
}
.btn__primary {
  color: #fff;
  background-color: #000;
}
.btn-group {
  display: flex;
  justify-content: space-between;
}
.btn-group > * {
  flex: 1 1 auto;
}
.btn-group > * + * {
  margin-left: 0.8rem;
}
.label-wrapper {
  margin: 0;
  flex: 0 0 100%;
  text-align: center;
}
[class*="__lg"] {
  display: inline-block;
  width: 100%;
  font-size: 1.9rem;
}
[class*="__lg"]:not(:last-child) {
  margin-bottom: 1rem;
}
@media screen and (min-width: 620px) {
  [class*="__lg"] {
    font-size: 2.4rem;
  }
}
.visually-hidden {
  position: absolute;
  height: 1px;
  width: 1px;
  overflow: hidden;
  clip: rect(1px 1px 1px 1px);
  clip: rect(1px, 1px, 1px, 1px);
  clip-path: rect(1px, 1px, 1px, 1px);
  white-space: nowrap;
}
[class*="stack"] > * {
  margin-top: 0;
  margin-bottom: 0;
}
.stack-small > * + * {
  margin-top: 1.25rem;
}
.stack-large > * + * {
  margin-top: 2.5rem;
}
@media screen and (min-width: 550px) {
  .stack-small > * + * {
    margin-top: 1.4rem;
  }
  .stack-large > * + * {
    margin-top: 2.8rem;
  }
}
/* End global styles */
#app {
  background: #fff;
  margin: 2rem 0 4rem 0;
  padding: 1rem;
  padding-top: 0;
  position: relative;
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 2.5rem 5rem 0 rgba(0, 0, 0, 0.1);
}
@media screen and (min-width: 550px) {
  #app {
    padding: 4rem;
  }
}
#app > * {
  max-width: 50rem;
  margin-left: auto;
  margin-right: auto;
}
#app > form {
  max-width: 100%;
}
#app h1 {
  display: block;
  min-width: 100%;
  width: 100%;
  text-align: center;
  margin: 0;
  margin-bottom: 1rem;
}
```

你会发现todo list现在在一张纸上，并且格式也更好了。

### 单个文件组件中组件范围的样式

We should apply the button CSS classes to the `<button>` in our `ToDoForm` component. 

Since Vue templates are valid HTML, this is done in the same way to how you might do it in plain HTML — by adding a `class=""` attribute to the element.

Add `class="btn btn__primary btn__lg"` to your form’s `<button>` element:

```html
<button type="submit" class="btn btn__primary btn__lg">
  Add
</button>
```

While we're here, there's one more semantic and styling change we can make. 

Since our form denotes a specific section of our page, it could benefit from an `<h2>` element. 

The label, however, already denotes the purpose of the form. To avoid repeating ourselves, let's wrap our label in an `<h2>`. There are a few other global CSS styles which we can add as well. We'll also add the `input__lg` class to our `<input>` element.

代码最终如下：

```html
<template>
  <form v-on:submit.prevent="onSubmit">
    <label for="new-todo-input">What needs to be done? </label>
    <input type="text" id="new-todo-input" name="new-todo" autocomplete="off" v-model.lazy.trim="ToDoName" class="input__lg"/>
    <button type="submit" class="btn btn__primary btn__lg">Add</button>
  </form>
</template>
```

Let's also add the `stack-large` class to the `<ul>` tag in our `App.vue` file. This will help improve the spacing of our to-do items a bit.

```html
<ul aria-labelledby="list-summary" class="stack-large">
```

### 添加scoped styles

最后我们想添加样式的是我们的 ToDoItem 组件。

To keep the style definitions close to the component we can add a `<style>` element inside it.

However, if these styles alter things outside of this component, it could be challenging to track down the styles responsible, and fix the problem.

This is where the `scoped` attribute can be useful — this attaches a unique HTML `data` attribute selector to all of your styles, preventing them from colliding globally.

在 ToDoItem.vue 里给< style>加上scoped修饰符：

```css
<style scoped>
</style>
```

Next, copy the following CSS into the newly created `<style>` element：

```css
<stype scoped>
.custom-checkbox > .checkbox-label {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  font-weight: 400;
  font-size: 16px;
  font-size: 1rem;
  line-height: 1.25;
  color: #0b0c0c;
  display: block;
  margin-bottom: 5px;
}
.custom-checkbox > .checkbox {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  font-weight: 400;
  font-size: 16px;
  font-size: 1rem;
  line-height: 1.25;
  box-sizing: border-box;
  width: 100%;
  height: 40px;
  height: 2.5rem;
  margin-top: 0;
  padding: 5px;
  border: 2px solid #0b0c0c;
  border-radius: 0;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}
.custom-checkbox > input:focus {
  outline: 3px dashed #fd0;
  outline-offset: 0;
  box-shadow: inset 0 0 0 2px;
}
.custom-checkbox {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  font-weight: 400;
  font-size: 1.6rem;
  line-height: 1.25;
  display: block;
  position: relative;
  min-height: 40px;
  margin-bottom: 10px;
  padding-left: 40px;
  clear: left;
}
.custom-checkbox > input[type="checkbox"] {
  -webkit-font-smoothing: antialiased;
  cursor: pointer;
  position: absolute;
  z-index: 1;
  top: -2px;
  left: -2px;
  width: 44px;
  height: 44px;
  margin: 0;
  opacity: 0;
}
.custom-checkbox > .checkbox-label {
  font-size: inherit;
  font-family: inherit;
  line-height: inherit;
  display: inline-block;
  margin-bottom: 0;
  padding: 8px 15px 5px;
  cursor: pointer;
  touch-action: manipulation;
}
.custom-checkbox > label::before {
  content: "";
  box-sizing: border-box;
  position: absolute;
  top: 0;
  left: 0;
  width: 40px;
  height: 40px;
  border: 2px solid currentColor;
  background: transparent;
}
.custom-checkbox > input[type="checkbox"]:focus + label::before {
  border-width: 4px;
  outline: 3px dashed #228bec;
}
.custom-checkbox > label::after {
  box-sizing: content-box;
  content: "";
  position: absolute;
  top: 11px;
  left: 9px;
  width: 18px;
  height: 7px;
  transform: rotate(-45deg);
  border: solid;
  border-width: 0 0 5px 5px;
  border-top-color: transparent;
  opacity: 0;
  background: transparent;
}
.custom-checkbox > input[type="checkbox"]:checked + label::after {
  opacity: 1;
}
@media only screen and (min-width: 40rem) {
  label,
  input,
  .custom-checkbox {
    font-size: 19px;
    font-size: 1.9rem;
    line-height: 1.31579;
  }
}
</style>
```

Now we need to add some CSS classes to our template to connect the styles.

In the next article we'll return to adding some more functionlity to our app, namely using a computed property to add a count of completed todo items to our app.

## 十八、使用Vue的 computed 财富

我们将添加一个计数器来显示已完成的todo数量，使用computed财富。

和methods很像，These work similarly to methods, but only re-run when one of their dependencies changes.

这个功能是很有用的。

> This can be useful for users, while also serving to label the list for assistive technology. If we have 2 of 5 items completed in our to-do list, our summary could read "2 items completed out of 5".

很快我们就可以得到如下代码：

```html
<h2>{{ToDoItems.filter(item => item.done).length}} out of {{ToDoItems.length}} items completed</h2>
```

每次渲染都会重新计算。对于一个这样的小页面来说，可能问题不大。但是对于一些大网页而言，当表达式很复杂的时候，可能会引起很严重的性能问题。

一个更好的解决方法就是使用 computed 财富。

> Computed Properties work similarly to methods, but only re-run when one of their dependencies changes. In our case, this would only re-run when the ToDoItems array changes.

To create a computed property, we need to add a `computed` property to our component object, much like the `methods` property we've used previously.

为了创建一个 computed 财富，我们需要像 methods 那样添加：

在 App.vue 里面添加如下代码：

```javascript
computed: {
  listSummary() {
    const numberFinishedItems = this.ToDoItems.filter(item =>item.done).length
    return `${numberFinishedItems} out of ${this.ToDoItems.length} items completed`
  }
}
```

The listSummary method will get the number of finished `ToDoItems`, and return a string reporting this.

我们直接把 {{listSummary}} 直接加到 template 标签中，代码如下：

```html
<h2 id="list-summary">{{listSummary}}</h2>
<ul aria-labelledby="list-summary" class="stack-large">
  <li v-for="item in ToDoItems" :key="item.id">
    <to-do-item :label="item.label" :done="item.done" :id="item.id"></to-do-item>
  </li>
</ul>
```

现在你可以发现当你添加新的ToDoItem后总的item数发生了变化，但是当你打勾或取消打勾时，发现并没有更新数字，这是bug。下一节将解决这个问题。

## 十九、追踪done个数items的变化

我们可以使用 events 来捕捉checkbox的更新以此来管理我们的列表。

因为我们没有依靠按钮来触发这个变化，我们可以 attach 一个 `@change` event处理者给每个checkbox，而不使用 v-model。

更新 ToDoItem.vue 的 input标签：

```html
<input type="checkbox" class="checkbox" :id="id" :checked="isDone"
       @change="$emit('checkbox-changed')" />
```

因为我们只需要 emit 勾选框被勾选，我们可以 include the `$emit` inline

在App.vue里，添加一个新方法叫 updateDoneStatus()。

This method should take one parameter: the todo item *id*. We want to find the item with the matching `id` and update its `done` status to be the opposite of its current status:

代码如下：

```javascript
updateDoneStatus(toDoId) {
  const toDoToUpdate = this.ToDoItems.find(item => item.id === toDoId)
  toDoToUpdate.done = !toDoToUpdate.done
}
```

我们想要无论何时 ToDoItem 发射一个 checkbox-changed event 时都要运行这个方法，并且将 id 作为参数传递过去。

在App.vue中更新 < todo-do-item> 标签，代码如下：

```html
<to-do-item :label="item.label" :done="item.done" :id="item.id"
            @checkbox-changed="updateDoneStatus(item.id)">
</to-do-item>
```

到这里你再运行就会发现实现了相关的数字更改了。

> **因为Vue你只需要关注逻辑，数据层，不用去关心UI层了，界面UI会绑定并帮你自动更新，所以我们只需要更新todo的值就可以自动更新页面**

这一节使用了 computed 财富来加了一点小特色。下一篇文章为条件渲染，看看我们如何来编辑已存在的todo items。

## 二十、Vue的条件渲染：编辑已存在的todos

To do this, we will take advantage of Vue's conditional rendering capabilities — namely `v-if` and `v-else` — to allow us to toggle between the existing todo item view, and an edit view where you can update todo item labels. 

We'll also look at adding functionality to delete todo items.

### 创建一个可编辑组件

```vue
<template>
  <form class="stack-small" @submit.prevent="onSubmit">
    <div>
      <label class="edit-label">Edit Name for &quot;{{label}}&quot;</label>
      <input :id="id" type="text" autocomplete="off" v-model.lazy.trim="newLabel" />
    </div>
    <div class="btn-group">
      <button type="button" class="btn" @click="onCancel">
        Cancel
        <span class="visually-hidden">editing {{label}}</span>
      </button>
      <button type="submit" class="btn btn__primary">
        Save
        <span class="visually-hidden">edit for {{label}}</span>
      </button>
    </div>
  </form>
</template>
<script>
export default {
  props: {
    label: {
      type: String,
      required: true
    },
    id: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      newLabel: this.label
    };
  },
  methods: {
    onSubmit() {
      if (this.newLabel && this.newLabel !== this.label) {
        this.$emit("item-edited", this.newLabel);
      }
    },
    onCancel() {
      this.$emit("edit-cancelled");
    }
  }
};
</script>
<style scoped>
.edit-label {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #0b0c0c;
  display: block;
  margin-bottom: 5px;
}
input {
  display: inline-block;
  margin-top: 0.4rem;
  width: 100%;
  min-height: 4.4rem;
  padding: 0.4rem 0.8rem;
  border: 2px solid #565656;
}
form {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
form > * {
  flex: 0 0 100%;
}
</style>
```

你应该对上述代码很熟悉，确保你理解每一行代码！对于加强之前的学习很有帮助。

This code sets up the core of the edit functionality. We create a form with an `<input>` field for editing the name of our to-do.

There is a "Save" button and a "Cancel" button:

- When the "Save" button is clicked, the component emits the new label via an `item-edited` event.
- When the "Cancel" button is clicked, the component signals this by emitting an `edit-cancelled` event.

### 修正我们的 ToDoItem 组件

在将 `ToDoEdit` 添加到我们的 App.vue 前，我们需要在我们的 ToDoItem 上做一些修正。

* 我们需要添加一个变量来表示其是否正在被编辑；
* 并且添加一个按钮来处理这个变量；
* 添加一个删除按钮

ToDoItem 如下：

```html
<template>
  <div class="stack-small">
    <div class="custom-checkbox">
      <input type="checkbox" class="checkbox" :id="id" :checked="isDone"
             @change="$emit('checkbox-changed')" />
      <label :for="id" class="checkbox-label">{{label}}</label>
    </div>
    <div class="btn-group">
      <button type="button" class="btn"  @click="toggleToItemEditForm">
        Edit <span class="visually-hidden">{{label}}</span>
      </button>
      <button type="button" class="btn btn__danger" @click="deleteToDo">
        Delete <span class="visually-hidden">{{label}}</span>
      </button>
    </div>
  </div>
</template>
```

你要注意 div 在template下同级只能有一个，但是内部可以嵌套很多个，没有限制。We’ve added a wrapper <div> around the whole template for layout purposes.

点击 Edit 按钮时会展示 ToDoEdit 组件所以我们可以用它来编辑我们的 Item，通过一个方法。这个方法会把 isEditing 变量设为true。在这之前，我们要先定义这个变量在 data() 财富里。

最终代码如下：

```javascript
export default {
  name: "ToDoItem",
  props: {
    label: {required: true, type: String},
    done: {default: true, type: Boolean},
    id: {required: true, type: String}
  },
  data() {
    return {
      isDone: this.done,
      isEditing: false
    }
  },
  methods: {
    toggleToItemEditForm() {
      this.isEditing = true
    },
    deleteToDo() {
      this.$emit('item-deleted')
    }
  }
}
```

### 通过 v:if 和 v:else 有条件地展示组件

If `isEditing` is true, we want to use that flag to display our `ToDoItemEditForm` instead of the checkbox. To do that, we'll use another Vue directive: `v-if`.

The `v-if` directive will only render a block if the value passed to it is truthy. This is similar to how an `if` statement works in JavaScript

`v-if` also has corresponding `v-else-if` and `v-else` directives to provide the equivalent of JavaScript `else if` and `else` logic inside Vue templates.

> It's important to note that `v-else` and `v-else-if` blocks need to be the first sibling of a `v-if`/`v-else-if` block, otherwise Vue will not recognize them.

You can also attach `v-if` to a `<template>` tag if you need to conditionally render an entire template.

Lastly, you can use a `v-if` + `v-else` at the root of your component to display only one block or another, since Vue will only render one of these blocks at a time. 

We'll do this in our app, as it will allow us to replace the code that displays our to-do item with the edit form.

第一步：在 ToDoItem 组件的根 div 上添加 `v-if="!isEditing"`

```html
<div class="stack-small" v-if="!isEditing">
```

第二步：在 div 的关闭tag后面添加如下代码：

```html
<to-do-edit- v-else :id="id" :label="label"></to-do-edit>
```

同样你需要导入：

```javascript
import ToDoEdit from "@/components/ToDoEdit";


components: {
  ToDoItemEditForm
},
```

现在去你的网页点击 Edit 按钮，你可以发现checkbox被替代了为编辑框了。

### 退出编辑模式

我们首先要添加一个 itemEdited() 方法给我们 ToDoItem 组件。

这个方法要做两件事：

* 把新的item label作为一个参数emit给父组件。
* 把 isEditing 变量设置为false。

添加如下代码：

```javascript
itemEdited(newLabel) {
  this.$emit('item-edited', newLabel);
  this.isEditing = false;
}

editCancelled() {
  this.isEditing = false;
}
```

最后就是要把 event 处理者添加到由 ToDoEdit 发射出的事件上。

```html
<to-do-edit v-else :id="id" :label="label"
            @item-edited="itemEdited"
            @edit-cancelled="editCancelled">
</to-do-edit>
```

### 更新并删除todo item

我们现在可以处理edit和checkbox模式的转变，但是还没有更新App.vue中的ToDoItems。

要解决这个问题，我们要监听 item-edited 事件，并据此更新列表。

```javascript
deleteToDo(toDoId) {
  const itemIndex = this.ToDoItems.findIndex(item => item.id === toDoId);
  this.ToDoItems.splice(itemIndex, 1);
},
editToDo(toDoId, newLabel) {
  const toDoToEdit = this.ToDoItems.find(item => item.id === toDoId);
  toDoToEdit.label = newLabel;
}
```

Next, we'll add the event listeners for the `item-deleted` and `item-edited` events:

在 App.vue 里添加listener：

```html
<to-do-item :label="item.label" :done="item.done" :id="item.id"
             @checkbox-changed="updateDoneStatus(item.id)"
             @item-deleted="deleteToDo(item.id)" 
             @item-edited="editToDo(item.id, $event)"></to-do-item>
```

到此可以编辑、取消和删除了！

## 二十一、修复一个有关 isDone 状态的bug

We've actually introduced a bug by adding in the edit functionality. 

Try doing this:

1. 勾选或取消勾选其中一个勾选框
2. 点击Edit按钮
3. 取消编辑

你可以发现在你点击Edit按钮前的状态是错误的，而且显示的数字也是错误的，并且后续的操作显示的数字也是错误的。

原因在于 isDone 这个值只在组件加载时被赋值。（This is because the `isDone` inside `data` is only given the value `this.done` on component load.）

解决这个问题很简单——将 isDone 转变为 computed 财富，computed 财富的另一个优势是动态地存储（another advantage of computed properties is that they preserve [reactivity](https://vuejs.org/v2/guide/reactivity.html), meaning (among other things) that their state is saved when the template changes like ours is now doing.）

按如下来变更：

1. 移除 data() 财富中如下行：

   ```javascript
   isDone: this.done,
   ```

2. Add the following block below the data() { } block:

   ```javascript
   computed: {
     isDone() {
       return this.done;
     }
   },
   ```

   > data 和 computed 里的同名会冲突，只能留有一个。

Now when you save and reload, you'll find that the problem is solved — the checkbox state is now preserved when you switch between todo item templates.

## 二十二、理解看似混乱的events

可以发现目前最困惑的就是各种标准/自定义事件了。

为了更好地理解，可以画个流程图、描述或者图表来表示。

App.vue

* to-do-form监听：

  * 当表单被submit时，由ToDoForm发出的todo-added事件。

    结果：addToDo() 添加新的todo item到 ToDoItems 数组。

* to-do-item监听：

  * 当复选框被勾选或取消勾选时，由 ToDoItem 中的 input 发出的 checkbox-changed 事件

    结果：updateDoneStatus() 更改相关item的done状态

  * 当 Delete 按钮被点击时由 ToDoItem 组件中的 deleteToDo() 方法发出的 item-deleted 事件

    结果：deleteToDo() 方法删除相关的todo item

  * 当 ToDoEdit 中的 onSubmit() 成功监听到发出 item-edited 事件

这些看 [Understanding the tangle of events](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_conditional_rendering#understanding_the_tangle_of_events)

## 二十三、使用 Vue refs 进行焦点管理

几乎快完成了！

最后一个部分是有关焦点管理，或者换一种说法，是有关网页键盘按钮响应。

我们会使用 Vue refs 来处理这个问题（an advanced feature that allows you to have direct access to the underlying DOM nodes below the virtual DOM, or direct access from one component to the internal DOM structure of a child component.）

虽然我们已经完成编辑功能，但我们还没有给无鼠标的用户做适配。特别地，当一个用户激活 Edit 按钮，我们把 Edit 按钮从DOM中移除，不过我们却没有把焦点移到哪里，所以焦点就消失了。This can be disorienting for keyboard and non-visual users.

做以下步骤理解正在发生什么：

1. Reload your page, then press Tab. You should see a focus outline on the input for adding new to-do items.
2. Press Tab again. The focus should move to the "Add" button.
3. Hit it again, and it'll be on the first checkbox. One more time, and focus should be on the first "Edit" button.
4. Activate the "Edit" button by pressing Enter. The checkbox will be replaced with our edit component, but the focus outline will be gone.

这可以说很令人不快，。此外，Tab再次按下时会发生什么，取决于您使用的浏览器。同样，如果保存或取消编辑，则焦点将再次消失，即移回非编辑视图。

为了给用户带来更好的体验，我们将添加代码来控制焦点，以便在显示编辑表单时将其设置为编辑字段。

当用户取消或保存他们的编辑时，我们还希望将焦点重新放在“编辑”按钮上。为了确定重点，我们需要更多地了解Vue在内部的工作方式。

## Virtual DOM

与其他框架一样，Vue使用虚拟DOM（VDOM）来管理元素。

这意味着Vue在内存中保留了我们应用程序中所有节点的表示。

首先在内存节点上执行所有更新，然后对页面上的实际节点进行的所有更改都将批量同步。

由于读取和写入实际DOM节点通常比虚拟节点更昂贵，因此虚拟DOM可以提高性能。

但是，这也意味着`Document.getElementById`在使用框架时，您通常不应该直接通过本机浏览器API（例如）直接编辑HTML元素，因为这会导致VDOM和实际DOM不同步。

Instead, if you need to access the underlying DOM nodes (like when setting focus), you can use **[Vue refs](https://vuejs.org/v2/api/#ref)**.

对于自定义的Vue组件，您还可以使用引用直接访问子组件的内部结构，但是应谨慎操作，因为这会使代码更难以推理和理解。

要在组件中使用ref，请将`ref`属性添加到要访问的元素中，并带有属性值的字符串标识符。重要的是要注意，引用在组件内必须唯一。同时渲染的两个元素都不应具有相同的引用。

让我们添加一个 ref 到 ToDoItem 的编辑按钮上：

```html
<button type="button" class="btn" ref="editButton" @click="toggleToItemEditForm">
  Edit
  <span class="visually-hidden">{{label}}</span>
</button>
```

To access the value associated with our ref, we use the `$refs` property provided on our component instance.（注意 $ref 这个用法）

To see the value of the ref when we click our "Edit" button, add a `console.log()` to our `toggleToItemEditForm()` method, like so:

```javascript
toggleToItemEditForm() {
  console.log(this.$refs.editButton);
  this.isEditing = true;
}
```

If you activate the "Edit" Button at this point, you should see an HTML `<button>` element referenced in your console.

## Vue的 $nextTick() 方法

https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/Vue_refs_focus_management