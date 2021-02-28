# JavaScript异步

[TOC]

## axios+promise

> axios它是**基于promise的http库**，可运行在浏览器端和node.js中。
>
> 作者尤雨溪果断放弃了对其官方库vue-resource的维护，直接推荐axios库。
>
> 注：promise是JS中异步操作的方式。

axios特点：

* 从浏览器中创建 XMLHttpRequests
* 从 node.js 创建 http 请求
* 支持 Promise API
* 拦截请求和响应 （就是有interceptor）
* 转换请求数据和响应数据
* 取消请求
* 自动转换 JSON 数据
* 客户端支持防御 XSRF

首先安装：

```shell
npm i axios --save
npm i qs --save
```

用vue-cli创建的项目在src->util->api.js写好公共请求模块js（你开心定义在哪都行）

在js里导入

```javascript
import axios from 'axios'

//有时候向后端发送数据，后端无法接收,考虑使用qs模块
import qs from 'qs'
```

```javascript
//判定开发模式
if (process.env.NODE_ENV == 'development') {    
    axios.defaults.baseURL = '/api';
}else if (process.env.NODE_ENV == 'debug') {    
    axios.defaults.baseURL = 'http://v.juhe.cn';
}else if (process.env.NODE_ENV == 'production') {    
    axios.defaults.baseURL = 'http://v.juhe.cn';
}

//全局设置头部信息
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=UTF-8';

//全局设置超时时间
axios.defaults.timeout = 10000;

//请求路由拦截
//在请求发出去之前，可以做一些判断，看是否是合法用户
axios.interceptors.request.use(function (config) {
    // 一般在这个位置判断token是否存在
    return config;
   }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
});

//响应拦截
axios.interceptors.response.use(function (response){
     // 处理响应数据
    if (response.status === 200) {            
        return Promise.resolve(response);        
    } else {            
        return Promise.reject(response);        
    }
    }, function (error){
    // 处理响应失败
    return Promise.reject(error);
});
```

### 封装请求方法

在ES6前， 前端就使用RequireJS或者seaJS实现模块化。

requireJS是基于AMD规范的模块化库， 而像seaJS是基于CMD规范的模块化库， 两者都是为了为了推广前端模块化的工具，现在ES6自带了模块化，不过现代浏览器对模块(module)支持程度不同，需要使用babelJS，或者Traceur把ES6代码转化为兼容ES5版本的js代码；

```javascript
//get
export function get(url, params){    
    return new Promise((resolve, reject) =>{        
        axios.get(url, {            
            params: params        
        }).then(res => {
            resolve(res.data);
        }).catch(err =>{
            reject(err.data)        
        })    
    });
}

//post
export function post(url, params) {
    return new Promise((resolve, reject) => {
         axios.post(url, qs.stringify(params))
        .then(res => {
            resolve(res.data);
        })
        .catch(err =>{
            reject(err.data)
        })
    });
}
```

### 实际使用

在main.js中引入js

```javascript
import {get,post} from './utils/api'
//将方法挂载到Vue原型上（注意这种写法）
Vue.prototype.get = get;
Vue.prototype.post = post;
```

配置请求环境：这里通过devServer请求代理，当在请求过程中有/api字符串会自动转换为**目标服务器地址(target)**（原来都是这样写的，get到了）

```js
devServer: {
   historyApiFallback: true,
   hot: true,
   inline: true,
   stats: { colors: true },
   proxy: {
     //匹配代理的url
     '/api': {
          // 目标服务器地址
             target: 'http://v.juhe.cn',
             //路径重写
             pathRewrite: {'^/api' : ''},
             changeOrigin: true,
             secure: false,
           }
      },
    disableHostCheck:true
   }
}
```

这是以请求聚合数据的接口为例子：

```javascript
this.get('/toutiao/index?type=top&key=秘钥',{})
    .then((res)=>{
        if(res.error_code===0){
            resolve(res);
        }else{
            //这里抛出的异常被下面的catch所捕获
            reject(error);
        }
    })
    .catch((err)=>{
        console.log(err)
    })
}
```

![](https://segmentfault.com/img/bVbh9lA)

比如用户想请求url1接口完后再调url2接口：

```javascript
var promise = new Promise((resolve,reject)=>{
    let url1 = '/toutiao/index?type=top&key=秘钥'
    this.get(url,{}).then((res)=>{
        resolve(res);
    }).catch((err)=>{
        console.log(err)
    })
});

promise.then((res)=>{
    let url2 = '/toutiao/index?type=top&key=秘钥'
    this.get(ur2,{}).then((res)=>{
        //只有当url1请求到数据后才会调用url2，否则等待
        resolve(res);
    }).catch((err)=>{
        console.log(err)
    })
})
```

Promise有三种状态

* pending: 等待中，或者进行中，表示还没有得到结果
* resolved: 已经完成，表示得到了我们想要的结果，可以继续往下执行
* rejected: 也表示得到结果，但是由于结果并非我们所愿，因此拒绝执(用catch捕获异常)

这三种状态不受外界影响，而且状态只能从pending改变为resolved或者rejected，并且不可逆(顾名思义承诺的后的东西怎么又能返回呢)。在Promise对象的构造函数中，将一个函数作为第一个参数。而这个函数，就是用来处理Promise的状态变化。

这里要注意，不管是then或者catch返回的都是一个新的Promise实例！而每个Primise实例都有最原始的Pending（进行中）到Resolve（已完成），或者Pending（进行中）到Reject（已失败）的过程。

Promise resolve()用法：

```javascript
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```

有时需要将现有对象转为Promise对象，Promise.resolve方法就起到这个作用。

Promise reject()用法：

```javascript
Promise.reject('foo')
// 等价于
new Promise(reject => reject('foo'))
```

Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为rejected。但是Promise.reject()方法的参数，会原封不动地作为reject的理由，变成后续方法的参数。这一点与Promise.resolve方法不一致。

## 深入理解Promise

异步与同步相比，最难以掌控的就是异步的任务会什么时候完成和完成之后的回调问题。

如下代码：

```javascript
listen("click", function handler(evt){
    setTimeout(function request(){
        ajax("http://some.url.1", function response(text){
            if (text === "hello"){
                handler();
            } else {
                request();
            }
        })
    })
})
doSomething();
```

执行顺序为：

1. 执行listern()
2. doSomething()
3. 500ms（或者更远）后执行ajax()
4. 根据条件判断执行 handler() 或 request()

难受吗？

在你不知道的javascript一书中，对于回调的信任问题做了阐述当你使用第三方的库的方法处理回调时很有可能遇到以下信任内容：

* 调用回调过早
* 调用回调过晚
* 调用回调太多或太少次
* 没能向你的回调传递必要的参数
* 吞掉了可能发生的错误

怎么解决？你需要一个承诺

当你把一件事情交给别人去做（可能马上就能完成的也可能是需要一段时间的）这个人在任务完成或者失败后都会给你一个回应，这样的人你是不是特别放心的把事情交给他，他没回应你那么他是正在办事、回应你了就是成功了或者失败了。

在JavaScript中这样的人就是Promise。

Promise的实例有三个状态：

* Pending（进行中）
* Resolved（已完成）
* Rejected（已拒绝）。

当你把一件事情交给promise时，它的状态就是Pending，任务完成了状态就变成了Resolved，没有完成失败了就变成了Rejected。

写一个简单的Promise：

```javascript
// 接收一个callback，参数是成功函数resolve和失败函数reject
let promise = new Promise((resolve, reject) => { 
    setTimeout(() => {
        let num = parseInt(Math.random() * 100);
        // 如果数字大于50就调用成功的函数，并将状态变为resolve
        if (num > 50){
            reslove(num);
        } else {
            reject(num);
        }
    }, 10000);
})
```

当Promise执行的内容符合你预期的成功条件的话，就调用resolve函数，失败就调用reject函数。

这两个函数的参数会被Promise捕捉到，可以在之后的回调中使用。

创建一个承诺我们已经做完了，那么如何使用承诺后的结果呢？

```javascript
promise.then(res => {
    // 如果执行了resolve函数就会到这一步
    console.log(res);
}, err => {
    // 如果执行了reject函数就会到这一步
    console.log(err);
});
```

then方法接收两个函数，第一个是承诺成功（状态为resolved）的回调函数，一个承诺失败（状态为rejected）的回调函数。

then方法的返回值不是一个Promise对象的话就会被包装成一个Promise对象，所以then方法支持链式调用。

```javascript
promise.then(res => {
    return 42;
}).then(res => {
    console.log(res);
})
```

then方法的链式调用可以帮我们串行地解决一些逻辑。

当我们平时书写有顺序的异步时间，比如

```javascript
ajax("first");
ajax("second");
ajax("third");

// 需要按顺序来执行该怎么写
ajax("first").success(function(res){
    ajax("second").success(function(res){
        ajax("third").success(function(res){
            //按照顺序串行执行完任务了，可以在这里写之后的逻辑
        })
    })
})
```

多么美丽的三角形~（其实也挺好理解的呀）

如果使用then的话如下：

```javascript
let promise = new Promise((resolve, reject) => {
    ajax("first").success(function(res){
        resolve(res);
    })
})

promise.then(res => {
    return new Promise((resolve, reject) => {
        ajax("second").success(function(res){
            resolve(res);
        })
    })
}).then(res => {
    return new Promise((resolve, reject) => {
        ajax("third").success(function(res){
            resolve(res);
        })
    })
}).then(res => {
    // 按照顺序串行执行完任务了，可以在这里写之后的逻辑
})
```

而且每次执行resolve的时候，都可以把每次ajax的回调数据进行传递到最后。清晰简单明了。（真的吗？？我咋觉得没有第一种来得清晰...）

说完串行了，那么并行怎么办？当有多个异步事件，之间并无联系而且没有先后顺序，只需要全部完成就可以开始工作了。

串行会把每一个异步事件的等待时间进行一个相加，明显会对完成进行一个阻塞。那么并行的话该怎么确定全部完成呢？

这时候就要使用 Promise.all 和 Promise.race 了。

Promise.all 接收一个数组，数组的每一项都是一个Promise对象。

当数组中所有的promise的状态都达到resolved的时候，Promise.all的状态就会变成resolved，如果有一个状态变成了rejected，那么Promise.all的状态就会变成rejected（任意一个失败就算是失败），这就可以解决我们并行的问题。调用then方法时的结果成功时返回回调函数的参数也是一个数组，按顺序保存着每一个Promise对象resolve执行时的值。

```javascript
let promise1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(1);
    }, 10000)
})

let promise2 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(2);
    }, 10000)
})

let promise3 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(3);
    }, 10000)
})

Promise.all([promise1, promise2, promise3]).then(res => {
    console.log(res);
}, err => {
    console.log(err);
})
```

在控制台输入后可以看到

```javascript
Promise {<pending>}
```

的状态，等待10秒后就会变成

```javascript
(3) [1, 2, 3]
```

更改resolve函数的参数，你就会发现输出结果是对应传入resolve函数时的参数：

```javascript
let promise1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("Love");
    }, 10000)
})

let promise2 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("I");
    }, 10000)
})

let promise3 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("You");
    }, 10000)
})

Promise.all([promise1, promise2, promise3]).then(res => {
    console.log(res);
}, err => {
    console.log(err);
})
```

```javascript
(3) ["love", "I", "You"]
```

如果什么参数都不传，那么就是输出

```javascript
(3) [undefined, undefined, undefined]
```

Promise.race 竞速模式也是接受一个每一项都是Promise的数组。但是与all不同的是，第一个Promise对象状态变成resolved时自身的状态变成了resolved，第一个Promise变成rejected自身状态就会变成rejected。第一个变成resolved的promsie的值就会被使用。

```javascript
let promise1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("Love");
    }, 10000)
})

let promise2 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("I");
    }, 9000)
})

let promise3 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("You");
    }, 10000)
})

Promise.race([promise1, promise2, promise3]).then(res => {
    console.log(res);
    // 此时这里打印出了I，因为promise2第一个完成了承诺，其他的就忽略了
}, rej => {
    console.log("rejected");
    console.log(rej);
})
```

Promsie.race还有一个很重要的实际用处就是，有时候我们要去做一件事，但是超过三秒钟左右我们就不做了那怎么办？

这个时候就可以使用Promise.race方法。

## async & await

async主要是避免回调地狱的问题。

* async 表示这是一个async函数，await只能用在这个函数里面。它作为一个关键字放到函数前面，用于表示函数是一个异步函数，异步函数也就意味着该函数的执行不会阻塞后面代码的执行。

  举例如下：

  ```javascript
  async function sayHi() {
      return "hello world";
  }
  ```

  语法很简单，就是在函数前面加上async 关键字，来表示它是异步的。

  async 函数也是函数，平时我们怎么使用函数就怎么使用它，直接加括号调用就可以了，为了表示它没有阻塞它后面代码的执行，我们在async 函数调用之后加一句console.log()

  ```javascript
  async function sayHi() {
      return "hello world";
  }
  sayHi();
  console.log("虽然在后面，但是我先执行");
  ```

  打开控制台，可以看到：

  ```javascript
  虽然在后面，但是我先执行
  ```

  what？为什么sayHi()没有输出，不是应该输出'hello world'吗？先不要着急， 看一看timeout()执行返回了什么？ 把上面的 timeout() 语句改为console.log(timeout())

  继续看控制台：

  ```javascript
  Promise {<resolved>: "hello world"}
  虽然在后面，但是我先执行
  ```

  原来async 函数返回的是一个promise 对象，如果要获取到promise 返回值，我们应该用then 方法， 继续修改代码：

  ```javascript
  async function sayHi() {
      return 'hello world'
  }
  sayHi().then(result => {
      console.log(result);
  })
  console.log('虽然在后面，但是我先执行');
  ```

  这时候再看控制台就可以发现输出如下：

  ```javascript
  虽然在后面，但是我先执行
  hello world
  ```

  我们获取到了"hello world', 同时timeout 的执行也没有阻塞后面代码的执行，和我们刚才说的一致。

  这时，你可能注意到控制台中的Promise 有一个resolved，这是async 函数内部的实现原理。

  如果async 函数中有返回一个值，当调用该函数时，内部会调用Promise.solve() 方法把它转化成一个promise 对象作为返回，但如果sayHi 函数内部抛出错误呢？ 那么就会调用Promise.reject() 返回一个promise 对象， 这时修改一下sayHi 函数

* await 表示在这里等待promise返回结果了，再继续执行。

* **await 后面跟着的应该是一个promise对象**（当然，其他返回值也没关系，不过那样就没有意义了…）

举例：

```javascript
var sleep = function (time) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            // 返回 ‘ok'
            resolve('ok');
        }, time);
    })
};

var start = async function () {
    let result = await sleep(3000);
    console.log(result); // 收到 ‘ok'
};
```

模拟捕捉错误：

```javascript
var sleep = function (time) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            // 模拟出错了，返回 ‘error'
            reject('error');
        }, time);
    })
};

var start = async function () {
    try {
        console.log('start');
        await sleep(3000); // 这里得到了一个返回错误
    
        // 所以以下代码不会被执行了
        console.log('end');
    } catch (err) {
        console.log(err); // 这里捕捉到错误 `error`
    }
};
```

在循环中：

```javascript
var start = async function () {
    for (var i = 1; i <= 10; i++) {
        console.log('当前是第${i}次等待..');
        await sleep(1000);
    }
};
```

再循环中使用不需要闭包，每次循环会被阻塞。

现在再来考虑一个场景：首先更改文章详情中的 PV（Page View，页面访问量），然后读取文章详情，然后根据文章详情中文章 Id 查阅该文章评论和该文章作者信息。获取全部数据之后渲染文章详情页。数据库操作都是异步的。

```javascript
var showArticle = async function () {
    // pv 加 1
    await new Promise(function (resolve, reject) {
        PostModel.incPv(postId, function (result) {
            resolve(result);
        });
    });
    // 获取文章信息
    var post = await new Promise(function (resolve, reject) {
        PostModel.getPostById(postId, function (article) {
            resolve(article);
        });
    });
    // 获取文章作者
    await new Promise(function (resolve, reject) {
        userModel.getUserById(post.author,function (author) {
            post.author=author;
            resolve();
        })
    });
    // 获取该文章所有留言
    var comments = await new Promise(function (resolve, reject) {
        CommentModel.getComments(post._id, function (comment) {
            resolve(comment);
        });
    });
    // 获取文章留言作者
    for(var i=0;i<comments.length;i++){
        await new Promise(function (resolve, reject) {
            userModel.getUserById(comments[i].author,function (author) {
                comments[i].author=author;
                resolve();
            })
        });
    }
    
    if (!post) {
        req.session.error = '该文章不存在';
        return res.redirect('/post');
    }
    res.render('post',{
        post: post, 
        comments: comments
    });
};
showArticle();
```

这时，你可能注意到控制台中的 Promise 有一个resolved，这是 async 函数内部的实现原理。如果 async 函数中有返回一个值，当调用该函数时，内部会调用Promise.solve() 方法把它转化成一个 promise 对象作为返回。

但如果 sayHi 函数内部抛出错误呢？ 那么就会调用Promise.reject() 返回一个promise 对象， 这时修改一下 sayHi 函数：

```javascript
async function sayHi(flag) {
    if (flag) {
        return 'hello world'
    } else {
        throw 'my god, failure'
    }
}
console.log(sayHi(true)); //调用Promise.resolve() 返回promise 对象。
console.log(sayHi(false)); //调用Promise.reject() 返回promise 对象。
```

控制台输出如下：

```javascript
Promise {<resolved>: "hello world"}
Promise {<rejected>: "my god, failure"}
```

如果函数内部抛出错误， promise 对象有一个catch 方法进行捕获：

```javascript
sayHi(false).catch(err => {
    console.log(err)
})
```

我们再来考虑await 关键字，await是等待的意思，那么它等待什么呢？它后面跟着什么呢？

其实它后面可以放任何表达式，**不过我们更多的是放一个返回Promise对象的表达式**。注意await 关键字只能放到async函数里面。

现在写一个函数，让它返回Promise对象，该函数的作用是2s之后让数值乘以2

```javascript
// 2s 之后返回双倍的值
function doubleAfter2seconds(num) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(2 * num)
        }, 2000);
    } )
}
```

现在再写一个async函数，从而可以使用await关键字， await后面放置的就是返回promise对象的一个表达式，所以它后面可以写上 doubleAfter2seconds 函数的调用：

```javascript
async function testResult() {
    let result = await doubleAfter2seconds(30);
    console.log(result);
}
```

现在调用testResult 函数：

```javascript
testResult();
```

打开控制台，2s 之后，输出了60。

现在我们看看代码的执行过程：

* 调用 testResult 函数，它里面遇到了await，await 表示等一下，代码就暂停到这里，不再向下执行了，它等什么呢？等后面的promise对象执行完毕，然后拿到 promise resolve 的值并进行返回。
* 返回值拿到之后，它继续向下执行。具体到我们的代码，遇到 await 之后，代码就暂停执行了， 等待 doubleAfter2seconds(30) 执行完毕，doubleAfter2seconds(30) 返回的 promise 开始执行，2秒之后，promise resolve 了， 并返回了值为60， 这时 await 才拿到返回值60， 然后赋值给result，暂停结束，代码才开始继续执行，执行 console.log语句。

就这一个函数，我们可能看不出 async/await 的作用，如果我们要计算3个数的值，然后把得到的值进行输出呢？

```javascript
async function testResult() {
    let first = await doubleAfter2seconds(30);
    let second = await doubleAfter2seconds(50);
    let third = await doubleAfter2seconds(30);
    console.log(first + second + third);
}
```

6秒后，控制台输出220。

**我们可以看到，写异步代码就像写同步代码一样了，再也没有回调地狱了。**

再写一个真实的例子：我原来做过一个小功能，话费充值，当用户输入电话号码后，先查找这个电话号码所在的省和市，然后再根据省和市，找到可能充值的面值，进行展示。

为了模拟一下后端接口，我们新建一个node 项目。

新建一个文件夹 async，然后npm init -y 新建package.json文件，npm install express --save 安装后端依赖，再新建 server.js 文件作为服务端代码， public文件夹作为静态文件的放置位置， 在 public 文件夹里面放index.html 文件， 整个目录如下：

![](https://images2017.cnblogs.com/blog/1013082/201802/1013082-20180206094358654-1149495639.png)

server.js 文件如下，建立最简单的 web 服务器：

```javascript
const express = require('express');
const app = express();
// express.static 提供静态文件，就是如html、css、js文件
app.use(express.static('public'));

app.listen(3000, () => {
    console.log('server start');
})
```

再写 index.html 文件，我在这里用了vue构建页面，用 axios 发送ajax请求，为了简单，用cdn 引入它们。

html部分很简单，一个输入框，让用户输入手机号，一个充值金额的展示区域， js部分，按照vue 的要求搭建了模版：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Async/await</title>
    <!-- CDN 引入vue 和 axios -->
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
</head>
<body>
    <div id="app">

        <!-- 输入框区域 -->
        <div style="height:50px">
            <input type="text" placeholder="请输入电话号码" v-model="phoneNum">
            <button @click="getFaceResult">确定</button>
        </div>

        <!-- 充值面值 显示区域 -->
        <div>
            充值面值：
            <span v-for="item in faceList" :key='item'>
                {{item}}
            </span>
        </div>
    </div>

    <!-- js 代码区域 -->
    <script>
        new Vue({
            el: '#app',
            data: {
                phoneNum: '12345',
                faceList: ["20元", "30元", "50元"]
            },
            methods: {
                getFaceResult() {

                }
            }
        })
    </script>
</body>
</html>
```

为了得到用户输入的手机号，给input 输入框添加v-model指令，绑定phoneNum变量。

展示区域则是绑定到faceList 数组，v-for 指令进行展示。

这时命令行nodemon server启动服务器，如果你没有安装nodemon， 可以npm install -g nodemon安装它。

启动成功后，在浏览器中输入 http://localhost:3000，可以看到页面如下，展示正确。

![](https://images2017.cnblogs.com/blog/1013082/201802/1013082-20180206101959779-1328834798.png)

现在我们来动态获取充值面值。

当点击确定按钮时， 我们首先要根据手机号得到省和市，所以写一个方法来发送请求获取省和市，方法命名为getLocation，接受一个参数phoneNum，后台接口名为phoneLocation，当获取到城市位置以后，我们再发送请求获取充值面值，所以还要再写一个方法getFaceList，它接受两个参数，province和city，后台接口为faceList，在methods下面添加这两个方法getLocation()、getFaceList()

```javascript
methods: {
    //获取到城市信息
    getLocation(phoneNum) {
        return axios.post('phoneLocation', {
            phoneNum
        })
    },
    // 获取面值
    getFaceList(province, city) {
        return axios.post('/faceList', {
            province,
            city
        })
    },
    // 点击确定按钮时，获取面值列表
    getFaceResult () {
               
    }
}
```

现在再把两个后台接口写好，为了演示，写的非常简单，没有进行任何的验证，只是返回前端所需要的数据。

Express 写这种简单的接口还是非常方便的，在 app.use 和 app.listen 之间添加如下代码

```javascript
// 电话号码返回省和市，为了模拟延迟，使用了setTimeout
app.post('/phoneLocation', (req, res) => {
    setTimeout(() => {
        res.json({
            success: true,
            obj: {
                province: '广东',
                city: '深圳'
            }
        })
    }, 1000);
})

// 返回面值列表
app.post('/faceList', (req, res) => {
    setTimeout(() => {
        res.json(
            {
                success: true,
                obj:['20元', '30元', '50元']
            }
            
        )
    }, 1000);
})
```

最后是前端页面中的click 事件的getFaceResult，由于axios返回的是promise对象，我们使用then的链式写法，先调用getLocation方法，在其then方法中获取省和市，然后再在里面调用getFaceList，再在getFaceList的then方法获取面值列表：

```javascript
// 点击确定按钮时，获取面值列表
getFaceResult () {
    this.getLocation(this.phoneNum).then(res => {
        if (res.status === 200 && res.data.success) {
            let province = res.data.obj.province;
            let city = res.data.obj.city;

            this.getFaceList(province, city).then(res => {
                if(res.status === 200 && res.data.success) {
                    this.faceList = res.data.obj
                }
            })
        }
    }).catch(err => {
        console.log(err)
    })
}
```

现在点击确定按钮，可以看到页面中输出了从后台返回的面值列表。

这时你看到了then的链式写法，有一点回调地狱的感觉。现在我们再用 async/ await 来改造一下。

首先把 getFaceResult 转化成一个async 函数，就是在其前面加async， 因为它的调用方法和普通函数的调用方法是一致，所以没有什么问题。然后就把 getLocation 和 getFaceList 放到await 后面，等待执行， getFaceResult 函数修改如下：

```javascript
// 点击确定按钮时，获取面值列表
async getFaceResult () {
    let location = await this.getLocation(this.phoneNum);
    if (location.data.success) {
        let province = location.data.obj.province;
        let city = location.data.obj.city;
        let result = await this.getFaceList(province, city);
        if (result.data.success) {
            this.faceList = result.data.obj;
        }
    }
}
```

现在代码的书写方式，就像写同步代码一样，没有回调的感觉，非常舒服。（可以看到需要then的函数都加上await）

现在就还差一点需要说明，那就是怎么处理异常，如果请求发生异常，怎么处理？ 它用的是 **try/catch** 来捕获异常，把 await 放到 try 中进行执行，如有异常，就使用 catch 进行处理。

```javascript
async getFaceResult () {
    try {
        let location = await this.getLocation(this.phoneNum);
        if (location.data.success) {
            let province = location.data.obj.province;
            let city = location.data.obj.city;
            let result = await this.getFaceList(province, city);
            if (result.data.success) {
                this.faceList = result.data.obj;
            }
        }
    } catch(err) {
        console.log(err);
    }
}
```

现在把服务器停掉，可以看到控制台中输出net Erorr，整个程序正常运行。

## 有了async/await就不学Promise了吗？

nodejs 7.0 正式发布，并且增加了 async/await 功能，其原理类似于 co，使用

```shell
node --harmony-async-await app.js  
```

就可以直接体验最新的 async/await 特性。

下图你可以很直观看到三者的一个演进和变化。

![](https://pic1.zhimg.com/80/v2-be8f674422c9f30ffeaee88477ad2c84_1440w.png)

可以很明显的看到，callback 来控制异步的方式虽然非常简单，但也过于原始。在实际的使用中代码的逻辑顺序和业务的顺序是不相同的，错误控制基本靠手动检查err参数。（特别是有很多的方法要串行/并行的时候）

而到了 Promise 中这种情况好了很多，通过链式调用，Promise 可以直接在 then 中返回一个新的 Promise 来将异步操作串联起来，也有了统一的 catch 来做错误处理。美中不足的是，你仍然需要传递一个回调函数给 then，通过 then 来串联虽然保证了至少代码顺序上和真正的逻辑顺序一致，但和同步代码的差别仍然很大。

async/await 则直接将其变成了同步的写法，心智负担大大降低。

常常有人吐槽前端技术变化太快，说刚学的技术就被新技术取代，甚至有人将其当做不愿意学习的借口，实则不然，就以 Promise 到 async/await 的演变为例，async/await 的出现并不代表我们就完全不需要再去了解 Promise，更不代表 Promise 相关的知识失去了作用，事实上，在使用 async/await 之前，我们**必须**了解 async/await 和 Promise 之间的联系。

async/await 和 Promise 的关系，用一句话总结，就是 **async function 就是返回 Promise 的 function**。

这句话意味着很多，例如

### await 同样可以用于返回 Promise 的其他函数

例如我们有

```javascript
function timeout(time) {  
  return new Promise((reslove, reject) => {
    setTimeout(() => {
      reslove(10)
    }, time)
  })
}
```

我们可以有两种使用方法

```javascript
// Promise
timeout(3000).then((data) => {  
  console.log(data)
})

// 直接使用 await
(async () => {
  console.log(await timeout(3000))
})()
```

需要注意的是 await 只能在 async function 内使用，所以常常有这种写法：

```javascript
(async () => {
  // do your async work
})()
```

### async function 可以被用于 promise

async function 同样可以被当做 promise 使用，这样我们就可以继承 promise 留给我们的许多遗产。

```javascript
async function asyncfunc(time){  
  await timeout(time)
  console.log('hello')
  return 20
}

async (() => {  
  const r = await asyncfunc(3000) // r = 20
})()

asyncfunc(3000)  
  .then(data => {
    console.log(data) // 20
  })

asyncfunc(3000) // hello  
```

最后一个例子中，async function 调用后返回的 Promise 本身是立即执行的，所以如果你不想管后面发生了什么，也可以直接这样调用。

除此之外，Promise.all 和 Promise.race 等也同样有用

```javascript
const times = [1000, 2000, 3000, 4000];  
(async () => {
    await Promise.all(times.map(time =>
                                asyncfunc(time)))
    console.log('done')
})()
```

### 异常处理

上面说到，async function 实际上就是 Promise，而 Promise 并不能通过 try-catch 来捕获异常（事实上我们没有办法捕获异步回调中的的异常），是通过 reject 调用相应的回调的。而 await 实际上会在被 reject 时 throw 相应的异常：

```javascript
(async () => {
  try {
    await Promise.reject('aha')
  } catch(e) {
    console.log(e)
  }
})();
```

会得到 aha。

相应的，在 async function 中 throw 也会导致其返回的 Promise reject：

```javascript
(async () => {
  throw('aha')
})().catch(err => {
  console.log(err) // aha
})
```

在早期版本的 nodejs 中不会处理没有 catch 的 Promise reject，程序会自己悄无声息的崩掉，参见 [Node and IO silently exits when error thrown in promise catch · Issue #600 · nodejs/node · GitHub](https://link.zhihu.com/?target=https%3A//github.com/nodejs/node/issues/600) 。如果需要在早期版本的 node 中处理，可以考虑加上

```javascript
process.on("unhandledRejection", (reason, promise) => {  
  throw reason;
})
```

[浅析JavaScript异步](https://www.cnblogs.com/aaron---blog/p/10903118.html)

## JS——一门单线程语言

多线程要考虑线程之间的资源抢占，死锁，冲突等一系列问题。（如Java）

JavaScript作为一门客户端脚本，没有多线程的一系列问题。JavaScript语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。那么，为什么JavaScript不能有多个线程呢？因为这样能提高效率。

JavaScript的单线程，与它的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。

> 比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

为了利用多核CPU的计算能力，HTML5提出Web Worker标准，允许JavaScript脚本创建多个线程，但是子线程完全受主线程控制，且不得操作DOM。所以，这个新标准并没有改变JavaScript单线程的本质。

### 任务队列

单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。

如果排队是因为计算量大，CPU忙不过来，倒也算了，但是很多时候CPU是闲着的，因为IO设备（输入输出设备）很慢（比如Ajax操作从网络读取数据），不得不等着结果出来，再往下执行。

JavaScript语言的设计者意识到，这时主线程完全可以不管IO设备，挂起处于等待中的任务，先运行排在后面的任务。等到IO设备返回了结果，再回过头，把挂起的任务继续执行下去。

于是，所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入”任务队列”（task queue）的任务，只有”任务队列”通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

具体来说，异步执行的运行机制如下。（同步执行也是如此，因为它可以被视为没有异步任务的异步执行。）

* 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
* 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
* 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
* 主线程不断重复上面的第三步。

通过上面的描述，可以得到说异步全都得等到同步都完成了才会执行到的。只要主线程空了，就会去读取”任务队列”，这就是JavaScript的运行机制。这个过程会不断重复。

### 事件和回调函数

“任务队列”是一个事件的队列（也可以理解成消息的队列），IO设备完成一项任务，就在”任务队列”中添加一个事件，表示相关的异步任务可以进入”执行栈”了。主线程读取”任务队列”，就是读取里面有哪些事件。

“任务队列”中的事件，除了IO设备的事件以外，还包括一些用户产生的事件（比如鼠标点击、页面滚动等等）。**只要指定过回调函数，这些事件发生时就会进入”任务队列”，等待主线程读取。**

> 注意和Android的主线程区分呀，Android是使用Java，可以用多线程的，通过handler在不同线程间切换，并且全局变量是线程共享的。

所谓”回调函数”（callback），就是那些会被主线程挂起来的代码。异步任务必须指定回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。

“任务队列”是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。主线程的读取过程基本上是自动的，只要执行栈一清空，”任务队列”上第一位的事件就自动进入主线程。但是，由于存在后文提到的”定时器”功能，主线程首先要检查一下执行时间，某些事件只有到了规定的时间，才能返回主线程。

### Event Loop

主线程从”任务队列”中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。

为了更好地理解Event Loop，请看下图。

![](https://img-blog.csdn.net/20160708162153924)

上图中，主线程运行的时候，产生堆（heap）和栈（stack），栈中的代码调用各种外部API，它们在”任务队列”中加入各种事件（click，load，done）。只要栈中的代码执行完毕，主线程就会去读取”任务队列”，依次执行那些事件所对应的回调函数。

执行栈中的代码（同步任务），总是在读取”任务队列”（异步任务）之前执行。请看下面这个例子。

```javascript
var req = new XMLHttpRequest();
req.open('GET', url);    
req.onload = function (){};    
req.onerror = function (){};    
req.send();
```

上面代码中的req.send方法是Ajax操作向服务器发送数据，它是一个异步任务，意味着只有当前脚本的所有代码执行完，系统才会去读取”任务队列”。所以，它与下面的写法等价。

```javascript
var req = new XMLHttpRequest();
req.open('GET', url);
req.send();
req.onload = function (){};    
req.onerror = function (){}; 
```

也就是说，指定回调函数的部分（onload和onerror），在send()方法的前面或后面无关紧要，**因为它们属于执行栈的一部分，系统总是执行完它们，才会去读取”任务队列”**。

### 定时器

除了放置异步任务的事件，**”任务队列”**还可以放置定时事件，即指定某些代码在多少时间之后执行。这叫做”定时器”（timer）功能，也就是定时执行的代码。

定时器功能主要由setTimeout()和setInterval()这两个函数来完成，它们的内部运行机制完全一样，区别在于前者指定的代码是一次性执行，后者则为反复执行。以下主要讨论setTimeout()。

setTimeout()接受两个参数，第一个是回调函数，第二个是推迟执行的毫秒数。

```javascript
console.log(1);
setTimeout(function() {
    console.log(2);
}, 1000);
console.log(3);
```

上面代码的执行结果是1，3，2，因为setTimeout()将第二行推迟到1000毫秒之后执行。

如果将setTimeout()的第二个参数设为0，就表示当前代码执行完（执行栈清空）以后，立即执行（0毫秒间隔）指定的回调函数。

```javascript
setTimeout(function() {
    console.log(1);
}, 0);
console.log(2);
```

上面代码的执行结果总是2，1，因为只有在执行完第二行以后，系统才会去执行”任务队列”中的回调函数。

总之，setTimeout(fn, 0)的含义是，指定某个任务在主线程最早可得的空闲时间执行，也就是说，尽可能早地执行。它在”任务队列”的尾部添加一个事件，因此要等到同步任务和”任务队列”现有的事件都处理完，才会得到执行。

HTML5标准规定了setTimeout()的第二个参数的最小值（最短间隔），不得低于4毫秒，如果低于这个值，就会自动增加。在此之前，老版本的浏览器都将最短间隔设为10毫秒。另外，对于那些DOM的变动（尤其是涉及页面重新渲染的部分），通常不会立即执行，而是每16毫秒执行一次。这时使用 requestAnimationFrame() 的效果要好于 setTimeout()。

需要注意的是，setTimeout()只是将事件插入了”任务队列”，必须等到当前代码（执行栈）执行完，主线程才会去执行它指定的回调函数。要是当前代码耗时很长，有可能要等很久，所以并没有办法保证，回调函数一定会在setTimeout()指定的时间执行。

### Node.js的Event Loop

Node.js也是单线程的Event Loop，但是它的运行机制不同于浏览器环境。

![](https://img-blog.csdn.net/20160708162523449)

根据上图，Node.js的运行机制如下：

* V8引擎解析JavaScript脚本。
* 解析后的代码，调用Node API。
* libuv库负责Node API的执行。它将不同的任务分配给不同的线程，形成一个Event Loop（事件循环），以异步的方式将任务的执行结果返回给V8引擎。
* V8引擎再将结果返回给用户。

除了 setTimeout() 和 setInterval() 这两个方法，Node.js还提供了另外两个与”任务队列”有关的方法：process.nextTick() 和 setImmediate()。它们可以帮助我们加深对”任务队列”的理解。

process.nextTick() 方法可以在当前”执行栈”的尾部——下一次Event Loop（主线程读取”任务队列”）之前——触发回调函数。也就是说，它指定的任务总是发生在所有异步任务之前。

setImmediate() 方法则是在当前”任务队列”的尾部添加事件，也就是说，它指定的任务总是在下一次Event Loop时执行，这与setTimeout(fn, 0)很像。请看下面的例子（via StackOverflow）。

```javascript
process.nextTick(function A() {
  console.log(1);
  process.nextTick(function B(){console.log(2);});
});
 
setTimeout(function timeout() {
  console.log('TIMEOUT FIRED');
}, 0)
// 1
// 2
// TIMEOUT FIRED
```

上面代码中，由于 process.nextTick() 方法指定的回调函数，总是在当前”执行栈”的尾部触发，所以不仅函数A比 setTimeout() 指定的回调函数 timeout() 先执行，而且函数B也比 timeout() 先执行。

这说明，如果有多个process.nextTick语句（不管它们是否嵌套），将全部在当前”执行栈”执行。

接下来看 setImmediate()：

```javascript
setImmediate(function A() {
  console.log(1);
  setImmediate(function B(){
      console.log(2);
  });
});
 
setTimeout(function timeout() {
  console.log('TIMEOUT FIRED');
}, 0);
```

上面代码中，setImmediate() 与 setTimeout(fn, 0) 各自添加了一个回调函数A和timeout，都是在下一次Event Loop触发。

那么，哪个回调函数先执行呢？答案是不确定。运行结果可能是1–TIMEOUT FIRED–2，也可能是TIMEOUT FIRED–1–2。

令人困惑的是，Node.js文档中称，setImmediate() 指定的回调函数，总是排在 setTimeout() 前面。实际上，这种情况只发生在递归调用的时候。

```javascript
setImmediate(function (){
    setImmediate(function A() {
        console.log(1);
        setImmediate(function B() {
            console.log(2);
        });
    });

    setTimeout(function timeout() {
        console.log('TIMEOUT FIRED');
    }, 0);
});
// 1
// TIMEOUT FIRED
// 2
```

上面代码中，setImmediate() 和 setTimeout() 被封装在一个setImmediate() 里面，它的运行结果总是1–TIMEOUT FIRED–2，这时函数A一定在timeout前面触发。至于2排在TIMEOUT FIRED的后面（即函数B在timeout后面触发），是因为 setImmediate() 总是将事件注册到下一轮Event Loop，所以函数A和timeout是在同一轮Loop执行，而函数B在下一轮Loop执行。

我们由此得到了 process.nextTick() 和 setImmediate() 的一个重要区别：多个 process.nextTick() 语句总是在当前”执行栈”一次执行完，多个 setImmediate() 可能则需要多次loop才能执行完。事实上，这正是Node.js 10.0版添加 setImmediate() 方法的原因，否则像下面这样的递归调用process.nextTick()，将会没完没了，主线程根本不会去读取”事件队列”！

```javascript
process.nextTick(function foo() {
    process.nextTick(foo);
});
```

事实上，现在要是你写出递归的process.nextTick，Node.js会抛出一个警告，要求你改成setImmediate。

另外，由于process.nextTick指定的回调函数是在本次”事件循环”触发，而setImmediate指定的是在下次”事件循环”触发，所以很显然，前者总是比后者发生得早，而且执行效率也高（因为不用检查”任务队列”）。