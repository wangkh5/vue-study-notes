# Vue2 学习笔记3

标签： WEB前端

---

## 定义Vue组件
什么是组件： 组件的出现，就是为了拆分Vue实例的代码量的，能够让我们以不同的组件，来划分不同的功能模块，将来我们需要什么样的功能，就可以去调用对应的组件即可；
组件化和模块化的不同：
 + 模块化： 是从代码逻辑的角度进行划分的；方便代码分层开发，保证每个功能模块的职能单一；
 + 组件化： 是从UI界面的角度进行划分的；前端的组件化，方便UI组件的重用；
### 全局组件定义的三种方式
#### 使用 Vue.extend 配合 Vue.component 方法：
```
<body>
  <div id="app">
    <!-- 如果要使用组件，直接，把组件的名称，以 HTML 标签的形式，引入到页面中，即可 -->
    <mycom1></mycom1>
  </div>

  <script>
    // 1.1 使用 Vue.extend 来创建全局的Vue组件
    // var com1 = Vue.extend({
    //   template: '<h3>这是使用 Vue.extend 创建的组件</h3>' // 通过 template 属性，指定了组件要展示的HTML结构
    // })
    // 1.2 使用 Vue.component('组件的名称', 创建出来的组件模板对象)
    // Vue.component('myCom1', com1)
    // 如果使用 Vue.component 定义全局组件的时候，组件名称使用了 驼峰命名，则在引用组件的时候，需要把 大写的驼峰改为小写的字母，同时，两个单词之前，使用 - 链接；
    // 如果不使用驼峰,则直接拿名称来使用即可;
    // Vue.component('mycom1', com1)

    // Vue.component 第一个参数:组件的名称,将来在引用组件的时候,就是一个 标签形式 来引入 它的
    // 第二个参数: Vue.extend 创建的组件  ,其中 template 就是组件将来要展示的HTML内容
    Vue.component('mycom1', Vue.extend({
      template: '<h3>这是使用 Vue.extend 创建的组件</h3>'
    }))

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {}
    });
  </script>
</body>
```
#### 直接使用 Vue.component 方法：
```
<body>
  <div id="app">
    <!-- 还是使用 标签形式,引入自己的组件 -->
    <mycom2></mycom2>
  </div>

  <script>
    // 注意:不论是哪种方式创建出来的组件,组件的 template 属性指向的模板内容,必须有且只能有唯一的一个根元素
    Vue.component('mycom2', {
      template: '<div><h3>这是直接使用 Vue.component 创建出来的组件</h3><span>123</span></div>'
    })

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {}
    });
  </script>
</body>
```
#### 将模板字符串，定义到<template></template>标签中：
```
<body>
  <div id="app">
    <mycom3></mycom3>
  </div>
 
 <!-- 在 被控制的 #app 外面,使用 template 元素,定义组件的HTML模板结构  -->
  <template id="tmpl">
    <div>
      <h1>这是通过 template 元素,在外部定义的组件结构,这个方式,有代码的只能提示和高亮</h1>
      <h4>好用,不错!</h4>
    </div>
  </template>
 
 <script>
    Vue.component('mycom3', {
      template: '#tmpl'
    })

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {}
    });
  </script>
</body>
```

> 注意： 组件中的DOM结构，有且只能有唯一的根元素（Root Element）来进行包裹！

### 定义私有组件
```
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <title>Document</title>
     <script src="./lib/vue-2.4.0.js"></script>
   </head>
   <body>
     <div id="app">
       <mycom3></mycom3>
       <!-- <login></login> -->
     </div>

     <div id="app2">
       <mycom3></mycom3>
       <login></login>
     </div>
    
     <!-- 在 被控制的 #app 外面,使用 template 元素,定义组件的HTML模板结构  -->
     <template id="tmpl">
       <div>
         <h1>这是通过 template 元素,在外部定义的组件结构,这个方式,有代码的只能提示和高亮</h1>
         <h4>好用,不错!</h4>
       </div>
     </template>
    
     <template id="tmpl2">
       <h1>这是私有的 login 组件</h1>
     </template>
    
     <script>
       //全局组件
       Vue.component('mycom3', {
         template: '#tmpl'
       })
    
       // 创建 Vue 实例，得到 ViewModel
       var vm = new Vue({
         el: '#app',
         data: {},
         methods: {}
       });
    
       var vm2 = new Vue({
         el: '#app2',
         data: {},
         methods: {},
         filters: {},
         directives: {},
         components: { // 定义实例内部私有组件的
           login: {
             //template:'<h1>这是私有的 login 组件</h1>'
             template: '#tmpl2'
           }
         }
       })
     </script>
   </body>
   </html>
```

### 组件中展示数据和响应事件

1. 在组件中，`data`需要被定义为一个方法，例如：

```
<body>
  <div id="app">
    <mycom1></mycom1>
  </div>

  <script>
    // 1. 组件可以有自己的 data 数据
    // 2. 组件的 data 和 实例的 data 有点不一样,实例中的 data 可以为一个对象,但是 组件中的 data 必须是一个方法
    // 3. 组件中的 data 除了必须为一个方法之外,这个方法内部,还必须返回一个对象才行;
    // 4. 组件中 的data 数据,使用方式,和实例中的 data 使用方式完全一样!!!
    Vue.component('mycom1', {
      template: '<h1>这是全局组件 --- {{msg}}</h1>',
      data: function () {
        return {
          msg: '这是组件的中data定义的数据'
        }
      }
    })

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {}
    });
  </script>
</body>
```
2. 在子组件中，如果将模板字符串，定义到了script标签中，那么，要访问子组件身上的`data`属性中的值，需要使用`this`来访问；

### 【重点】为什么组件中的data属性必须定义为一个方法并返回一个对象
通过计数器案例演示
```
<body>
  <div id="app">
    <counter></counter>
    <hr>
    <counter></counter>
    <hr>
    <counter></counter>
  </div>


  <template id="tmpl">
    <div>
      <input type="button" value="+1" @click="increment">
      <h3>{{count}}</h3>
    </div>
  </template>

  <script>
    var dataObj = { count: 0 }

    // 这是一个计数器的组件, 身上有个按钮,每当点击按钮,让 data 中的 count 值 +1
    Vue.component('counter', {
      template: '#tmpl',
      data: function () {
        // return dataObj
        return { count: 0 }
      },
      methods: {
        increment() {
          this.count++
        }
      }
    })

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {}
    });
  </script>
</body>
```

### 使用`components`属性定义局部子组件
1. 组件实例定义方式：
```
<script>
    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {},
      methods: {},
      components: { // 定义子组件
        account: { // account 组件
          template: '<div><h1>这是Account组件{{name}}</h1><login></login></div>', // 在这里使用定义的子组件
          components: { // 定义子组件的子组件
            login: { // login 组件
              template: "<h3>这是登录组件</h3>"
            }
          }
        }
      }
    });
  </script>
```
2. 引用组件：
```
<div id="app">
    <account></account>
  </div>
```

### 使用`flag`标识符结合`v-if`和`v-else`切换组件
```
<body>
  <div id="app">
    <a href="" @click.prevent="flag=true">登录</a>
    <a href="" @click.prevent="flag=false">注册</a>

    <login v-if="flag"></login>
    <register v-else="flag"></register>

  </div>

  <script>
    Vue.component('login', {
      template: '<h3>登录组件</h3>'
    })

    Vue.component('register', {
      template: '<h3>注册组件</h3>'
    })

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {
        flag: false
      },
      methods: {}
    });
  </script>
</body>
```

### 使用`:is`属性来切换不同的子组件
```
<body>
  <div id="app">
    <a href="" @click.prevent="comName='login'">登录</a>
    <a href="" @click.prevent="comName='register'">注册</a>

    <!-- Vue提供了 component ,来展示对应名称的组件 -->
    <!-- component 是一个占位符, :is 属性,可以用来指定要展示的组件的名称 -->
    <component :is="comName"></component>

  </div>

  <script>
    // 组件名称是 字符串
    Vue.component('login', {
      template: '<h3>登录组件</h3>'
    })

    Vue.component('register', {
      template: '<h3>注册组件</h3>'
    })

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
      el: '#app',
      data: {
        comName: 'login' // 当前 component 中的 :is 绑定的组件的名称
      },
      methods: {}
    });
  </script>
</body>

```