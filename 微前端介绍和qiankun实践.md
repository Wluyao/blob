## 微前端介绍
### 概念 
微前端是一种类似于微服务的架构，它将微服务的理念应用于客户端。将前端应用由单一的单体应用转变为多个小型应用聚合为一的应用。
各个前端应用可以独立开发、独立部署。同时，它们之间也可以进行组件/模块的共享。
实现了项目级别的模块化，把不同项目变成了一个个模块来拼装组合，模块化从项目内提升到了项目本身。

**_微前端不是单纯的前端框架或者工具，而是一套架构体系。_**
 
### 几种方案的对比
_**如果不考虑体验问题，iframe几乎是最完美的微前端解决方案。**_
iframe 最大的特性就是提供了浏览器原生的硬隔离方案。在iframe方案中，父应用单独是一个页面，每个子应用嵌套一个 iframe，父子通信可采用 postMessage 的方式，实现简单，子应用之间自带沙箱，天然隔离，互不影响	iframe 的样式显示。

#### iframe存在的问题

- url 不同步。浏览器刷新 iframe url 状态丢失、后退前进按钮无法使用。
- 每次子应用进入都是一次浏览器上下文重建、资源重新加载的过程。(src加载外部资源)
- 全局上下文完全隔离，内部变量不共享。
- UI 不同步，DOM 结构不共享。假如要求在iframe中的页面拉起的一个模态窗在整个页面水平垂直居中就做不到。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1631090057378-d4ae3ef9-78c0-4c96-b4a5-456d98a4078d.png#averageHue=%23f1f1f1&clientId=ua8637087-629d-4&from=paste&height=234&id=vISt2&originHeight=291&originWidth=547&originalType=binary&ratio=1&rotation=0&showTitle=false&size=6805&status=done&style=none&taskId=u2fcb0144-2daa-4c53-957d-bedad509b37&title=&width=439)
[native-ui](https://www.naiveui.com/zh-CN/os-theme)

#### 特点比较
iframe 式：子工程可以使用不同技术栈；子工程之间完全独立，无任何依赖；基座工程和子工程需要建立通信机制；无单页应用体验；路由地址管理困难。
通用中心路由基座式：子工程可以使用不同技术栈；子工程之间完全独立，无任何依赖；统一由基座工程进行管理，按照 DOM 节点的注册、挂载、卸载来完成。
特定中心路由基座式：子业务线之间使用相同技术栈；基座工程和子工程可以单独开发单独部署；子工程有能力复用基座工程的公共基建。

| 方案 | 技术栈是否能统一 | 单独打包 | 单独部署 | 打包部署速度 | 单页应用体验 | 子工程切换速度 | 工程间通信难度 | 现有工程侵入性 | 学习成本 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **iframe 式** | 是（不强制） | 是 | 是 | 正常 | 否 | 慢 | 高 | 高 | 低 |
| **通用中心路由基座式** | 是（不强制） | 是 | 是 | 正常 | 是 | 慢 | 高 | 高 | 高 |
| **特定中心路由基座式** | 是（强制） | 是 | 是 | 快 | 是 | 快 | 正常 | 低 | 低 |


### 目标
一个微前端架构应该要具备以下这些功能：
![1631638177483.jpeg](https://cdn.nlark.com/yuque/0/2021/jpeg/1561110/1631782478747-3edecc66-6c6f-4ca3-8354-e7f0a104a4fd.jpeg#averageHue=%23f4f4f4&clientId=u701e6407-9491-4&from=paste&height=873&id=u22f32531&originHeight=873&originWidth=1306&originalType=binary&ratio=1&rotation=0&showTitle=false&size=79437&status=done&style=none&taskId=u69e0f89d-ddad-4a54-9eae-82daf259768&title=&width=1306)

### 使用场景
#### 适用场景
**超级应用**
一个非常庞大的系统拆分给不同部门、不同团队去开发。
使用qiankun搭建一个基座(主应用)，不同团队开发的项目作为子应用在基座中进行整合：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1631088081880-96c143f6-ce87-4cb0-9a52-81cbd89e71da.png#averageHue=%23fcfcfc&clientId=udb5706bc-3e02-4&from=paste&height=273&id=Xhe0a&originHeight=273&originWidth=730&originalType=binary&ratio=1&rotation=0&showTitle=false&size=17116&status=done&style=none&taskId=u99132335-470e-4247-9c31-d695beea6ef&title=&width=730)

**模块共享**
相同的模块需要在多个应用中使用。
在每个需要使用通用模块的应用中安装qiankun，配置需要接入的子应用。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1631088271735-8151037d-9a8e-4ee5-92b3-ec4f967cd829.png#averageHue=%23f9faf7&clientId=udb5706bc-3e02-4&from=paste&height=201&id=vhwU4&originHeight=201&originWidth=579&originalType=binary&ratio=1&rotation=0&showTitle=false&size=13265&status=done&style=none&taskId=u6339027a-7af6-460f-9526-2b1e005a44b&title=&width=579)

**历史项目**
经过多年多次的迭代的老系统，已无力再进行大规模的功能开发。
在面对各种复杂场景时，通常很难对一个已经存在的系统做全量的技术栈升级或重构。
使用qiankun搭建一个基座，新模块使用React开发，主应用接入新模块和历史项目。各子应用之间具备互相跳转的能力，所以在历史项目中也可以直接打开新模块。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1631088288471-2235ea85-ba91-48d0-8cce-ca98f3c7a4e9.png#averageHue=%23fcfcfc&clientId=udb5706bc-3e02-4&from=paste&height=203&id=TLrWi&originHeight=203&originWidth=736&originalType=binary&ratio=1&rotation=0&showTitle=false&size=14055&status=done&style=none&taskId=u9696e8db-021b-457b-a3cb-236501ce39f&title=&width=736)

_**微前端架构优势：保证应用具备独立开发权的同时，具有将它们整合到一起保证产品完整的流程体验的能力。同时还能为多个应用提供通用的能力。**_

#### 不适用场景
本质上，微前端(微服务架构)关注的是如何解决组织和团队间协作带来的工程问题，而不是单纯的某个技术问题。
就像DDD一样，提供一种软件架构的上的思想，并不直接解决实际问题。

**没有子应用的话语权**
系统里的所有组件/模块都是由一个个不同的的团队开发的，不具备系统内所有架构组件的话语权，为改造过程增加大量沟通协调成本。

**没有动力改造**
改造系统的效果没有覆盖新老系统混杂带来的问题，没有足够动力去治理、改造系统中的所有模块。改造没什么明显的效果。

**各模块之间本身强耦合**
系统及组织架构上，各模块之间本身就是强耦合、自洽、不可分离的，系统本身就是一个最小单元，拆分的成本高于治理的成本。

**用户体验要求高**
不允许交互上不一致的情况出现，不允许应用内元素风格不一致，从产品层面上否决了渐进式升级的技术策略。


微应用不宜拆分过细，建议按照业务域。业务关联紧密的功能单元应该做成一个微应用，反之关联不紧密的可以考虑拆分成多个微应用。
 一个判断业务关联是否紧密的标准：看这个微应用与其他微应用是否有频繁的通信需求。如果有，可能说明这两个微应用本身就是服务于同一个业务场景，合并成一个微应用可能会更合适。

## qiankun
### 介绍
qiankun 是一个生产可用的微前端框架，它基于 [single-spa](https://zh-hans.single-spa.js.org/) ，具备 js 沙箱、样式隔离、HTML Loader、预加载 等微前端系统所需的能力。qiankun 可以用于任意 js 框架，微应用接入像嵌入一个 iframe 系统一样简单。

### 特点
**技术栈无关**
任意技术栈的子应用均可接入到主应用中。

**各应用具备完全自主权**
所有接入的应用都可以独立开发、独立运行、独立部署。

**HTML Entry**
以子应用构建产物中的HTML作为入口，主框架可以通过 fetch html 的方式获取子应用的静态资源，同时将 HTML document 作为子节点挂在到主框架中。
对比JS Entry的方式，要求子应用的所有资源打包到一个 js bundle 里，包括 css、图片等资源。除了打出来的包可能体积庞大之外的问题之外，资源的并行加载等特性也无法利用上。

**样式隔离**
确保各应用之间样式互相不干扰。
qiankun采用HTML Entry的方案接入子应用，在子应用切出/卸载后会直接移除去 HTML 结构，从而自动移除了其样式表。

**JS 沙箱**
各个子应用之间的全局变量不会互相干扰，从而保证每个子应用之间的软隔离。
JS 沙箱为每个微应用生成单独的 window proxy 对象，配合 HTML Entry 提供的 JS 脚本执行器来实现 JS 隔离。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1631092520576-f697eeb2-991f-4add-a22c-81f89fe16ab9.png#averageHue=%23f3efec&clientId=ua8637087-629d-4&from=paste&height=479&id=u6f7fa040&originHeight=479&originWidth=461&originalType=binary&ratio=1&rotation=0&showTitle=false&size=22487&status=done&style=none&taskId=uc40fd841-41c8-4f1d-8568-f7cad2b2ec1&title=&width=461)
## qiankun实践
### 主应用
**1.安装qiankun**
```
yarn add qiankun
```

**2.环境变量配置**
环境变量提供不同模式下的全局参数。
在项目根目录建立环境变量配置文件。

.env.development
```
VUE_APP_SUB_VUE=//localhost:3001/subapp/sub-vue/
VUE_APP_SUB_REACT=//localhost:3002/subapp/sub-react/
VUE_APP_SUB_HTML=//localhost:3003/
```

.env.production
```
VUE_APP_SUB_VUE=//www.production.com/subapp/sub-vue/
VUE_APP_SUB_REACT=//www.production.com/subapp/sub-react/
VUE_APP_SUB_HTML=//www.production.com/subapp/sub-html/
```


**_注意：如果为Vue项目，变量名必须以VUE_APP_开头；如果为React项目，变量名必须以REACT_APP_开头。_**

**3.子应用规则配置**
将子应用关联到一些 url 规则，当子应用注册完之后，一旦浏览器的 url 发生变化，就会触发 qiankun 的匹配逻辑，所有 activeRule 规则匹配上的子应用就会被挂载到指定的 container 中，同时依次调用子应用暴露出的生命周期钩子。
为了在主应用中对所有子应用统一管理，将所有子应用的配置统一放在src/channel/micro-app.js中。
```javascript
// vue子应用配置
const subVueApp = {
    name: 'sub-vue',
    entry: process.env.VUE_APP_SUB_VUE,
    activeRule: '/sub-vue'
}

// React子应用配置
const subReactApp = {
    name: 'sub-react',
    entry: process.env.VUE_APP_SUB_REACT,
    activeRule: '/sub-react'
}

// html子应用配置
const subHtmlApp = {
    name: 'sub-html',
    entry: process.env.VUE_APP_SUB_HTML,
    activeRule: '/sub-html'
}

const subApps = [subVueApp, subReactApp, subHtmlApp]

// 为所有子应用配置添加统一的配置
const apps = subApps.map((item) => {
    return {
        ...item,
        //  子应用挂载节点
        container: '#subapp-container',
        // 传递信息给子应用
        props: {
            // 基础路由，子应用的路由前缀(router的base)
            routerBase: item.activeRule
        }
    }
})

export default apps

```

**参数说明**

- name ：子应用的名称。子应用之间必须确保唯一。
- entry： 子应用的入口。表示子应用的访问地址。
- activeRule：子应用的激活规则。
- container :子应用挂载的节点。

主应用**运行时**qiankun先加载 entry 资源，待 entry 资源加载完毕，确保子应用的路由系统注册进主框架之后，由子应用的路由系统接管 url change 事件。同时在子应用路由切出时，需要触发相应的 destroy 事件，子应用在监听到该事件时，调用自己的卸载方法卸载应用。


**5.注册子应用**
在主应用的入口文件中调用registerMicroApps()方法注册子应用。
```javascript
import Vue from 'vue'
import { registerMicroApps, start } from 'qiankun'
import router from './router'
import store from './store'
import App from './App.vue'
import './assets/styles/app.css'
import microApps from './channel/micro-app'

Vue.config.productionTip = false

new Vue({
    el: '#app',
    router,
    store,
    render: (h) => h(App)
})

registerMicroApps(microApps)
start()
```
![](https://cdn.nlark.com/yuque/0/2021/png/1561110/1631087635672-129dbfb2-35e7-448d-ac63-1321e2b83649.png#averageHue=%23fefefd&clientId=udb5706bc-3e02-4&from=paste&id=ua15aa739&originHeight=606&originWidth=800&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ua7de3224-0e78-4483-ab4c-5acf17bc798&title=)

**手动加载子应用**
如果微应用不是直接跟路由关联的时候，也可以选择手动加载微应用的方式，这种场景下微应用是一个不带路由的可独立运行的业务组件。可以在脱离路由的限制下，以更自由的方式去加载微应用。
```javascript
import { loadMicroApp } from 'qiankun' 

loadMicroApp({
  name: 'sub-react',
  entry: process.env.VUE_APP_SUB_REACT,
  container: '#container'
})
```
### 
### 子应用
子应用不需要额外安装任何其他依赖即可接入qiankun主应用。

#### Vue子应用
**1.子应用打包配置**
```javascript
const packageName = require('../package.json').name

module.exports = {
	devServer: {
		headers: {
			'Access-Control-Allow-Origin': '*'
		}
	},
	output: {
		library: `${packageName}-[name]`,
		libraryTarget: 'umd',
		jsonpFunction: `webpackJsonp_${packageName}`
	},
}
```


**为什么需要允许开发环境跨域？**
由于 qiankun 是通过 fetch 去获取子应用静态资源的，所以必须要求这些静态资源支持跨域。

**为什么打**[**umd**](https://www.jianshu.com/p/6e61bf5c4d23)**包？**
微前端架构下，需要获取到子应用暴露出的一些钩子引用，如 bootstrap、mount、unmout 等，从而能对接入应用有一个完整的生命周期控制。而由于子应用通常又有集成部署、独立部署两种模式同时支持的需求，所以只能选择 umd 这种兼容性的模块格式打包子应用。
  	只需要走 umd 包格式中的 global export 方式获取子应用的导出，就可以在浏览器运行时获取远程脚本中导出的模块引用。大体的思路是通过给 window 变量打标记，记住每次最后添加的全局变量，这个变量一般就是应用 export 后挂载到 global 上的变量。 [ systemjs global import实现](https://github.com/systemjs/builder)。


**2.创建 public-path**
 用于修改[运行时的 publicPath](https://webpack.docschina.org/guides/public-path/#on-the-fly)
**_注意：运行时的 publicPath 和构建时的 publicPath 是不同的，两者不能等价替代。_**

 publicPath被设置为空字符串 ('') ，这样所有的资源都会被链接为相对路径。但是会这导致子应用的资源地址变成了主应用的地址。比如，主应用为localhost:8000，子应用为localhost:8001，子应用的图片访问地址应该为 [http://localhost:8001/img/logo.82b9c7a5.png](http://localhost:8001/img/logo.82b9c7a5.png)，但是从主应用访问时，图片地址变为[http://localhost:8000/img/logo.82b9c7a5.png](http://localhost:8000/img/logo.82b9c7a5.png)。为了让子应用的资源仍然访问自己的域，在子应用运行时设置公共路径。

src/public-path.js
```javascript
if (window.__POWERED_BY_QIANKUN__) {
	__webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
}
```
在入口文件最顶部引入 public-path.js
 	

**3.导出微前端生命周期钩子**
子应用需要在自己的入口 js (通常就是 webpack 配置的的 entry js) 导出 bootstrap、mount、unmount 三个生命周期钩子，以供主应用在适当的时机调用。
```javascript
import './public-path'
import Vue from 'vue'
import App from './App.vue'
import routes from './router'
import store from './store'
import VueRouter from 'vue-router'

Vue.config.productionTip = false
let instance = null

function render(props = {}) {
    const { container, routerBase } = props
    const router = new VueRouter({
        base: window.__POWERED_BY_QIANKUN__ ? routerBase : process.env.BASE_URL,
        mode: 'history',
        routes
    })
    // 多个应用设置同一个名称的挂载节点(#app)，导致渲染错误。可以通过父应用传过来的props中的container节点，通过这个container再寻找下面的#app。
    instance = new Vue({
        router,
        store,
        render: (h) => h(App)
    }).$mount(container ? container.querySelector('#app') : '#app')
}

// 子应用独立运行的环境
if (!window.__POWERED_BY_QIANKUN__) {
    render()
}

// 只会在微应用初始化的时候调用一次
export async function bootstrap() {
    console.log('[vue] vue app bootstraped')
}

// 子应用每次进入都会调用 mount方法
export async function mount(props) {
    render(props)
}

//  可选生命周期钩子，仅使用 loadMicroApp 方式加载微应用时生效
export async function update(props) {
    console.log('update props', props)
}

// 子应用每次 切出/卸载 会调用的方法
export async function unmount() {
    instance.$destroy()
    instance.$el.innerHTML = ''
    instance = null
}
```


#### React子应用
**在入口文件导出生命周期钩子**
```javascript
import './public-path'
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'
import App from './App'
import * as serviceWorker from './serviceWorker'

function render() {
    ReactDOM.render(<App />, document.getElementById('root'))
}

if (!window.__POWERED_BY_QIANKUN__) {
    render()
}

// 只会在微应用初始化的时候调用一次
export async function bootstrap() {
    console.log('react app bootstraped')
}

// 子应用每次进入都会调用 mount方法
export async function mount(props) {
    // 可通过 props获取基座下发的数据
    render()
}

//  可选生命周期钩子，仅使用 loadMicroApp 方式加载微应用时生效
export async function update(props) {
    console.log('update props', props)
}

// 子应用每次 切出/卸载 会调用的方法
export async function unmount() {
    ReactDOM.unmountComponentAtNode(document.getElementById('root'))
}
```

#### HTML子应用
**1.在入口文件导出生命周期钩子**

js/main.js
```javascript
const render = () => {
  document.querySelector('#current-env').innerHTML = 'qiankun'

  const node = document.createElement('div')
  node.innerHTML = `<a target="_blank" href="${window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__}">打开独立运行环境</a>`

  document.querySelector('.container').appendChild(node)

  return Promise.resolve();
};

(global => {
  global['prehtml'] = {
    bootstrap: () => {
      console.log('purehtml bootstrap');
      return Promise.resolve();
    },
    mount: (options) => {
      console.log('purehtml mount', options);
      return render(options);
    },
    unmount: () => {
      console.log('purehtml unmount');
      return Promise.resolve();
    },
  };
})(window);
```

**2.开发环境HTML**
由于没有webpack自动构建，线上path需要根据部署路径手动更换。为了不在部署的时候进行人工操作，把html文件分开管理，线上环境的html放在 public 文件夹。

index.html
```html
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>sub-html子应用开发模式</title>
    <style>
      .container{
        padding: 20px;
      }
    </style>
  </head>
  <body>
    <div class="container">
      纯原生HTML子应用
    </div>

    <!-- 开发环境的相对路径引用方式 -->
    <script src="./js/main.js"></script>
  </body>
</html>
```

**3.生产环境HTML**
public/index.html
```html
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>sub-html子应用生产环境</title>
  <style>
    .container {
      padding: 20px;
    }
  </style>
</head>

<body>
  <div class="container">
    纯原生HTML子应用, 当前处于独立运行环境。
  </div>

  <script src="/subapp/sub-html/js/main.js"></script>
</body>

</html>
```

 [官方Demo](https://github.com/umijs/qiankun/tree/master/examples/purehtml)

## 核心问题和解决方式
### 样式冲突
默认情况下沙箱可以确保单实例场景子应用之间的样式隔离。比如 HTML Entry 模式下，子应用加载完成的后的 DOM 结构：
```html
<html>
<body>
  <div id="container">
    
    <!-- 子应用完整的 html 结构 -->
    <div id="qiankun_sub-app">
      <div id="root">....</div>
      <style type="text/css"></style>
    </div>
    
  </div>
</body>
</html>
```
当子应用被替换或卸载时，sub-app 节点的 innerHTML 也会被复写，样式表自然就被移除。

但是这种方式无法确保主应用跟子应用、或者多实例场景的子应用样式隔离。
```html
<html>
<body>
  <div id="container">
    <div class="menu"></div>
    
    <div id="qiankun_sub-app">
      <div id="root">
        <div class="menu"></div>
      </div>
      <style type="text/css"></style>
    </div>
    
  </div>
</body>
</html>
```
主应用和子应用中的css选择器".menu"就会产生冲突。


#### 开启css module
```javascript
module.exports = {
    module: {
      rules: [{
        test: /\.css$/,
        use: ['style-loader', {
          loader: 'css-loader',
          options: {
            modules: true,
            localIdentName: '[path][name]__[local]--[hash:base64:5]'
          },
        }]
      }]
    }
 }
```
根据命名规则自动为类名添加前缀和后缀，比如.menu就会变成：main-src-pages-home-style__menu--1CaHo和micro-src-pages-home-style__menu--3uD9F。
缺点：需要webpack配合使用，如果子应用是一个很老的项目，则无法使用这种方式进行改造。
即使是使用Vue或者React开发的项目，出于种种原因(比如调试的时候很难直接肉眼找到真实的类名)，如果最开始就没有采用css module的方案，改造的时候冒然更改全局的配置对子应用的破坏性非常大。

#### BEM命名法
定义css选择器的私有前缀。
```html
 <div class="main-app__menu"></div>
 
 <div class="sub-app__menu"></div>
```
缺点：子应用需要在最初开发时就采用[BEM](https://blog.csdn.net/chenmoquan/article/details/17095465)命名法。如果要改造不可能手动的将所有类名都进行一个个的修改。

#### qiankun
如果接入的子应用本身已经通过工程化的手段来处理了其自身的css命名冲突问题，那么接入之后一般也就不会和主应用产生冲突。
如果子应用中存在大量通用的css选择器，比如标签选择器a、div、input，或者名称很简单的类选择器.header、footer，那么当子应用挂载之后，往往会污染到主应用中的样式。
针对这种情况qiankun支持使用以下两种方式来进行样式隔离。

**Shadow Dom**
严格的样式隔离模式。这种模式下 qiankun 会为每个子应用的容器包裹上一个 [shadow dom](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components/Using_shadow_DOM) 节点，从而确保微应用的样式不会对全局造成影响。
```javascript
start({
    sandbox: {
        strictStyleIsolation: true
    }
})
```

 这种方式也存在一定的问题，比如在子应用中 打开了一个模态窗，而这个模态窗被挂载到body上，那么它的样式就会丢失。
```html
<html>
<body>
  <div id="container">
    <div class="menu"></div>
    
    <div id="qiankun_sub-app">
      <div id="root">
        <div class="menu"></div>
      </div>
      <style type="text/css"></style>
    </div>
    
  </div>
  <div class="modal"></div>
</body>
</html>
```

      除此之外，由于React事件机制(Raact中的事件为自定义的合成事件)的原因，开启ShadowDom后也会让事件失效，在Raact项目中同时还要做一些适配。
  
**改写css选择器**
       qinakun提供了一个实验性的样式隔离特性，通过动态改写 css 选择器来实现，类似于 css scoped。
```javascript
start({
    sandbox: {
        experimentalStyleIsolation: true
    }
})
```
这样就会改写子应用所添加的样式，为所有样式规则增加一个特殊的选择器规则来限定其影响范围，比如子应用中的.menu就会变为：div[data-qiankun="sub-app"] .menu[data-v-7ba5bd90]。

    这种方式如果配合ant-design的模态窗使用也会产生模态窗样式失效的问题，因为antd的Modal默认是挂载到body上的，所以css选择器：div[data-qiankun="sub-react"] .ant-modal-mask {}无法选中这种方式使用的模态窗，需要在使用的时候取消这种默认行为：
```jsx
<Modal
    title='Basic Modal'
    visible={modalVisible}
    onOk={handleConfirm}
    onCancel={handleCloseModal}
    getContainer={false}
>
    <p>Some contents...</p>
    <p>Some contents...</p>
    <p>Some contents...</p>
</Modal>
```

qiankun支持的strictStyleIsolation和 experimentalStyleIsolation方案都不太完善，实际开发过程中不建议无脑开启。而且即使出现样式污染，也只会干扰到基座公共部分如头部和侧边菜单，全局性的样式问题也很容易发现，遇到具体问题具体处理即可。

### 应用间通信
#### props 下发状态
微前端场景下，最合理的通信方案是通过 URL 及 CustomEvent 来处理。但在一些简单场景下，基于 props 的方案会更直接便捷， qiankun提供了一个initGlobalState()方法用于在主应用和子应用之间共享状态。

**1.主应用创建共享状态**
micro-action.js
```javascript
import Vue from 'vue'
import { initGlobalState } from 'qiankun'

const initialState = {
    user: {
        name: '张三'
    }
}

const actions = initGlobalState(initialState)

actions.onGlobalStateChange((newState, prev) => {
    for (const key in newState) {
        initialState[key] = newState[key]
    }
})

// 定义一个获取state的方法下发到子应用
actions.getGlobalState = (key) => {
    return key ? initialState[key] : initialState
}

export default actions
```

**2.主应用下发获取状态的方法给子应用**
micro-app.js
```javascript
import microAction from './micro-action'

// vue子应用配置
const subVueApp = {
    name: 'sub-vue',
    entry: process.env.VUE_APP_SUB_VUE,
    activeRule: '/sub-vue'
}

// React子应用配置
const subReactApp = {
    name: 'sub-react',
    entry: process.env.VUE_APP_SUB_REACT,
    activeRule: '/sub-react'
}

// html子应用配置
const subHtmlApp = {
    name: 'sub-html',
    entry: process.env.VUE_APP_SUB_HTML,
    activeRule: '/sub-html'
}

const subApps = [subVueApp, subReactApp, subHtmlApp]

// 为所有子应用配置添加统一的配置
const apps = subApps.map((item) => {
    return {
        ...item,
        //  子应用挂载节点
        container: '#subapp-container',
        // 传递信息给子应用
        props: {
            // 基础路由，子应用的路由前缀(router的base)
            routerBase: item.activeRule,
            // 下发getGlobalState方法
            getGlobalState: microAction.getGlobalState
        }
    }
})

export default apps

```


3.子应用通过 props 获取共享状态并监听：
```javascript
export async function mount(props) {
    // 获取基座下发的数据
    const globalState = props.getGlobalState()
    // //  监听全局数据的变化
    props.onGlobalStateChange((state, prevState) => {
        console.log(state, prevState)
    })
    // 改变全局的数据
    props.setGlobalState({ user: { name: 'aaa' } })
    render(props)
}
```


#### CustomEvent 
**1.实现eventBus**
```typescript
/**
 * 原生customEvent实现eventBus，主要用于微前端事件通信
 */

const tupleStr = <T extends string[]>(...args: T) => args

// 项目中所有自定义事件名称
const eventName = tupleStr(
    // 改变主应用的count
    'changeMainCount'
)

type EventName = typeof eventName[number]
type HandlerType = (...args: any[]) => void

class EventEmitter {
    eventBus: HTMLSpanElement

    constructor() {
        const eventBus = document.getElementById('__micro-bus__') || document.createElement('span')
        eventBus.id = '__micro-bus__'
        this.eventBus = eventBus
        document.body.appendChild(eventBus)
    }

    on(eventName: EventName, handler: HandlerType) {
        this.eventBus.addEventListener(eventName, handler)
        return this
    }

    once(eventName: EventName, handler: HandlerType) {
        const magic = (event) => {
            handler(event)
            this.off(eventName, magic)
        }
        this.on(eventName, magic)
        return this
    }

    off(eventName: EventName, handler: HandlerType) {
        this.eventBus.removeEventListener(eventName, handler)
        return this
    }

    emit(eventName: EventName, ...args: any[]) {
        const customEvent = new CustomEvent(eventName, { detail: args })
        this.eventBus.dispatchEvent(customEvent)
    }
}

export default new EventEmitter()
```

** 2.主应用监听事件**
```vue
<script>
import store from '@/store'
import microBus from '@/channel/micro-frontend/micro-bus'

export default {

    created() {
        microBus.on('changeMainCount', (event) => {
            const [value] = event.detail
            store.commit('setCount', value)
        })
    },
    destroyed() {
        microBus.off('changeMainCount')
    }
}
</script>
```

** 3.子应用触发触发事件**
```vue

<script>
import store from '@/store'
import microBus from '@/channel/micro-frontend/micro-bus'
import { random } from '@/utils/core'
  
 export default {
    name: 'home',
    computed: {
        count() {
            return store.state.count
        }
    },
    methods: {
        changeMainCount() {
            microBus.emit('changeMainCount', random())
        },
    }
}
</script>
```


## 扩展
[qiankun](https://qiankun.umijs.org/zh)
[微前端框架 - qiankun 大法好](https://www.yuque.com/fe-semimonthly/history-article/f0916e17-2b91-4084-8454-82f6589cbf6e)
[探索微前端的场景极限](https://www.yuque.com/kuitos/gky7yw/uyp6wi)
[微前端的核心价值](https://www.yuque.com/kuitos/gky7yw/rhduwc)
[可能是你见过最完善的微前端解决方案](https://zhuanlan.zhihu.com/p/78362028)
[qiankun 微前端方案实践及总结](https://juejin.cn/post/6844904185910018062)
[你可能并不需要微前端](https://mp.weixin.qq.com/s/khrziHjDfgGS4GgHa6qQJg)
[每日优鲜供应链前端团队微前端改造](https://juejin.cn/post/6844903943873675271#heading-0)
[前端微服务化解决方案](https://alili.tech/archive/ea599f7c/)
[为iframe正名，你可能并不需要微前端](https://juejin.cn/post/7185070739064619068)
[CloudExplorer](https://github.com/CloudExplorer-Dev/CloudExplorer-Lite)






## 

