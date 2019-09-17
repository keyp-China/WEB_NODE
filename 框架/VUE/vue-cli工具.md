# vue-cli 工具介绍

> - 介绍：vue-cli是一个**`辅助开发工具`**=> **`代码编译`** + **`样式`** + **`语法校验`** + **`输出设置`** + 其他 ...
> - 中间过程  =>  转化过程(Vuejs/脚手架) => 浏览器可识别 支持
> - 作用：可以为开发者提供一个**`标准`**的**`项目开发结构`** 和配置  **开发者**不需要再关注
> - vue-cli 一个**`命令行`**工具，最新版本也支持图形化操作，可快速搭建大型网页应用
> - 使用vue-cli =>  综合性服务 => nodejs开发的=> 五花八门=> 开发期间 暂时先不关注

# vue-cli 安装

> 说明：vue-cli本质上是一个npm包,也需要通过npm去安装下载
>
> ```bash
> cnpm i -g @vue/cli  ## 全局安装脚手架  默认安装的最新版本 3.0+
> ```
>
> vue-cli 的命令行 关键字 是**`vue`**
>
> ```bash
> ## 查看脚手架版本号
> vue --version
> vue -V 
> ```
>
> 执行以下命令就可以 2.0 和 3.0 兼得
>
> 2.0 和 3.0 **`创建项目`**的命令不同的 
>
> ```bash
> cnpm install -g @vue/cli-init  # 安装桥接工具 将 2.0+ 的功能补齐到目前的脚手架上
> ```

# vue-cli 创建项目

## 1. vue-cli 2.0+ 

```bash
vue init <template-name> <project-name>  ## 创建2.0版本模板
 --templates include:['webpack','webpack-simple','browserify','browserify-simple','pwa','simple']
```

```bash
#  heroes 创建的项目名称
vue  init webpack-simple heroes   ## webpack-simple 为模板名称 固定写法
# 切换到当前目录
cd  heroes
# 引入依赖的包
npm install
# 在开发模式下 启动运行项目
npm run dev
```

## 2. vue-cli 3.0+

创建项目: 采用 cli 3.0 特性 (两种 默认 /选填)

```bash 
# 3.0下创建项目
$ vue create heroes // create(创建) 为关键字
# 切换到当前目录
$ cd  heroes 
# 在开发模式下 启动运行项目
$ npm run serve
```

**注意** 3.0+ 创建项目时  有两种模式, 一种默认模式, 一种选择模式,

默认模式:一种标准的模板

选择模式 可以根据自己的需求选择需要的工具和模式

# vue-cli 项目目录解释

> **`目标`**对2.0项目目录生成的模板文件进行识别认识
>
> node_modules  =>  存放包依赖文件 (不能提交)
>
> .bablelrc  =>  存放 babel编译的配置信息 => ES7/ES6 => ES5 => ES3
>
> .editorconfig  =>  存放编辑器的配置信息
>
> .gitignore  =>  git忽略文件  =>  忽略**`不需要提交`**的文件
>
> index.html  =>  单页应用的html
>
> package.json  =>  用于存放依赖信息 及 其他项目信息 => dependencies/devDependencies
>
> - dependencies  运行时依赖  => 项目上线之后依然再用 => axios /vuejs
> - devDependencies 开发时依赖  =>开发时 需要用的=> vue-cli =>  上线时不再需要 =>脚手架
>
> README.md  =>  项目介绍信息 github上的页面信息
>
> webpack.config.js  =>  webpack工具的配置文件  =>  webpack是一个前端工程化的工具

# 回顾-ES6模块的导入和导出

**`目标`**回顾ES6的导入导出

- require  => nodeJs 语法 
- import/from => es6语法 => react/angular/vue
- 正常开发过程中  一个文件就是一个组件 => **`拆解任务`**
- 组件嵌套 => 父组件 => 子组件  =>引入子组件 =>import  别名 from 路径(第三方 可以直接写 名称 package.json中的名称) => import 导入的前提是 ?=> 必须导出  export default 导出对象 { }
- var obj = { }    =>  export default  {}      import 别名  from  路径

> **ES6**提供**import**   别名   **from**  路径(包名)   语法 来引入 组件   引入
>
> 提供 **export**  **default**  {     } 语法来导出组件  导出
>
> 上面的代码 换成import 

如果想引入组件,该组件必须导出 =>  *.vue 文件默认做了导出 

要有导入,必须要有导出

> ```js
> export default vue //导出对象 vue
> ```
>
> ```js
> import vue from 'vue'
> ```
>
> 扩展--- 
>
> ```js
> export const function  fn1() {} // 方法1
> export const function  fn2() {} // 方法2
> export const function  fn3() {} // 方法3
> ```
>
> ```js 
> import { fn1,fn2, fn3 } from '文件'
> ```

# Vue-单文件组件及入口解析

> - .vue文件就是一个组件
>
>   ```js
>   var obj = {
>       template:``,
>       data(){return{}}
>   } 
>   ```
>
> - **`template/script/style  =>   html+js+css`** => 将原来的template提成了一个节点 => 写代码有提示.结构更清晰 => 开发更有效率
> - script  =>  导出一个组件对象 =>  export default  {   data () { return {} }   }
> - style =>  样式 => 编写该组件的样式
>
> **`注意`**Vue 选项中的 **`render`** 函数若存在，则 Vue 构造函数不会从 **`template`** 选项或通过 `el` 选项指定的挂载元素中提取出的 HTML 模板编译渲染函数。
>
> 介绍: 在cli开发模式下, 一个*.vue文件就是**`一个组件`**
>
> - **`template`** 组件的页面结构 代表它的 html 结构 
> - - 必须在里面放置一个 html 标签来包裹所有的代码 
> - 我们在其他地方写好了一个组件，然后就可以在当前template中引入
> - **`script`**  组件的逻辑结构及数据对象
>
> ```js
> export default {
>     // 这里写你的代码,如
>     data:,
>     props:
>     // 省略
> };
> ```

# 项目示例2.0版本

## 初始化项目

vue 2.0+ 

```bash
vue init webpack-simple heroes   # 创建项目脚手架
cd ./heroes        # 进入项目目录
npm install        # 导入依赖包
npm run dev        # 热启动项目
```

## 导入素材处理样式

```bash
npm i  bootstrap@3.3.7 --save # 安装 bootstrap 固定版本
```

在App.vue中引入样式文件

```js
import "./../node_modules/bootstrap/dist/css/bootstrap.css";  // 引入 bootstrap.css
import "./assets/index.css";  // 引入 index.css
```

重启运行,发现bootstrap.css文件 运行报错 

根据错误 需要在webpack.config.js增加对不识别文件的处理

```js
//  module ==>  rules 下
{
    test: /.(ttf|woff2|woff|eot)$/,
        loader: "file-loader",
        options: {
            name: "[name].[ext]?[hash]"
        }
},
```

## 提取公共组件-头部-侧边栏-列表,并预览效果

> 1. 新建vue文件
> 2. 拷贝html静态内容到 template中
> 3. 在app.vue中引入注册组件
> 4. 注册在app.vue的组件中 
> 5. 在app.vue的模板中使用注册组件 

## 提取路由模块

> **1.安装 vue-router**
>
> ```bash
> npm i vue-router  # 安装路由模块
> ```
>
> **2.新建一个 router.js 文件在main.js中引用 新建是了让main.js看起来更整洁**
>
> ```js
> //########## main.js
> import Vue from 'vue'
> import App from './App.vue'
> import router from "./router"  // 导入路由对象
> 
> new Vue({
> el: '#app',
> render: h => h(App),
> // 挂载路由对象
> router
> })
> ```
>
> **3.router.js 中配置路由**
>
> ```js
> import Vue from 'vue' // 引入vue
> import VueRouter from 'vue-router' // 1.引用router
> Vue.use(VueRouter) // 2. 使用router
> 
> // 引入组件对象
> import HeroList from "./Hero-list";
> import WeaponList from "./Weapon-list";
> import EquipList from "./Equip-list";
> 
> // 3. 实例化 router  5. 导出路由对象
> export default new VueRouter({
> linkActiveClass: "active", // 设置router-link的class  默认router-link-exact-active router-link-active
> // 4. 配置路由表
> routes: [
>    { path: "/", redirect: "/hero" },
>    { path: "/hero", component: HeroList },
>    { path: "/weapon", component: WeaponList },
>    { path: "/equip", component: EquipList },
> ]
> })
> ```
>
> 4.在App-sidebar.vue中使用
>
> ```vue
> <ul class="nav nav-sidebar">
> <router-link tag="li" to="/hero">
>    <a href="#">英雄列表</a>
> </router-link>
> <router-link tag="li" to="/equip">
>    <a href="#">装备列表</a>
> </router-link>
> <router-link tag="li" to="/weapon">
>    <a href="#">武器列表</a>
> </router-link>
> </ul>
> ```
>
> 5.在App.vue中渲染组件
>
> ```vue
> <template>
> <div id="app">
> <app-header></app-header>
> <app-sidebar></app-sidebar>
> <!-- 渲染路由返回的组件 -->
> <router-view class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main"></router-view>
> </div>
> </template>
> ```

## json-server-启动接口服务器

> 新建一个db.json文件 在里面存储分别存储hero/weapon/equip数据表数据
>
> ```bash
> # json-server 命令      npm i -g json-server // 安装json-server 
> json-server --watch db.json 
> ```

## hero列表渲染

> 1.安装axios 插件 
>
> ```bash
> npm i axios # 安装axios插件
> ```
>
> 2.在英雄列表页面
>
> ```js
> import axios from 'axios' // 引入axios
> 
> data 中定义数组
> 查询出来的数据赋值给data中的数组
> data中的数组在页面中遍历
> 
> 给删除/添加/修改分别注册点击事件
> ```
>
> 

## 优化-axios统一导入

```js
Vue.prototype.$http = Axios; //所有的实例都直接共享拥有了 这个方法
```

## 优化-设置baseUrl

```js
Axios.defaults.baseURL = "http://localhost:3000"; // 设置共享的方法  "heroes"  以后写axios请求可以省略前面部分了 
```

## 优化-路由-统一设置激活样式

```js
linkActiveClass: "active", // active为bootstrap中的 一个class样式  设置路由link样式  tag="标签名" tag默认是a
```

## 优化-切换路由增加过渡效果 (扩展)

```html
<transition name="slide">
	<router-view></router-view>
</transition>
```

```css
.slide-enter,
.slide-leave-to {
	opacity: 0;
}
.slide-enter-to,
.slide-leave {
	opacity: 1;
}
.slide-enter-active {
	transition: all 1s;
}
```

