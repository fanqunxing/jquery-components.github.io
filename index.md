# jquery-componets

## 1 介绍

### 1.1 什么是jquery-componets

jquery是一个操作dom的库，jquery有着无法做单页面和组件化开发的缺点。jquery-componets 是为了解决jquery无法组件化和无法构建SPA的工具库。

### 1.2 为什么选择jquery-componets

jquery-componets 拥有jquery的所有api，并在几乎无新增api的前提下完成了组件化，赋予了jquery新的生命。



## 2 安装使用

### 2.1 安装jquery-componets-loader

> jquery-componets-loader是解析.jq文件的webpack的loader

```shell
npm install jquery-componets-loader -D
```



### 2.2 安装jquery-componets

> jquery-componets 是jquery组件化的核心包，它提供了组件注册、解析、渲染、路由解析、事件分发等核心功能。

```
npm install jquery-componets -S
```



### 2.3 在webpack.config.js的rules中配置jquery-componets-loader


```javascript
module: {
  rules: [
    {
      test: /\.jq/i,
      loader: "jquery-componets-loader"
    }
  ],
}
```



### 2.4 HTML入口页面

```html
<body>
  <div id="app"></div>
</body>
```



### 2.5 编写App.jq

```html
<template>
  <div>
  	hello jquery-componets
  </div>
</template>
<script>
  export default function ($) {}
</script>
<style>
  * {
    margin: 0;
    padding: 0;
  }
</style>
```



### 2.6 在main.js中使用

```javascript
import $ from 'jquery';
import { useJquery } from 'jquery-componets';
import App from './App.jq';

useJquery($);
$("#app").html(App);
```



启动webpack后页面显示

```html
hello jquery-componets
```



## 3. 组件

### 3.1 组件创建

使用js创建一个组件，此方法不推荐。仅用于示例。

```js
// 声明一个组件
const comp = {
  template: '<div class="btn">我是一个组件, 点击我~</div>',
  ready($) {
    $(".btn").click(function() {
      alert('hello world!')
    });
  }
};
// 选择dom元素挂载组件
$(".my_components").html(comp);
```



使用jq-loader创建组件

simpCom.jq文件

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

引入和使用

使用import标签引入自定义组件，name是组件名，src是组件路径。这样在template中就可以使用

```html
<template>
  <div>
    <simpCom></simpCom>
  </div>
</template>

<import name="simpCom" src="../components/simpCom.jq"></import>
<script>
  export default function() {}
</script>
```



### 3.2 template的使用

注意：

1. template必须含义一个根标签。
2. 自定义组件的使用要使用双标签，单标签暂时不被支持。

```html
<template>
  <div>
    <div class="btn">我是一个组件, 点击我~</div>
    <simpCom></simpCom>
  </div>
</template>
```



### 3.3 script的使用

script默认导出一个函数，这个函数会在组件被挂载完成后执行。

默认函数中传入一个$参数，这个$做了作用域的限制，使用这个$仅仅能选中组件内的dom元素。

```html
<script>
  export default function($) {
    $(".btn").click(function() {
      alert('hello world!')
    });
  }
</script>
```



### 3.4 style的使用

style里面正常使用css即可，支持less等扩展语言。



### 3.5 全局组件

我们声明了全局组件则不再需要引入。

使用$.component(name, compontents)即可。

示例：

```js
import MyBtn from './MyBtn.jq';
$.component('MyBtn', MyBtn)
```



## 4 组件传参

### 4.1 父组件向子组件传参



场景一：使用attr同步传入

```html
// 父组件中传入name
<div>
  <MyBtn name="父子传参"></MyBtn>
</div>
```

在子组件MyBtn.jq中接受

```html
<template>
  <div class="btn">按钮</div>
</template>
<script>
  export default function ($) {
    var name = $.el.attr('name');
    console.log(name);
  }
</script>
```



场景二：如果是异步写入name，则需要下面方法

```html
<template>
  <div>
    <MyBtn class="btn"></MyBtn>
    <div class="click">触发</div>
  </div>
</template>
<script>
  export default function ($) {
    $(".click").click(function () {
      $(".btn").attr('name', '父子传参');
    });
  }
</script>
```

在子组件MyBtn.jq中使用监听到数据

```html
<template>
  <div class="btn">按钮</div>
</template>
<script>
  export default function ($) {
    $.on('name', function (res) {
      console.log(res);
    });
  }
</script>
```



场景三：如果数据量太大或者数据格式，不适合用attr，则可以使用data方法

```html
<template>
  <div>
    <MyBtn class="btn"></MyBtn>
    <div class="click">触发</div>
  </div>
</template>
<script>
  export default function ($) {
    $(".click").click(function () {
      $(".btn").data('name', { name: '父子传参' });
    });
  }
</script>
```

在子组件一样可以监听的到

```html
<template>
  <div class="btn">按钮</div>
</template>
<script>
  export default function ($) {
    $.on('name', function (res) {
      console.log(res);
    });
  }
</script>
```



### 4.2 子组件向父组件传参

子组件

```html
<template>
  <div class="btn">按钮</div>
</template>
<script>
  export default function ($) {
    $.el.click(function() {
      $.el.trigger('onMyclick', '父组件需要的参数')
    });
  }
</script>
```

父组件

```html
<template>
  <div>
    <MyBtn class="btn"></MyBtn>
  </div>
</template>
<script>
  export default function ($) {
    $('.btn').on('childClick', function (evt, param) {
      // param就是子组件传过来的参数
      console.log(param);
    });
  }
</script>
```



## 5 路由

### 5.1 使用路由

配置路由文件

```js
import Guide from '../pages/Guide.jq';
import Init from '../pages/Init.jq';

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

在main.js中使用路由

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
<script>
  export default function ($) {

  }
</script>
```



### 5.2 监听路由变化

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



## 6 动态组件

动态组件是指在组件内部使用html方法挂载一个复杂组件的时候使用

如下,  js_content里面含有MyBtn标签，则需要声明为动态组件，以保证Mybtn按预期渲染。

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



## 7 对jquery做了哪些改动

1. 重写了html方法，在支持原来的字符串html写入dom的条件下，也支持了写入jquery组件。
2. 新增了$.router方法挂载路由 (如果需要路由的话)。
3. 新增了$.component全局组件声明。
