# Vue2 学习笔记4

标签： WEB前端

---
[文中例子代码请参考github][1]
## 父组件向子组件传值
+ 组件实例定义方式，注意：一定要使用`props`属性来定义父组件传递过来的数据
```
<script>
    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {
        msg: '这是父组件中的消息'
      },
      components: {
        son: {
          template: '<h1>这是子组件 --- {{finfo}}</h1>',
          props: ['finfo']
        }
      }
    });
  </script>
```
+ 使用`v-bind`或简化指令，将数据传递到子组件中：
```
<div id="app">
    <son :finfo="msg"></son>
  </div>
```
+ 完整实例
```
<body>
  <div id="app">
    <!-- 父组件，可以在引用子组件的时候， 通过 属性绑定（v-bind:） 的形式, 把 需要传递给 子组件的数据，以属性绑定的形式，传递到子组件内部，供子组件使用 -->
    <com1 v-bind:parentmsg="msg"></com1>
  </div>

  <script>
    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {
        msg: '123 啊-父组件中的数据'
      },
      methods: {},

      components: {
        // 结论：经过演示，发现，子组件中，默认无法访问到 父组件中的 data 上的数据 和 methods 中的方法
        com1: {
          data() { // 注意： 子组件中的 data 数据，并不是通过 父组件传递过来的，而是子组件自身私有的，比如： 子组件通过 Ajax ，请求回来的数据，都可以放到 data 身上；
            // data 上的数据，都是可读可写的；
            return {
              title: '123',
              content: 'qqq'
            }
          },
          template: '<h1 @click="change">这是子组件 --- {{ parentmsg }}</h1>',
          // 注意： 组件中的 所有 props 中的数据，都是通过 父组件传递给子组件的
          // props 中的数据，都是只读的，无法重新赋值
          props: ['parentmsg'], // 把父组件传递过来的 parentmsg 属性，先在 props 数组中，定义一下，这样，才能使用这个数据
          directives: {},
          filters: {},
          components: {},
          methods: {
            change() {
              this.parentmsg = '被修改了'
            }
          }
        }
      }
    });
  </script>
</body>
```

## 子组件向父组件传值
- 原理：父组件将方法的引用，传递到子组件内部，子组件在内部调用父组件传递过来的方法，同时把要发送给父组件的数据，在调用方法的时候当作参数传递进去；
- 父组件将方法的引用传递给子组件，其中，`getMsg`是父组件中`methods`中定义的方法名称，`func`是子组件调用传递过来方法时候的方法名称
```
<son @func="getMsg"></son>
```
- 子组件内部通过`this.$emit('方法名', 要传递的数据)`方式，来调用父组件中的方法，同时把数据传递给父组件使用
- 完整实例
```
<body>
  <div id="app">
    <!-- 父组件向子组件 传递 方法，使用的是 事件绑定机制； v-on, 当我们自定义了 一个 事件属性之后，那么，子组件就能够，通过某些方式，来调用 传递进去的 这个 方法了 -->
    <com2 @func="show"></com2>
  </div>

  <template id="tmpl">
    <div>
      <h1>这是 子组件</h1>
      <input type="button" value="这是子组件中的按钮 - 点击它，触发 父组件传递过来的 func 方法" @click="myclick">
    </div>
  </template>

  <script>

    // 定义了一个字面量类型的 组件模板对象
    var com2 = {
      template: '#tmpl', // 通过指定了一个 Id, 表示 说，要去加载 这个指定Id的 template 元素中的内容，当作 组件的HTML结构
      data() {
        return {
          sonmsg: { name: '小头儿子', age: 6 }
        }
      },
      methods: {
        myclick() {
          // 当点击子组件的按钮的时候，如何 拿到 父组件传递过来的 func 方法，并调用这个方法？？？
          //  emit 英文原意： 是触发，调用、发射的意思
          // this.$emit('func123', 123, 456)
          this.$emit('func', this.sonmsg)
        }
      }
    }


    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {
        datamsgFormSon: null
      },
      methods: {
        show(data) {
          // console.log('调用了父组件身上的 show 方法: --- ' + data)
          // console.log(data);
          this.datamsgFormSon = data
        }
      },

      components: {
        com2
        // com2: com2
      }
    });
  </script>
</body>
```
## 评论列表案例
目标：主要练习父子组件之间传值
代码参考github day4代码组件案例，评论列表
[文中例子代码请参考github][1]

## 使用 `this.$refs` 来获取元素和组件
```
  <div id="app">
    <div>
      <input type="button" value="获取元素内容" @click="getElement" />
      <!-- 使用 ref 获取元素 -->
      <h1 ref="myh1">这是一个大大的H1</h1>

      <hr>
      <!-- 使用 ref 获取子组件 -->
      <my-com ref="mycom"></my-com>
    </div>
  </div>

  <script>
    Vue.component('my-com', {
      template: '<h5>这是一个子组件</h5>',
      data() {
        return {
          name: '子组件'
        }
      }
    });

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {
        getElement() {
          // 通过 this.$refs 来获取元素
          console.log(this.$refs.myh1.innerText);
          // 通过 this.$refs 来获取组件
          console.log(this.$refs.mycom.name);
        }
      }
    });
  </script>
```

## 什么是路由
1. 对于普通的网站，所有的超链接都是URL地址，所有的URL地址都对应服务器上对应的资源；

2. 对于单页面应用程序来说，主要通过URL中的hash(#号)来实现不同页面之间的切换，同时，hash有一个特点：HTTP请求中不会包含hash相关的内容；所以，单页面程序中的页面跳转主要用hash实现；

3. 在单页面应用程序中，这种通过hash改变来切换页面的方式，称作前端路由（区别于后端路由）；

## 在 vue 中使用 vue-router
1 导入 vue-router 组件类库：
```
<!-- 1. 导入 vue-router 组件类库 -->
  <script src="./lib/vue-router-2.7.0.js"></script>
```
2 使用 router-link 组件来导航
```
<!-- 2. 使用 router-link 组件来导航 -->
<router-link to="/login">登录</router-link>
<router-link to="/register">注册</router-link>
```
3 使用 router-view 组件来显示匹配到的组件
```
<!-- 3. 使用 router-view 组件来显示匹配到的组件 -->
<router-view></router-view>
```
4 创建使用`Vue.extend`创建组件
```
    // 4.1 使用 Vue.extend 来创建登录组件
    var login = Vue.extend({
      template: '<h1>登录组件</h1>'
    });
    
    // 4.2 使用 Vue.extend 来创建注册组件
    var register = Vue.extend({
      template: '<h1>注册组件</h1>'
    });
```
5 创建一个路由 router 实例，通过 routers 属性来定义路由匹配规则
```
// 5. 创建一个路由 router 实例，通过 routers 属性来定义路由匹配规则
    var router = new VueRouter({
      routes: [
        { path: '/login', component: login },
        { path: '/register', component: register }
      ]
    });
```
6 使用 router 属性来使用路由规则
```
// 6. 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      router: router // 使用 router 属性来使用路由规则
    });
```

## 设置路由高亮
- vue 提供的类名：router-link-active，我们只要对该类自定义样式即可.
```
    .router-link-active {
      color: red;
      font-weight: 800;
      font-style: italic;
      font-size: 80px;
      text-decoration: underline;
      background-color: green;
    }
```
-  自定义高亮样式类的名称：
```
 var routerObj = new VueRouter({
      routes: [ 
        { path: '/login', component: login },
        { path: '/register', component: register }
      ],
        /*自定义高亮 样式类的名称*/
      linkActiveClass: 'myactive'
    })
```

## 在路由规则中定义参数
1. 在规则中定义参数：
```
{ path: '/register/:id', component: register }
```
2. 通过 `this.$route.params`来获取路由中的参数：
```
var register = Vue.extend({
      template: '<h1>注册组件 --- {{this.$route.params.id}}</h1>'
    });
```

## 使用 `children` 属性实现路由嵌套
```
<body>
  <div id="app">
    <router-link to="/account">Account</router-link>
    <router-view></router-view>
  </div>

  <template id="tmpl">
    <div>
      <h1>这是 Account 组件</h1>

      <router-link to="/account/login">登录</router-link>
      <router-link to="/account/register">注册</router-link>

      <router-view></router-view>
    </div>
  </template>

  <script>
    // 组件的模板对象
    var account = {
      template: '#tmpl'
    }
    var login = {
      template: '<h3>登录</h3>'
    
    var register = {
      template: '<h3>注册</h3>'
    }
    var router = new VueRouter({
      routes: [
        {
          path: '/account',
          component: account,
          // 使用 children 属性，实现子路由，同时，子路由的 path 前面，不要带 / ，否则永远以根路径开始请求，这样不方便我们用户去理解URL地址
          children: [
            { path: 'login', component: login },
            { path: 'register', component: register }
          ]
        }
        // { path: '/account/login', component: login },
        // { path: '/account/register', component: register }
      ]
    })

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {},
      router
    });
  </script>
</body>
```

## 命名视图实现经典布局
```
  <style>
    html,
    body {
      margin: 0;
      padding: 0;
    }
    .header {
      background-color: orange;
      height: 80px;
    }
    h1 {
      margin: 0;
      padding: 0;
      font-size: 16px;
    }
    .container {
      display: flex;
      height: 600px;
    }
    .left {
      background-color: lightgreen;
      flex: 2;
    }
    .main {
      background-color: lightpink;
      flex: 8;
    }
  </style>
</head>

<body>
  <div id="app">
    <router-view></router-view>
    <div class="container">
      <router-view name="left"></router-view>
      <router-view name="main"></router-view>
    </div>
  </div>

  <script>
    var header = {
      template: '<h1 class="header">Header头部区域</h1>'
    }
    var leftBox = {
      template: '<h1 class="left">Left侧边栏区域</h1>'
    }
    var mainBox = {
      template: '<h1 class="main">mainBox主体区域</h1>'
    }
    // 创建路由对象
    var router = new VueRouter({
      routes: [
        /* { path: '/', component: header },
        { path: '/left', component: leftBox },
        { path: '/main', component: mainBox } */
        {
          path: '/', components: {
            'default': header,
            'left': leftBox,
            'main': mainBox
          }
        }
      ]
    })
    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {},
      router
    });
  </script>
</body>
```

## 相关文件
1. [URL中的hash（井号）](http://www.cnblogs.com/joyho/articles/4430148.html)

  [1]: https://github.com/wangkh5/vue-study-notes








