# vue

## 前端框架

### 什么是前端框架

前端开发进化过程： **原生JS 》jQuery 等类库 》Vue 等前端框架**

- jQuery 等类库提供了已封装好的 JS 方法集合，用户在前端开发中可以直接调用（可以使用多个）。
- Vue 等前端框架提供了完整的项目解决方案，用户在前端开发中必须按照特定框架规范进行开发（只能选择一个）。

目前主流的前端框架有 Vue 、 React 、 Angular 。

### 为什么要使用 vue

Vue 主要有以下两大特征：
1. 响应式数据绑定：数据发生改变，视图自动更新（开发者不再关注 dom 操作，进一步提高开发效率）
2. 可组合视图组件：视图按照功能切分成基本单元（易维护，易重用，易测试）


### 使用 vue 前端框架

- **引入外部文件(CDN)**

```html
<!-- 开发环境版本，包含命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

<!-- 生产环境版本，优化文件大小和响应速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

- **命令行工具(CLI)**

vue-cli 是基于 Node.js 的 vue 快捷开发工具（必须首先安装 Node.js）。

控制台输入 `npm install @vue/cli -g` 全局安装

1. `vue create project-name` 直接创建项目

2. `vue ui` 开启图形化工具（强烈推荐，可以创建和管理项目）

### vue 项目结构

vue 项目由上述两种方式自动创建，其项目结构如下：

- **node_module 文件夹**  / 依赖包目录

- **public 文件夹** /  静态资源，外部可直接访问

  - **index.html** / 输出界面
  - **favicon.ico** / 图标

- **src 文件夹** / 组件等资源，由静态资源加载

  - **asserts 文件夹** / css、img 文件
  - **components 文件夹** / vue 文件

- **plugins 文件夹** / 插件文件

- **router 文件夹** / 路由文件

- **App.vue** / 核心组件

- **main.js** / 入口文件

还有一些其他配置文件，比如项目配置文件 package.json。
用户可以创建 vue.config.js 对 vue 进行自定义配置，默认覆盖原配置。

### vue 常用插件


### 组件库

不用自己画组件，可以使网站快速成型。推荐直接在 图形化工具内导入

官网：https://element.eleme.cn/#/zh-CN/component/installation

导入element-ui等桌面组件库，bootstrap 等移动端组件库

安装依赖包 npm install element-ui -S

导入资源即可

import ElementUI from 'element-ui'; 
import 'element-ui/lib/theme-chalk/index.css'// 样式

Vue.use(ElementUI);

---

## vue 组件

vue 前端框架的基本功能单元是组件，vue 对象本身也是一个组件（根组件）。

### vue 单文件组件(.vue)

```vue
<template>
  模板内容 html
</template>

<script>
  业务逻辑 export
</script>

<style>
  组件样式 css
</style>
```

### 全局组件

```js
Vue.component("greet-bar",{  //也可以使用驼峰式命名 greetBar
  template:'
    <div>
      <p>大家好，我是{{name}}</p>
      <button value="改名" v-on:click="changeName"></button>
    </div>
  ',
  data:function(){
    return {name:"王东浩"}
  },
  methods:{
    changeName:function(){
      this.name="甘甜"
    }
  }
})
```
Vue.component 声明全局组件
- template : 组件模板。只能含有一个根元素。
- data : 模板中调用数据。必须以函数的形式返回。
- methods : 组件中调用方法。

组件必须在声明的 vue 对象中使用，即被根组件调用：

```html
<div id="app">  
  <greet-bar></greet-bar>
  <greet-bar></greet-bar>
</div>

<script>
  new Vue({
    el:"#app",
    data:{}
  });
</script>
```

<font size=2 color=red>html 文件元素名和属性名不区分大小写，因此不可采用驼峰形式：但在 vue 组件中也可以作为驼峰形式识别。</font>

### 局部组件

```js
var componentA = {
  data:function(){
    return {name:"王东浩"}
  ,
  template:'<p>hello {{name}}</p>'
};

var componentB = {
  data:function(){
    return {name:"陈伯言"}
  ,
  template:'<p>hello {{name}}</p>'
};
```

局部组件只能在局部，即指定的 vue 对象中使用：

```html
<div id="app">  
  <greet-a></greet-a>  
  <greet-b></greet-b>
</div>

<script>
  new Vue({
    el:"#app",
    data:{},
    components:{
      'greet-a': componentA,
      'greet-b': componentB
    }
  });
</script>
```
### 组件间数据交互

**父组件向子组件传值**，在父组件中定义数据

```html
<div id="app">  
  <greet-bar :first-name='sname' last-name = '赵四'></greet-bar>
</div>

<script>
  new Vue({
    el:"#app",
    data:{sname:"尼古拉斯"}
  });
</script>
```

可以被子组件所读取并显示

```js
Vue.component("greet-bar",{
  props::['first-name', 'last-name'],  //也可以使用驼峰式接收 firstName
  template:'
    <div>
      <p>大家好，我是{{first-name + "·" + last-name}}</p>
    </div>
  '
})
```

props 是单向数据流，只能用于父组件向子组件传值。

**子组件向父组件传值**，子组件绑定自定义事件

在template中 `<button @click='$emit("change-word", 0.1)'>点击</button>`

第一个属性为事件标记，第二个属性为传递数值。

父组件中监听事件并触发方法 `<menu-item @change-word='handle($event)'> </menu-item>`

$event 为传递数值，名字不可更改。

**非父子组件传值**，必须创建一个 vue 对象作为事件中心居中协调，监听两个子组件事件并通过props传递给另一个子组件。


### 组件插槽

在组件的template中添加`<slot>默认内容可选</slot>`

可以自动读取`<greet-bar>内容</greet-bar>`中的内容并展示。


---

### 创建 vue 对象

在 JS 代码中创建 vue 对象：

- el：元素ID（作用域）
- data：数据
- methods：方法（使用数据发生变化，重新执行方法刷新页面）
- computed: 计算属性（使用数据发生变化，页面不会更新）
- created: 创建时调用方法
- components: 组件
- watch: 监听器（检测到相应数据变化后执行，常用于处理异步操作）
- filter: 过滤器（对显示数据进行过滤和修改）

```html
  <script>
    var app = new Vue({
      el: '#app',
      data: {
        name:'Vue',
        message: 'Hello Vue'
      },
      methods:{
        show:function(){
            alert(message)
            console.log(message)
            //也可以 return
        }
      },
      watch:{
        name:function(val){
          this.message = 'Hello ' + val;
        }
      }，
      filter:{
        upper:function(val){
          return val.charAt(0).toUpperCase() + val.slice(1)
        }
      }
    });
    app.message="GoodBye Vue."; // vue 对象数据可以被 JS 代码更新
  </script>
```

### 数据显示

`<p>{{ message }}</p>`

- 插值表达式：vue-data 中读取元素内容。

`<p v-text="message"></p> `

- v-text: vue-data 中读取元素内容。

`<p v-html="message"></p> `

- v-html: vue-data 中读取元素内容。数据中<>不会被转义。


### 数据过滤

过滤器的使用，有以下两种方式

1. `<div>{{message | upper}}</div>`
2. `<div v-bind:id="id | upper"></div>`

过滤器还可以传递参数：`<div v-bind:id="id | upper(2,'hello')"></div>`



```html
<!--定义全局过滤器并接收参数-->
  <script>
    Vue.filter('upper', function(val,arg1,arg2){
      console.log(arg2);
      return val.charAt(arg1).toUpperCase() + val.slice(arg1 + 1);
    })
  </script>
```

### 指令绑定

`<input type=button value="按钮" v-bind:title="message" v-on:click="show">`

- v-bind: 绑定属性，表示该属性内容从 vue-data 中加载。可以用 : 代替。
- v-on: 绑定事件，表示该事件从 vue-methods 中加载。可以用 @ 代替。

`<img class="box" src="{{url}}" >`

- 插值表达式绑定：给img标签的src属性赋值

<font size=2 color=red>JS 默认属性均为字符串，但 vue 绑定属性能自动识别数据为数值、布尔型、数组或对象。</font>

`<div class="box" :class="{'textColor':isColor, 'textSize':isSize}">` 

【示例】绑定boolean属性：判断是否使用textColor和textSize类



**可绑定事件**

**事件修饰符**

当父级元素和子级元素被同一个事件触发指令时，会先执行子级元素的指令，再执行父级元素的指令。

- .prevent 阻止事件默认行为（比如超链接点击跳转，表单点击提交）。
- .stop  阻止冒泡调用，不再执行父级的指令。
- .capture 捕获调用，先执行父级的指令再执行子级的指令。
- .self 该指令只有元素本身被点击才执行，不会被子级的指令冒泡调用。
- .once 事件只触发一次，被触发后该指令无效。

### 表单输入绑定

Vue 使用单向绑定机制：后台数据发生改变后，页面显示会自动同步；但如果页面中表单输入发生变化，后台数据不会发生更新。

`<input v-model="age" type="number">`

- v-model: 运用于表单输入元素，实现双向绑定。输入发生变化后台数据也会实时更新。

**表单域修饰符**

- number  转化为数值
- trim  去掉首尾空格
- lazy  鼠标离开输入元素后才更新（验证用户名是否已被使用时常用）

### 条件渲染

对于 布尔数据

```html
<script>
  new Vue({
    el:'#app',
    data:{
      existA:false,
      existB:true,
      surface:true
    }
  });
</script>
```

```html
<p v-if="existA">你好，我是A</p>
<p v-else-if="existB">你好，我是B</p>  
<p v-else v-show="surface">不好意思，A和B都不在</p>
```

- v-if: boolean 数据判断是否绘制元素
- v-show: boolean 数据判断是否显示 / 隐藏元素

### 列表渲染

对于 数组数据

```html
<script>
  var vm = new Vue({
    el:'#app',
    data:{
      list:[1,2,3,4,5,6],
      user:{
        id:1,
        name:'王东浩'
      },
      userList:[
        {id:1, name:'zs1'},
        {id:2, name:'zs2'},
        {id:3, name:'zs3'}
      ]
    }
  });
</script>
```
- v-for: 迭代显示列表(List)元素

  - 普通数组：`<p v-for="(item,index) in list">索引值是{{i}}，内容为{{item}}</p>`
  - 对象键值对：`<p v-for="(val,key,index) in user">键是{{key}}，内容为{{val}}</p>`

数组 (item,index) 第一个属性为内容；第二个属性为索引。
键值 (val,key,index) 第一个属性为内容；第二个属性为键名；第三个属性为索引。

```html
<!--对象数组-->
<tr :key='user.id' v-for='user in userList'>
  <td>{{user.id}}</td>
  <td>{{user.name}}</td>
</tr>
```

为方便管理元素，一般需要为每项绑定一个唯一的 key 属性：
`<p v-for="item in user" :key="item.id">用户的名字为{{item.name}}</p>`

可以用于循环固定次数：`<p v-for="count in 10">这是第{{count}}次循环</p>`

### 数组数据更新操作(API)

- push
- pop

以上操作直接对原有数组进行修改，页面也会随数据变化实时更新。

- filter

以上操作会产生新的数组，返回值需要重新赋值去更新页面。

`Vue.set(vm.list,1,'new data')` 或者 `Vm.$set(vm.list,1,'new data')` 

响应式修改数组元素

---


---

## vue 前后端交互

传统的原生 JS 开发和 jQuery 都使用 ajax 实现前后端交互：仍需要处理 dom 操作。

```
$.ajax({
  url:'http://localhost:8080',
  success:function:(data){
    ret = data;
    console.log(ret); // 打印读取数据
  }
})
console.log(ret); // 由于异步操作，此时打印数据尚未更新
```

### promise 对象

在 JavaScript 最新版本标准 ES6 中， 定义了 promise 对象获取异步操作的消息。

```js
function queryData(url){
  // 创建 promise 对象
  var p = new Promise(function(resolve, reject){
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function(){
      if(xhr.readyState != 4) return;
      if(xhr.readyState == 4 && xhr.status ==200){
        // 执行成功，执行 resolve
        resolve(xhr.responseText);
      }else{
        // 执行失败，执行 reject
        reject("服务器错误");
      }
    };
    xhr.open('get',url);
    xhr.send(null);
  });
  return p;
}
```

- resolve 函数： 将 promise 对象的状态标记为成功
- reject 函数：将 promise 对象的状态标记为失败

**发送请求并获取处理结果**

```js
queryData('http://localhost:8080').then(function(data){
  // 成功执行前者，返回数据为 data
  console.log(data);
},function(info){
  // 失败执行后者，返回数据为 info (可不含)
  console.log(info);
});
```

- p.then 获取异步正常执行结果
- p.catch 获取异常信息
- p.finally 无论正确与否都会触发

**请求嵌套**

```js
// 执行并通过 then 获取处理结果
queryData('http://localhost:8080').then(function(data){
  console.log(data);
  return queryData('http://localhost:8080/next1');
})
// 执行第二次调用的返回数据
.then(function(data){
  console.log(data);
});
```

单一的 Promise 链并不能发现 async/await 的优势，但是，如果需要处理由多个 Promise 组成的 then 链的时候，优势就能体现出来了（很有意思，Promise 通过 then 链来解决多层回调的问题，现在又用 async/await 来进一步优化它）。

**批量处理**

```js
var p1 = queryData('http://localhost:8080/data1');
var p2 = queryData('http://localhost:8080/data2');
var p3 = queryData('http://localhost:8080/data3');
...
Promise.all([p1,p2,p3]).then(
  //所有任务都执行完才能返回结果
);

Promise.race([p1,p2,p3]).then(
  //最先完成者就能返回结果
);
```

### axios 库

axios 是基于 promise 实现的 http 客户端。作为第三方库，比官方的 fetch 功能更为强大。

```html
<!--引入 axios -->
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>

<!--引入 qs , 一般用于处理提交数据-->
<script src="https://cdn.bootcss.com/qs/6.7.0/qs.min.js"></script>
```

**全局配置**

```js
axios.defaults.timeout = 3000; // 超时时间
axios.defaults.baseURL = "http://localhost:8080" // 默认地址
axios.defaults.headers['mytoken'] = 'asaffdf123' // 请求头
```

**GET / DELETE 请求**

```js
axios.get('/get',{
  params:{
    id:123
  }
})
.then(function(ret){
  console.log(ret.data)
}
```

**POST / PUT 请求**（参数以 json 形式传递）

```js
axios.post('/post',{
  uname:'tom',
  password:123456
})
.then(ret=>{
  console.log(ret.data)
}
```

对于响应结果 ret: 
- ret.data : 响应返回数据 【ret.data.item 返回数据中某一具体属性】
- ret.headers : 响应头信息
- ret.status : 响应状态码
- ret.statusText : 响应状态信息

使用 asyns/await 将 axios 异步请求同步化


```js
async getHistoryData (data) {
      try {
        let res = await axios.get('/api/survey/list/', {
          params: data
        })
        this.tableData = res.data.result
        this.totalData = res.data.count
      } catch (err) {
        console.log(err)
        alert('请求出错！')
      }
    }
```


表单自带校验方法 validate(callback){ 直接返回 Promise 对象}，默认 valid 为 true 通过。一旦错误直接


**拦截器**

对请求或者响应进行加工处理。

1. 对请求加工处理

```js
axios.intercepter.request.use(function(config){
  // 首个函数执行拦截修改功能
  config.headers.mytoken = 'nihao';
  return config;
},function(error){
  // 第二个函数 反馈错误信息
  console.log(error);
}
)
```

2. 对响应结果加工处理

```js
axios.intercepter.response.use(function(res){
  var data = res.data;
  return data;
},function(error){
  console.log(error);
}
)
```

---

## vue 路由

### 什么是路由

路由的作用：把用户远程请求映射到相应的网络资源。可采用以下两种方式：

- 后端路由：服务器根据用户请求 URL 返回 html 页面，浏览器直接显示。（频繁刷新界面）
- 前端路由：服务器根据用户请求 URL 返回 json 数据，浏览根据用户触发事件更新 html 页面。（无法记录历史访问）

现在主流开发使用基于前端路由的 SPA 技术：整个网站只有一个界面，使用 ajax 技术局部更新界面，同时支持浏览器界面的前进后退操作。

### vue router 插件

vue 深度集成了官方路由管理器 vue router。可选【使用用户操作历史或哈希存储历史访问】.

```html
<!--引入 vue router-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue-router.js"></script>
```

开发者在专用的路由 js 文件中定义路由规则。

```js
<script>
  const User = {...};
  const Register = {...};

  // 路由规则
  const router = new VueRouter({
    routes:[
      {path:'/',redirect:'/user'}, // 重定向
      {path:'/user',component:User},
      {path:'/register',component:Register}
    ]
  })

  new Vue({
    el:'#app',
    data:{},
    Router: router
  })
</script>
```

在 vue 组件中，点击 router-link 组件实现页面跳转，预留的 router-view 区域将显示指定组件。

```html
<div id="app">
  <!--路由链接-->
  <router-link to="/user">User</router-link>
  <router-link to="/register">Register</router-link>
  <!--路由占位符，显示位置-->
  <router-view></router-view>
</div>
```

### 嵌套路由

```js
<script>
  const Tab1 = {...};
  const Tab2 = {...};
  const Register = {
    template:'
      <div>
        <router-link to="/register/tab1">Tab1</router-link>
        <router-link to="/register/tab2">Tab2</router-link>
        <router-view />
      </div>
    '
  }

  const router = new VueRouter({
    routes:[
      {path:'/',redirect:'/user'}, // 重定向
      {path:'/user',component:User},
      {
        path:'/register',
        component:Register,
        children:[
          {path:'/register/tab1',component:Tab1},
          {path:'/register/tab2',component:Tab2}
        ]}
    ]
  })

  new Vue({
    el:'#app',
    data:{},
    Router: router
  })
</script>
```

### 动态路由

根据参数自动选择路由

```js
// 动态路径
var router = new VueRouter({
  routes:[
    {path:'/user/:id',component: User}
  ]
})

// 动态显示内容
const User = {
  template:'<div>User {{$route.params.id}}</div>'
}
```

但 $route 的方式传参高度耦合，一般使用 props 将组件和路由解耦。还可以对路由路径进行命名。

```js
var router = new VueRouter({
  routes:[
    {path:'/user/:id',
    name:'user',  // 路由命名
    component: User, 
    props:true}  // 动态路径
  ]
})

// 动态显示内容
const User = {
  props:['id'],
  template:'<div>User {{id}}</div>'
}
```

`<router-link :to="{name:'user',params:{id:3}}">User</router-link>`


### 路由语句执行

#### $route 查询路由信息

在 vue 组件中，可以通过 $route 查询当前路由的详细信息。在组件内，即 this.$route 。

对于路由 /list/type/11?favorite=yes 

{
  path:'/list/type/:id',
  name:'user',  // 路由命名
  component: User, 
  props:true
}
    
    
$route.path  （字符串）返回绝对路径  $route.path='/list/type/11'

$route.params （对象）动态路径键值对   $route.params.id == 11 

$route.query  （对象）查询参数键值对  $route.query.favorite == 'yes' 

$route.name   （对象）路径名，没有则为空。 $route.name == 'user'

$route.router    路由规则所属的路由器（以及其所属的组件）。
$route.matched    数组，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象。



#### $router 执行路由跳转

在 vue 组件中，可以通过调用全局路由对象 $router 查的方法实现页面跳转。

push 方法和 <router-link :to="..."> 等同，执行时跳转指定页面。

this.$router.push('home')                                                /home
this.$router.push({ path: 'home' })                                      /home
this.$router.push({ path: 'home', query: { plan: '123' }})               /home?plan=123（附带查询参数）
this.$router.push({ name: 'user', params: { id: 123 }})                  /list/type/123（根据命名跳转可以附带动态路径）



go 方法根据历史记录，跳转上一个或下一个页面。

this.$router.go(-1)                  返回之前的页面

replace 方法替换当前的页面，和 push 方法的不同在于不会历史记录（一般用于 404 页面）。

this.$router.replace('/')



---

### Element UI 组件库

$ 符 用来绑定事件

this.$refs.tree.getCheckedKeys());  

$refs代表一个引用：tree表示组件中某个元素（ref属性设为tree）,然后我们可以通过对象调用方法。

https://www.cnblogs.com/my466879168/p/12091439.html 局部修改css 样式

<style lang="scss">
@import '../../styles/custom-menu.scss';
  .menu-form .el-form-item__label {
      text-align: left!important;
      font-size: 20px!important;
      color: #000!important;
      font-weight: normal!important;
}
</style>








在属性前加冒号，表示此属性的值是变量或表达式，如果不加，就认为是字符串，所以抛错。!!!!!!!!

