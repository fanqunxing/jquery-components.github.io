# jquery-components

## 1 介绍

### 1.1 什么是jquery-components

jquery是一个操作dom的库，jquery无法同vue和react一样做单页面和组件化开发。jquery-components 正是为了解决这个问题。

### 1.2 为什么选择jquery-components

jquery-components 拥有jquery的所有api，几乎无新增api，赋予了jquery新的生命，使用jquery的同学可以快速上手。



## 2 安装依赖

### 2.1 依赖

jquery-components 是核心依赖，它用于在运行时起作用。

jquery-components-loader 是webpack用于解析`.jq`文件的loader。



### 2.2 jquery-components

jquery-components 是jquery组件化的核心包，它提供了组件注册、解析、渲染、路由解析、事件分发等核心功能。

```shell
npm install jquery-components -S
```



### 2.3  jquery-components-loader

jquery-components-loader是用于解析.jq文件的webpack的loader。

安装命令：

```shell
npm install jquery-components-loader -D
```



安装完后我们需要在webpack.config.js中配置它

```js
module: {
  rules: [
    {
      test: /\.jq/i,
      loader: "jquery-components-loader"
    }
  ],
}
```



## 3 第一个应用



demo代码在地址：https://github.com/fwx426328/jquery-components-demo



### 3.1 安装依赖

webpack依赖

```sh
npm install webpack webpack-cli webpack-dev-server html-webpack-plugin -D
```



jquery-components-loader

```shell
npm install jquery-components-loader  -D
```



babel和css-loader也需要

```shell
npm install @babel/core babel-loader css-loader  -D
```



jquery 和 jquery-components

```shell
npm install jquery jquery-components  -S
```



### 3.2 配置webpack.config.js

根目录新建webpack.config.js

配置如下

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  devtool: "source-map",
  mode: process.env.NODE_ENV,
  entry: './src/main.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  devServer: {
    compress: true,
    port: 8080,
    hot: true
  },
  module: {
    rules: [
      {
        test: /\.jq/i,
        loader: "jquery-components-loader"
      }
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './public/index.html'
    })
  ]
};

```



### 3.3 编写代码

新建public/index.html文件

```html
<!DOCTYPE html>
<html lang="en">
<body>
  <div id="app"></div>
</body>
</html>
```



新建src目录，目录下新建App.jq和main.js

App.jq

```html
<template>
  <div>hello jquery</div>
</template>
<script>
export default function ($) {
  
}
</script>
<style>
  div {
    color: red;
  }
</style>
```



main.js

```js
import $ from 'jquery';
import { useJquery } from 'jquery-components';
import App from './App.jq';

useJquery($);
$("#app").html(App);
```



在package.json中配置dev启动命令

```json
"scripts": {
   "dev": "webpack serve --hot --mode=development"
}
```



启动命令

```shell
npm run dev
```



最后我们可以看到界面出现红色的`hello jquery`



## 4 vscode插件

由于`.jq`文件vscode无法识别，在vscode插件中搜索`jquery-components For Highlighter`

安装插件即可识别`.jq`文件。 



## 5 生命周期

jquery-components 只有两个生命周期mounted和destroy



### 5.1 mounted

在`export default function($) {}`函数中的方法会在mounted的时候执行

```html
<template>
  <div class="btn">我是一个组件, 点击我~</div>
</template>
<script>
  export default function($) {
    $(".btn").click(function() {
      alert('hello world!')
    });
  }
</script>
```



### 5.2 destroy

destroy会在组件销毁的时候调用。

使用`$.on('destroy', () => {})`监听

```html
<template>
  <div class="btn">我是一个组件, 点击我~</div>
</template>
<script>
  export default function($) {
    $.on('destroy', () => {
      console.log('destroy')
    });
  }
</script>
```



## 6 事件绑定和监听

### 6.1 元素的事件监听

`default`的函数中默认传递了一个`$`，这个`$`是对`jquery`的封装和扩展，包含着作用域限制和其他扩展方法。

使用`$`即可对元素实现监听，同`jquery`的使用方法一样。

```html
<template>
  <div class="btn">我是一个组件, 点击我~</div>
</template>
<script>
  export default function($) {
    $(".btn").click(function() {
      alert('hello world!')
    });
  }
</script>
```

### 6.2 内置事件监听

`jquery-components`内置了几个系统事件，用于事件和生命周期的监听。都采用`$.on('name', () => {})`监听。

- router事件 用于监听路由
- destroy事件 用于监听destroy生命周期



## 7 组件

### 7.1 组件自定义

自定义组件是jquery-components的核心内容。

新建一个组件simpCom.jq

```html
<template>
  <div class="btn">我是一个组件, 点击我~</div>
</template>
<script>
  export default function($) {
    $(".btn").click(function() {
      alert('hello world!')
    });
  }
</script>
```



### 7.2 组件引入

jquery-components中是通过`<import>标签`引入组件，如下面代码示例

```html
<import name="simpCom" src="../components/simpCom.jq"></import>
```

`<import>标签`中的的`src`属性指定自定义组件的地址，`name`属性指定在父组件中引用该组件时使用的**标签名称**

示例如下：

```html
<import name="simpCom" src="../components/simpCom.jq"></import>

<template>
  <div>
    <simpCom></simpCom>
  </div>
</template>
```



### 7.3 全局组件注册

我们声明了全局组件则不再需要引入。

使用`$.component(name, compontents)`即可。

示例：

```js
import MyBtn from './MyBtn.jq';
$.component('MyBtn', MyBtn)
```



### 7.4 动态组件

动态组件是指在组件内部使用html方法挂载一个复杂组件的时候使用。

如下 `js_content` 里面含有`<MyBtn></MyBtn>`标签，则需要声明为动态组件，以保证`<MyBtn></MyBtn>`按预期渲染。

使用`html`方法动态加载。

```html
<template>
  <div>
    <div class="js_content"></div>
    <button class="click">保存</button>
  </div>
</template>
<script>
  import MyBtn from './MyBtn.jq';
  
  let data = [{ id: 0, name: '孙悟空', content: '打妖怪' }];
  
  export default function ($) {
    $(".click").click(render);

    function render(data) {
      let htmls = [];
      data.forEach(({name, content}) => {
        htmls.push(`
          <div>
            <span>${name}</span>
            <span>${content}</span>
            <span><MyBtn></MyBtn></span>
          <div>
        `);
      });
      
      $('.js_content').html({
        template: `<div>${htmls.join('')}</div>`,
        components: { MyBtn }
      })
    };
  }
</script>
```


### 7.5 插槽

插槽示例如下：
创建一个带插槽的组件

```html
<template>
  <div>
    <slot name="header"></slot>
    <slot></slot>
    <div>footer</div>
  </div>
</template>
```

在父组件中使用带插槽的组件
```html
<import name="SlotEg" src="./SlotEg.jq"></import>

<template>
  <SlotEg>
    <div> default content </div>
    <div slot="header"> header content </div>
  </SlotEg>
</template>
```


## 8 父子组件通信



### 8.1 父组件向子组件传参

**父组件通过 `attr`和`data`向子组件传递数据**



**场景一**：使用attr同步传入

```html
<import name="MyBtn" src="./MyBtn.jq"></import>

<!-- 父组件中传入name -->
<div>
  <MyBtn name="父子传参"></MyBtn>
</div>
```



```html
<!-- 在子组件MyBtn.jq中接受 -->
<template>
  <div>按钮</div>
</template>
<script>
  export default function ($) {
    const name = $.el.attr('name');
    console.log(name);
  }
</script>
```



**场景二**：如果是异步写入name，则需要下面方法

```html
<import name="MyBtn" src="./MyBtn.jq"></import>

<template>
  <div>
    <MyBtn class="my-btn"></MyBtn>
    <div class="click">触发</div>
  </div>
</template>
<script>
  export default function ($) {
    $(".click").click(function () {
      $(".my-btn").attr('name', '父子传参');
    });
  }
</script>
```

在子组件MyBtn.jq中使用监听到数据

```html
<template>
  <div>按钮</div>
</template>
<script>
  export default function ($) {
    $.on('name', function (res) {
      console.log(res);
    });
  }
</script>
```



**场景三**：如果数据量太大或者数据格式，不适合用attr，则可以使用data方法

```html
<import name="MyBtn" src="./MyBtn.jq"></import>

<template>
  <div>
    <MyBtn class="my-btn"></MyBtn>
    <div class="click">触发</div>
  </div>
</template>
<script>
  export default function ($) {
    $(".click").click(function () {
      $(".my-btn").data('name', { name: '父子传参' });
    });
  }
</script>
```

在子组件一样可以监听的到

```html
<template>
  <div>按钮</div>
</template>
<script>
  export default function ($) {
    $.on('name', function (res) {
      console.log(res);
    });
  }
</script>
```



### 8.2 子组件向父组件传参

**子组件通过 `trigger`向父组件传递数据，父组件使用自定事件监听**



子组件

```html
<template>
  <div class="btn">按钮</div>
</template>
<script>
  export default function ($) {
    $.el.click(function() {
      $.el.trigger('childClick', '父组件需要的参数')
    });
  }
</script>
```

父组件

```html
<import name="MyBtn" src="./MyBtn.jq"></import>

<template>
  <div>
    <MyBtn class="my-btn"></MyBtn>
  </div>
</template>
<script>
  export default function ($) {
    $('.my-btn').on('childClick', function (evt, param) {
      console.log(param);
    });
  }
</script>
```





## 9. less和scss的使用

在`<style></style>`标签中设置`lang`属性，使用后需要安装相应的loader

例如：

```html
<style lang="less">
  .app {
    display: flex;
    .app-content {
      flex: 1;
      padding: 20px;
    }
  }
</style>
```



## 10 路由

### 10.1 配置路由

配置路由文件如下

```js
import Guide from './Guide.jq';
import Init from './Init.jq';

export default [
  {
    path: '/guide',
    component: Guide
  },
  {
    path: '/init',
    component: Init
  }
];
```



### 10.2 注册和使用路由

在main.js中注册路由

```js
import routers from './router';

$.router(routers)
```



App.jq中就可以使用router-view标签, `<router-view></router-view>`就可以展示路由页面。

```html
<template>
  <div class="app">
    <a href="#/guide">guide</a>
    <a href="#/init">init</a>
    <router-view></router-view>
  </div>
</template>
```



### 10.3 监听路由变化

通过router事件即可监听

```html
<template>
  <div class="btn">按钮</div>
</template>
<script>
  export default function ($) {
    $.on('router', function(res) {
      console.log('router', res);
    });
  }
</script>
```



### 10.4 路由懒加载

为了让打包体积减少，可以使用路由懒加载

```js
const Guide = () => import('./Guide.jq');
const Init = () => import('./Init.jq');

export default [
  {
    path: '/guide',
    component: Guide
  },
  {
    path: '/init',
    component: Init
  }
];
```



### 10.5 子路由配置

子路由使用`children`配置

```js
const Guide = () => import('./Guide.jq');
const Init = () => import('./Init.jq');
const Hello = () => import('.Hello.jq');

export default [
  {
    path: '/guide',
    component: Guide
  },
  {
    path: '/init',
    component: Init,
    children: [
      {
        path: '/hello',
        component: Hello,
      }
    ]
  }
];
```



### 10.6 路由监听

通过router事件监听路由

```html
<template>
  <div></div>
</template>
<script>
  export default function ($) {
    $.on('router', function(res) {
      console.log('router', res);
    });
  }
</script>
```

