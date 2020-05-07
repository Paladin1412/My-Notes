# Node.js

https://www.runoob.com/nodejs/nodejs-tutorial.html

### 什么是 Node.js

Node.js 是一个 JavaScript 运行环境，运行在服务端（后端）。用户不再需要后台动态编程语言，只使用 JS 也可以创建自己的后台服务。


官网：https://nodejs.org/zh-cn/


安装成功后主机可以直接执行 JS 代码：

1. 在控制台输入 `node` 并回车，进入 Node.js 运行环境：直接输入 JS 代码执行。
2. 在控制台输入 `node JS文件名` 并回车，执行该 JS 文件。

### 模块化开发

根据主流的模块化规范 ES6 定义：一个 JS 文件就是一个模块。

- CommonJS 规范：module.exports 导出接口，require 引入模块（输出是值的拷贝）
- ES6 标准：export 导出接口，import 引入模块（输出是值的引用）

<font size=2 color=brown>由于引擎尚不支持，我们在 node.js 中习惯使用 CommonJS 语法。
在 vue 中习惯使用 ES6 语法，但必须安装 babel 插件自动转码为 commonJS 语法执行。</font>

**CommonJS 规范**

```js
  var num = 1
  function test(x){ 
    console.log("输出数据为" + x); 
  }

  exports.num = num
  exports.test = userTest           // 可以写为 module.exports.test = useTest

  /*************************************************************************/

  var a = require('./a')            // 调用 a.useTest(a.num)   
```

**ES6 语法**

```js
  var num = 1
  function test(x){ 
    console.log("输出数据为" + x); 
  }

  export var num                  
  export function test               // 可以写为 export test

  // 默认导出接口
  export default {            
    num,
    test
  }     

  /*************************************************************************/

  import {num,test as useTest} from './a.js'   // 调用 useTest(num)

  // 读取 JS 文件全部导出
  import * as a from './a.js'                  // 调用 a.test(a.num)

  // 读取 JS 文件默认导出
  import a from './a.js'           
```


### 常用 Node.js 官方模块

Node.js 提供了允许直接导入的官方模块，查询网址：http://nodejs.cn/api/


- OS 模块：系统信息

```js
var os = require("os");

console.log('主机名' + os.hostname());
console.log('操作系统' + os.type());
console.log('系统平台' + os.platform());
console.log('内存总量' + os.totalmem() + '字节');
console.log('可用内存' + os.freemem() + '字节');
```

- Path 模块：路径信息

```js
var path = require("path");

var data = "c:/myapp/index.html";
console.log(path.basename(data));          // 输出 index.html
console.log(path.dirname(data));           // 输出 c:/myapp
console.log(path.basename(dirname(data))); // 输出 myapp
```

- URL 模块：URL 信息

```js
var url = require("url");

var data = "http://test.com?name=王东浩&age=22";
console.log(url.parse(data));             // 输出 网址解析信息
var urlQuery = url.parse(data, true);
console.log(urlQuery.query.name);         // 输出 王东浩
```

- fs 模块：文件信息

```js
var fs = require("fs");

fs.writeFile('./a.txt', '你好，我是王东浩', function(err){
  if(err){
    console.log(err);
    return;
  }
  console.log('success'); 
})

fs.readFile('./a.txt', 'utf8', function(err, data){
  if(err){
    console.log(err);
    return;
  }
  console.log(data); 
})
```

- http / https 模块：服务器

```js
var http = require("http");

var server = http.createserver();             // 创建服务器

server.on('request', function(req, res){      // 配置监听器，有请求则触发
  console.log('收到用户请求，请求地址' + req.url)
  console.log('请求方式' + req.method)
  
  if(req.url == '/'){ 
    $msg = "<a herf='www.baidu.com'>点击跳转</a>";
  }else{
    $msg = "404 Not Found";
  }
  res.setHeader('Content-Type','text/html,charset=utf8');
  res.write($msg);
  res.end;
})

server.listen(8080, function(){               // 启动服务器，监听端口
  console.log('服务启动成功')
})
```

###  Node.js 第三方模块

Node.js 通过 **包管理工具(npm)** 管理 Node.js 模块：

控制台输入`npm init -y` ：将当前目录初始化为项目【生成 package.json 配置文件】

控制台输入 `npm install 模块名` 下载安装第三方模块
控制台输入 `npm uninstall 模块名` 卸载第三方模块

- `-g` 全局安装，允许所有项目调用（默认本地安装）

- `--save` （默认）记录为生产用模块【保存到 package.json - dependencies】 
- `--save-dev` 记录为开发专用模块【保存到 package.json - devdependencies】

在开发环境，控制台输入 `npm install` 安装该项目记录的所有模块
在生产环境，控制台输入 `npm install --production` 安装该项目记录的生产用模块

**express 框架**

<font size =2 >在项目目录下，控制台输入 `npm install express` 安装 express 框架，进行 Node.js 项目开发。</font>


### 基于 Node.js 的 web 服务项目结构

- **node_module 文件夹**  / 本地下载的第三方模块
- **public 文件夹** / 静态资源(css、js、img)
- **view 文件夹** / 视图文件(view)
- **app.js** / 入口模块，调用 http 模块创建服务器
- **router.js** / （可选）分发模块，把任务分发给不同的业务处理单元

在项目目录下，控制台输入 `node app.js` 即可运行服务器

此外还含有配置文件，如 package.json
 
 ```json
"script":{
  "echo": "echo welcome",
  "test": "node ./mine_test.js"
}
 ```

- script：用户自定义命令。控制台输入 `npm run test` 即可执行

### webpack 打包工具

webpack 是基于 Node.js 的自动化构建工具，对整个项目要请求的静态资源进行合并、封装、压缩、和自动转换。大大加快了请求静态资源页面加载速度，也解决了代码的兼容性问题。
