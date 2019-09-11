[TOC]

# 1. vue 介绍

## 1.1 vue 简介之前端现状

>**`目标`**: 了解Vue是什么 
>
>* Vue是一个优秀的**`前端框架`**
>* 开发者按照Vue的**`规范`**进行开发
>* 目前企业的技术栈中,Vue及Vue的语法规范占了**`半壁江山`** 
>* Vuejs 开发H5,微信小程序  Vue语法+ 一点react语法 + 一点自创语法 => 微信小程序
>* mpVue(美团) => Vue 85%  uni-app(多端应用) 90% Vue语法
>* 目前前端**`必备`**技能
>* 传统方式已经不能适应目前越来越**`复杂`**的开发需求
>* 前端发展**`日新月异`**
>* 一般新框架 不是以vue作为基础语言,就是用react

## 1.2 vue 的特点和能做什么

>**`目标`**了解Vue能做什么以及Vue的特点
>
>1. `数据`驱动**视图**  可以让我们只关注数据,完全**`解耦`**数据和**`视图`**  =>响应式数据 => 数据变化 => **`视图一定变化`**  
>  * document.getElementById("").innerText =""
>  * Vue将视图和数据进行绑定 => 改变数据 => 视图自动变化
>2. **`MVVM`** 双向绑定  => v-model => 数据变化 => 视图变化   视图变化 => 数据变化
>3. 通过**`指令`**增强了html功能 `新特性`
>4. 组件化 **`复用代码`**
>5. 开发者一般只需要关注数据
>6. 适应当前**`SPA`**的项目开发  single page application
>7. 传统网站开发 一般来说 需求不大
>8. 当下各种新框架都采用了**类Vue**或者**类React**的语法去作 为主语法, 微信小程序/MpVue/uni-app  
>
>**`结论`**掌握Vue的开发语法 相当于掌握了新的开发模式,可以适应目前绝大多数的技术环境

## 1.3 Vue学习的文档链接

>**`目标`** 知道如何查阅文档
>
>[Vue官方文档](https://cn.vuejs.org/)
>
>[Vue开源项目汇总](https://github.com/opendigg/awesome-github-vue)
>
>[Vue.js中文社区](https://www.vue-js.com/)
>
>* 所有关于Vue的问题都可以通过**`查阅文档`**解决
>

## 1.4 vue 三种安装方式

> **`目标`**: 了解采用几种方式安装vue
>
> 1. 采用本地文件引入的方式
> 2. 采用 **`在线cdn`**引入的方式
>
> - cdn相当于把一个文件放在了全国各地,然后你离哪里近,就从哪里调拨给你
>
> 1. 采用 **`npm`** 安装的方式 
> 2. 现状: 都会采用npm的方式来进行正式项目开发
>

# 2. vue 基本语法

## 2.1 vue 初体验

```html
<body>
	<!-- 1.设置vue的管理视图 -->
    <div id="app">
        <!-- 5.使用数据 -->
        {{ data }}
        {{ fn() }}
    </div>
    <!-- 2.引入vuejs -->
    <script src="./vue.js"></script>
    <script>
        // 3.实例化Vue对象
        var vm = new Vue({
            // 4. 设置vue实例的选项 将对象与视图发生关系
            el: '#app',
            data: {
                name: "helloWorld"
            },
            methods:{
                fn(){
                    return this.name;
                }
            }
        });
    </script>
</body>
```

## 2.2 实例选项

### 2.2.1 el

* 作用：当前Vue实例所管理的html视图
* 值：**`通常`**是id选择器(或者是一个 HTMLElement 实例)
* el所管理的视图**`不能是html或者body!`**
* 选择器 **只匹配第一个满足条件的元素** (**Vue实例 => 管理视图 1对1**)

### 2.2.2 data

**`数据驱动视图`** => 数据变化 => 视图一定变化  只需要关注数据

* Vue 实例的数据对象，是**`响应式数据`**(数据驱动视图) 数据变化 => 视图变化
* 可以通过 `vm.$data` 访问原始数据对象
* Vue 实例也代理了 data 对象上所有的属性，因此访问 `vm.a` 等价于访问 `vm.$data.a`
* 视图中绑定的数据必须**`显式`**的初始化到 data 中
* 数据对象的更新方式 直接 采用 **实例.属性 = 值**

### 2.2.3 methods

* methods是一个对象
* 可以直接通过 vm 实例访问这些方法，或者在**`插值表达式中使用`**。
* 方法中的 `this` 自动绑定为 Vue 实例。
* methods 中所有的方法 同样也被代理到了 Vue 实例对象上,都可通过this访问
* 注意，**不应该使用箭头函数来定义 method 函数** (例如 `plus: () => this.a++`)。理由是箭头函数绑定了**`父级作用域`**的上下文，所以 `this` 将不会按照期望指向 Vue 实例，`this.a` 将是 undefined

> vm. 调用到 data中所有的属性
> vm. 调用到methods中所有的方法
> **`data中命名不能和methods中的方法重名`**

# 3. 术语解释

## 3.1 插值表达式（重点）

> 作用：会将绑定的数据实时的显示出来:
>
> 形式： 通过 **`{{ 插值表达式 }}`**包裹的形式 
>
> 通过任何方式修改所绑定的数据，所显示的数据都会被实时替换(**响应式数据**)**数据驱动视图**
>
> 插值表达式 => **`为所欲为`**
>
> **`需要注意的是`**Vue实例上代理里 **`data`**中所有的属性 和 **`methods`**中的方法，而我们的el作用的视图直接可使用这些**`属性和方法`** 但是并不需要**`写this.属性 和 this.方法()`**
>
> **`注意`**：不能写 `var a = 10; 分支语句 循环语句`

## 3.2系统指令（重点）

> 指令 (Directives) 是带有 `v-` 前缀的特殊特性。 v- 相当于标识  angular的ng-  微信的 wx- 
>
> 指令特性的值预期是**`单个 JavaScript 表达式`**(`v-for` 是例外情况，稍后我们再讨论)。
>
> 指令的职责是，当表达式的值改变时，将其产生的连带影响，**`响应式`**地作用于 DOM。
>
> 指令位置:  **起始标签**

# 4. 系统指令 v-

## 4.1 v-text 和 v-html

很像**`innerText`**和**`innerHTML`**

- v-text：更新标签中的内容
- v-text和插值表达式的区别
  - v-text ：更新**`整个`**标签中的内容
  - 插值表达式： 更新标签中**`局部`**的内容
  - v-text 优先级大于 插值表达式 （同时使用显示v-text的值）
- v-html：更新标签中的内容/标签
  - 可以渲染内容中的HTML标签
  - 注意:尽量避免使用，容易造成危险 (XSS跨站脚本攻击)

```html
<body>
    <div id="app">
        <p v-text="text">{{ expression }}</p>  <!-- v-text优先级大于插值表达式 -->
        <p>测试:{{ expression }}:结尾</p>
        <p v-text="text">测试:结尾</p>
        <p v-text="content"></p>   <!-- v-text 不渲染标签 -->
        <p v-html="content"></p>   <!-- v-html 渲染标签 -->
    </div>
    <script src="./vue.js"></script>
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                text: "我是text的内容",
                expression: "我是表达式内容",
                content: "<span style='color:red;'>我是内容</span>"
            },
            methods: {}
        });
    </script>
</body>
```

## 4.2 v-if 和 v-show（条件渲染）

渲染指令的两种方式：v-if 和 v-show

- 场景:  需要**`根据条件`**决定 元素是否显示  使用以上指令
- 使用：v-if 和 v-show 后面的表达式返回的布尔值 来决定 该元素显示隐藏
- **注意**：v-if 是直接决定元素 的 添加 或者删除  而 v-show 只是根据样式来决定 显示隐藏

```html
<body>
    <div id="app">
        <div v-if="flag">成功</div>   <!-- flag为false时，该div不存在 -->
        <div v-else>失败</div> <!-- 配合上面的if使用 相当于 if else-->
        
        <div v-show="flag">成功</div> <!-- flag为false时，该div属性style="display: none;" -->
        <div v-show="!flag">失败</div>
    </div>
    <script src="./vue.js"></script>
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                flag: true
            },
            methods: {}
        });
    </script>
</body>
```

> v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。
>
> 因此，如果需要非常频繁地切换，则使用 `v-show` 较好；
>
> 如果在运行时条件很少改变，则使用 `v-if` 较好。
>
> 如果 切换频繁 v-if 开销更大  
>
> 需要**`注意`**的是 如果 有多个元素 需要用v-if或者v-show控制, 但是又不想增加 外部标签怎么办?
>
> 可以用 **`template`**标签来包裹多个元素 v-if 作用在 template 上,最终 template 不会形成任何元素

## 4.3 v-on（绑定事件）

- 场景：使用 v-on 指令给元素绑定事件

- 使用：绑定 **`v-on:事件名.修饰符="方法名"`**   简写： **`@事件名="方法名"`**

- **注意** 方法中可以采用 **`$event`** 的方式传形参  也可以直接写事件名 默认第一个参数为event事件参数

- 事件修饰符(可不写)
  - `.once` - 只触发一次回调。
  - `.prevent` -取消默认行为  相当于调用 `event.preventDefault()`。
  - `.stop` -阻止默认行为  相当于调用 `event.stopPropagation()`。

  按键修饰符

  * @keyup.13       13键盘弹时触发事件
  * @keyup.enter    上下的等价

## 4.4 v-for（循环遍历）

- 根据一组数组或一个对象的选项列表进行渲染。

- `v-for` 指令需要使用 `item in items` 或者 `item of items` 形式的特殊语法，

- `items` 是源数据数组 or 对象

- 注意：v-fo r写的位置 应该是重复的标签上，不是其父级元素上 需要注意

- > - 数组变异方法 通过以下方法改变数组 页面数据会从新渲染
  > - `push()`
  > - `pop()`
  > - `shift()`
  > - `unshift()`
  > - `splice()`
  > - `sort()`
  > - `reverse()`

```html
<body>
    <div id="app">
        <ul>
            <!-- 遍历数组   item为当前遍历属性数组项的值   index为数组的索引-->  
            <li v-for="(item, index) in arr" :key=‘index’>{{item + '--' + index}}</li> 
        </ul>
        <ol>
            <!-- 遍历对象   item为当前遍历属性对象的值  key为当前属性名的值   index为当前索引的值-->
            <li v-for="(item, key, index) in obj" :key='index'>{{key + '--' + item + '--' + index}}</li>
        </ol>
    </div>
    <script src="./vue.js"></script>
    <script>
        var vm = new Vue({
            el: "#app",
            data: {
                arr: ['北京', '天津', '上海', '重庆'],
                obj: {
                    name: '张三',
                    age: 18,
                    sex: '男'
                }
            },
            methods: {}
        });
    </script>
</body>
```

> 如果 v-for 中同样有多个元素,但是不想增加额外标签,同样可以用**`template`**
>
>  v-for循环中给循环项赋值key
>
> - 场景:列表数据变动会导致 视图列表重新更新 为了 提升性能 方便更新 需要提供 一个属性 key
> - 使用: 通常是给列表数据中的唯一值 也可以用索引值
>
> <!-- v-for 
>        key属性: 值通常是一个唯一的标识
>        key是一个可选属性
>        养成好习惯：建议在写v-for时 设置:key="唯一值"
> -->

## 4.5 v-if 与 v-for 同时使用

> **`目标`**: 了解v-if 和v-for的层级关系及使用
>
> ```html
> <p v-if="index > 1" v-for="(item,index) in list" :key='index'></p>
> ```
>
> 以上代码执行: 会将数组中前两个元素忽略掉
>
> 说明一个问题: v-for 的优先级大于 v-if  ,所有 v-if 才能使用 v-for 的变量
>
> 官网说明：
>
> **不推荐**同时使用 `v-if` 和 `v-for`
>
> 当 `v-if` 与 `v-for` 一起使用时，`v-for` 具有比 `v-if` 更高的优先级。

## 4.6 v-bind（绑定属性）

* 作用：绑定标签上的任何属性
* 场景：当标签上的属性是变量、动态、需要改变的
* 语法: ` <p v-bind:属性="数据对象中的属性名"></p>  简写v-bind可以省略`

### 基本语法

```html
<body>
    <div id="app">
        <p v-bind:id="id">{{name}}</p>
        <p :class="className">{{name}}</p>   <!-- 简写可以省略 v-bind -->
    </div>
    <script src="./vue.js"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                id: 'id',
                className: 'showClass',
                name: 'zs'
            },
            methods: {}
        });
    </script>
</body>
```

### 绑定 class 

**`对象语法`**

> - 绑定class对象语法    :class="{ class名称": 布尔值 }"    布尔值为true的时候class中存在，false时不存在
>
> ```html
> <p :class="{left:showLeftClass,righet:showLeftClass}" class="default">内容</p>
> <p :class="{left,righet}" class="default">内容</p><!-- 要是class名和data中名相同可以简写 -->
> ```
>
> **注意**: 绑定class和原生class会进行合并

**`数组语法`**

> 绑定class数组语法 :class="[class变量1,class变量2..]"
>
> ```html
> <p :class="[activeClass,selectClass]" class="default">内容</p>
> ```

### 绑定 style

**`对象语法`**

> 语法: :style="{css属性名: 变量}"
>
> ```html
> <p :style="{fontSize:fontsize}">{{name}}</p>
> <p :style="{fontSize:fontsize,color:color}">{{name}}</p>
> ```
>
> **注意** css属性名 例如 font-size要写成 fontSize  以此类推
>
> 但是 后面的样式会顶替到前面的样式 因为合并时 **`原生样式在前`**

**`数组语法`**

> - 语法:  :style="[对象1,对象2...]"
>
> ```html
> <p :style="[{fontSize:fontsize},{color:color}]">{{name}}</p>
> <p :style="{fontSize:fontsize,color:color}">{{name}}</p>  <!-- 等价 -->
> ```
>
> **注意** 对象可以是多个属性的 集合  同样里面的css属性需要遵从小驼峰命名的规则

## 4.7 v-model（数据双向绑定）

**`MVVM是Model-View-ViewModel的简写。`**

> **作用:** **`表单元素`**的绑定
>
> 特点: **双向数据绑定**
>
> - 数据发生变化可以更新到界面 => 响应式数据
> - 通过界面可以更改数据  => 表单数据变化 => ViewModel => 数据
> - ` v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` 特性的初始值而总是将 Vue 实例的数据作为数据来源。应该在 `data`选项中声明初始值。

### 基本语法

```html
<body>
    <div id="app">
        <p>{{name}}</p>
        <input type="text" v-model="name"> <!-- 双向绑定  页面改变值改变 值改变数据改变 -->
    </div>
    <script src="./vue.js"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                name: "张三",
            },
            methods: {}
        });
    </script>
</body>
```

### v-model 实现原理

* 数据改变 => 页面数据变化

* 表单元素数据改变 => 数据发生改变
* **`v-on:input`**与 **`v-bind:value`**

```html
<body>
    <div id="app">
        <p>{{name}}</p>
        <input type="text" v-model="name">
        <input type="text" v-on:input="changName" v-bind:value="name"> <!-- 实现原理-->
        <input type="text" @input="name = $event.target.value" :value="name"> <!-- 简写 -->
    </div>
    <script src="./vue.js"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                name: "张三",
            },
            methods: {
                changName(event) {   //表单改变则数据改变方法
                    this.name = event.target.value;
                }
            }
        });
    </script>
</body>
```

### 绑定其他表单元素

> 表单元素:  input  textarea checkbox radio  select 
>
> **注意**  checkbox在input标签中需要给定value值
>
> 所有表单元素一旦绑定了 v-model  就会忽略掉 原有的value值 checked值 selected值  需要从数据对象中取默认值

```html
<div id="app">
    <!-- input -->
    <p>{{name}}</p>
    <input type="text" v-model="name">
    <!-- textarea -->
    <p>{{textAreaName}}</p>
    <textarea v-model="textAreaName"></textarea>
    <!-- radio -->
    <p>{{radioName}}</p>
    <input type="radio" value="1" v-model="radioName">男
    <input type="radio" value="0" v-model="radioName">女
    <!-- checkbox 一个 -->
    <p>{{checkBoxName}}</p>
    <input type="checkbox" v-model="checkBoxName">是否同意
    <!-- checkbox 多个 -->
    <p>{{checkBoxsName}}</p>
    <input type="checkbox" v-model="checkBoxsName" value="bj">北京
    <input type="checkbox" v-model="checkBoxsName" value="sh">上海
    <input type="checkbox" v-model="checkBoxsName" value="tj">天津
    <input type="checkbox" v-model="checkBoxsName" value="cq">重庆
    <!-- select -->
    <p>{{selectName}}</p>
    <select v-model='selectName'>
        <option value="al">阿里</option>
        <option value="tx">腾讯</option>
        <option value="bd">百度</option>
    </select>
</div>
<!--
    data: {
        name: "张三",
        textAreaName: "文本域内容",
        radioName: "0",
        checkBoxName: false,
        checkBoxsName: ['tj'],
        selectName: "bd"
    }
-->
```

## 4.8 v-cloak（解决页面闪烁）

> - 场景: 解决页面初次渲染时 页面模板闪屏现象
>
> ​       1.  编写元素标签
>
> ​        2.  写入v-cloak指令
>
> ​        3.  将v-cloak指令 属性加上style
>
> **注意**  可以一次性 将v-cloak引用在实例视图上  避免多次写入标签
>
> ```html
> <div v-cloak id="app">
> 	<p>{{ name }}</p>
>     <p>{{ name }}</p>
> </div>
> ```
>
> ```css
> [v-cloak] {  /* 用属性选择器把所有元素都隐藏 等加载完系统会把带有v-cloak属性的元素全部取消隐藏 */
>   display: none;
> }
> ```

## 4.9 v-once（只渲染一次）

> - 作用：使得所在元素只渲染一次  
> - 场景：静态化数据 
>
> ```html
> <div id="app">
> 	<p v-once>{{ name }}</p>   <!-- 初始化的时候渲染一次 以后不会再随着数据改变而改变 -->
> </div>
> ```

## 4.10. ref 操作 DOM

> - 作用: **`通过 ref 特性可以获取元素的dom对象`**
> - 使用:  给元素定义 ref属性, 然后通过$refs.名称 来获取dom对象
> - **`$refs`**是vue实例的属性 
> - $data / $event => $开头的属性和方法都是Vue实例的方法和属性

```html
<div id="app">
    <!-- 定义 ref -->
    <input type="text" ref="myInput">
</div>
<script src="./vue.js"></script>
<script>
    var vm = new Vue({
        el: '#app',
        data: {},
        methods: {}
    });
    // 获取 DOM对象 并赋值value
    vm.$refs.myInput.value = "张三";
</script>
```

## 4.11 directive 自定义指令

> - 使用场景：需要对普通 DOM 元素进行操作，这时候就会用到自定义指令 
> - 基于Vue.directive自定义的指令，**`内部字母不能大写`**
> - 分类：**`全局注册`**和**`局部注册`**
>
> ```js
> // 注册一个全局自定义指令 `v-focus`  在实例化之前全局注册
> Vue.directive('mydirective', {
>   // 当被绑定的元素插入到 DOM 中时……
>   inserted: function (el) {
>     // el 当前元素
>   }
> })
> //###############################################
> //组件中注册一个局部自定指令   在实例化内部属性与data/methods同级上注册
> directives: {
>   mydirective: {
>     // 指令的定义
>     inserted: function (el) {
>       // el 当前元素
>     }
>   }
> }
> ```
>
> **简写**
>
> ```js
> // 全局自定义指令
> Vue.directive('mytive', (el, binding) => {
>   //el, binding, vnode, oldVnode  钩子函数的四个参数
>   console.log(el, binding);
> })
> 
> //局部自定义属性
>  directives: {
>      mytive(el, binding) {
>          console.log(el, binding);
>      }
>  }
> ```

# 5. 过滤器 filter

> - 场景：data中的数据格式(日期格式/货币格式/大小写等)需要处理时
> - 使用位置：{{}} 和 v-bind="表达式 | 过滤器名称"
> - 具体用法：{{msg | 过滤器名字}}  **`中间用管道`**
> - 分类：**`全局`**和**`本地(局部)`**
> - 本地：通过el/data/methods**`选项`**filters
> - 局部和全局的区别 => 注册位置不同，应用范围不同
> - 全局：在new Vue上面 Vue.filter()  => **`所有实例都可以使用`**
> - 局部：在Vue实例上 的选项上 filters => 所有过滤器集合 =>**`当前实例使用`**

## 5.1 全局过滤器

>1. 在创建 Vue 实例**`之前`**定义全局过滤器Vue.filter()
>2. Vue.filter('该过滤器的名字',(要过滤的数据)=>{return 对数据的处理结果});
>3. 在视图中通过{{数据 | 过滤器的名字}}或者v-bind使用过滤器

```html
<div id="app">
    	<!-- 3 -->
        <p>{{name | toUpper }}</p>
        <input type="text" v-model="name">
</div>
<script src="./vue.js"></script>
<script>
    // # 1  2
    Vue.filter("toUpper", function (value) {
        return value.toUpperCase();
    });
    
    var vm = new Vue({
        el: '#app',
        data: {
            name: "zs"
        },
        methods: {}
    });
</script>
```

## 5.2 局部过滤器

> 1. 在vm对象的选项中配置过滤器filters:{}
> 2. (key)过滤器的名字: (value)(要过滤的数据)=>{return 过滤的结果}
> 3. 在视图中使用过滤器:  {{被过滤的数据 | 过滤器的名字}}

```html
<div id="app">
        <p>{{name | toUpper}}</p>
        <input type="text" v-model="name">
    </div>
    <script src="./vue.js"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                name: 'zs'
            },
            methods: {},
            //与全局过滤器的差别就在于过滤器定义的位子不同 导致作用的范围不同
            filters: {
                toUpper(value) {
                    return value.toUpperCase();
                }
            }
        });
    </script>
```

## 5.3 传参数和串联使用

> - 过滤器可以传递参数,**`第一个参数永远是前面传递过来的过滤值`**
>
> ```js
> // <p>{{ value | toUpper(2)}}</p>
> // 根据传入的索引找到对应的字母换成大写字母
> // index为传入的参数 
> toUpper(value, index) {
>    return value
>      .split("")
>      .map(function(item, i) {
>        if (i === index) {
>          return item.toUpperCase();
>        }
>        return item;
>      })
>      .join("");
>  }
> }
> ```

> 过滤器也可以多个串行起来并排使用，每一个过滤器都是使用前一个表达式的最后返回值进行过滤
>
> ```html
> <p>{{ value | toUpper(2) | reverse }}</p>    // 语法 多个过滤器用 | (管道符)分割
> ```

