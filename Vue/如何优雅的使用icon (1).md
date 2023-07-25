## Font Class
### 1.选择图标
    在[阿里巴巴矢量图标库](https://www.iconfont.cn/home/index?spm=a313x.7781069.1998910419.2)挑选图标加入购物车，然后从购物车中添加至项目：<br />![](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599038666180-11c24124-9af4-42b1-b2b2-45bb99d02978.png#height=90&id=jFUJE&originHeight=180&originWidth=415&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=208)

### 2.引入图标资源到项目中
(1)复制资源路径<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1599038665367-a38b9fce-2458-4298-afc5-a2db2e877c14.jpeg#height=88&id=iYVQ1&originHeight=176&originWidth=680&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=340)<br />(2)在项目html启动模版文件中引入<br />public/index.html
```html
<link rel="stylesheet" href="//at.alicdn.com/t/font_862105_62svitrr35.css" />
```

也可以使用以下步骤引入<br />(1)在项目中点击下载至本地

![](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599038666398-e578ec87-433b-4c1d-8e3a-db0118ada047.png#height=38&id=VAb8h&originHeight=75&originWidth=195&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=98)<br />(2)拷贝以下文件到assets/fonts文件夹中<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1599038665983-ae18bfab-c9c9-4167-aee9-cccd91f263c4.jpeg#height=113&id=asVz3&originHeight=226&originWidth=234&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=117)<br />(3)在main.js中引入iconfont.css
```javascript
import '@/assets/fonts/iconfont.css'
```

### 3.使用
```vue
<template>
    <div>
       <i class="iconfont icon-password"></i>
       <i class="iconfont icon-search"></i>
    </div>
</template>

<style lang="scss" scoped>
   // 图标的自定义样式，以覆盖在iconfont.css文件中的默认样式。
   .iconfont{
       font-size: 30px;
       color: #4a4a4a;
   }
</style>
```

类名iconfont是在iconfont.css文件中默认定义的,可以在这里修改图标的默认样式：
```css
.iconfont {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

  类名icon-password对应的是图标的名字，可以在图标项目中查看：

![](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599038665658-e904be77-1dfc-427e-b56a-cc4aa9991092.png#height=79&id=iypbh&originHeight=157&originWidth=140&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=70)

## SVG
引入方式与FontClass基本一致。只是另外要引入js文件：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1599038666940-c1886f06-7754-4143-98ee-0f65f65b35dc.jpeg#height=151&id=l8r81&originHeight=302&originWidth=368&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=184)

或者在public/index.html中：
```html
<script src="//at.alicdn.com/t/font_862105_62svitrr35.js"></script>
```
### 
### 1.简易封装SvgIcon组件
src/components/SvgIcon.vue
```vue
<template>
  <svg class="svg-icon" :class="iconClass" aria-hidden="true">
    <use :xlink:href="icon"></use>
  </svg>
</template>
<script>
// 引入项目中下载的iconfont.js
import '@/assets/fonts/iconfont.js'
  
export default {
  props: {
    name: {
      required: true,
      default: ''
    },
    iconClass: {
      default: ''
    }
  },
  computed: {
    icon() {
      return '#icon-' + this.name
    }
  }
}
</script>
<style scoped>
.svg-icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```

### 2.使用
```vue
<template>
  <div class="svg">
    <svg-icon icon-class="icon" name="password"></svg-icon>
  </div>
</template>

<style lang="scss" scoped>
.svg {
  .icon {
    font-size: 30px;
  }
}
</style>
```

## svg-sprite-loader
    使用以上方式每当添加新的图标之后，都需要重新下载项目代码或者修改远程资源的路径。<br />    每当需要使用某个图标的时候都需要打开远程图标库，查看所要使用的图标的名称。<br />    使用svg-sprite-loader，可以按需管理图标，当要添加新的图标，只需要将图标下载，放置在icons文件夹中。当需要使用某个图标时，直接可以从项目中查看图标名称。

### 1.安装
```javascript
npm i svg-sprite-loader -D
```

### 2.webpack配置
```javascript
const resolve = (dir) => {
  return path.resolve(process.cwd(), dir)
}

module: {
    rules: [{
      test: /\.jpg|png|gif|jpeg|bmp|svg$/,
      use: {
        loader: 'url-loader',
        options: {
          limit: 10000,
          publicPath: '../',
          name: 'img/[name]-[hash:6].[ext]'
        }
      },
      exclude: resolve('src/components/base/svg-icon/icons')
    }, {
      test: /\.svg$/,
      use: {
        loader: 'svg-sprite-loader',
        options: {
          symbolId: 'icon-[name]'
        }
      },
      include: resolve('src/components/base/svg-icon/icons')
    }]
  },
```

### 3.下载svg图标
在src/components/base/svg-icon文件夹中创建一个icons文件夹，专门用于放置下载的svg图标文件。

![](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599038666757-a1762d0b-450f-4174-a6e0-9d890d30c5d7.png#height=35&id=dfzQg&originHeight=69&originWidth=375&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=188)<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1599038665500-f248b385-100c-4a9b-905a-b3a6ebc57746.jpeg#height=85&id=xiEIb&originHeight=170&originWidth=236&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=118)

### 4.导入icons文件夹中下载的图标
    src/components/index.js
```javascript
// 注册全局组件，这样使用的时候不用每次都import。
import Vue from 'vue'
import SvgIcon from './base/svg-icon'
Vue.component('svg-icon', SvgIcon)

//导入所有svg图标，然后结合svg-sprite-loader可以按需使用
const requireContext = require.context('./base/svg-icon/icons', false, /\.svg$/)
const requireAll = (requireContext) => requireContext.keys().map(requireContext)
requireAll(requireContext)
```

### 5.main.js
```javascript
import '@/components'
```

### 6.封装SvgIcon组件
src/components/base/svg-icon/SvgIcon.vue
```vue
<template>
  <svg class="svg-icon" :class="iconClass" :style="iconStyle" aria-hidden="true">
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
export default {
  props: {
    name: {
      required: true,
      default: ''
    },
    iconClass: {
      type: String,
      default: ''
    },
    size: {
      type: [String, Number],
      default: 18
    },
    color: {
      type: String
    }
  },
  computed: {
    iconName() {
      return '#icon-' + this.name
    },
    iconStyle() {
      return {
        fontSize: typeof this.size === 'string' ? this.size : `${this.size}px`,
        color: this.color
      }
    }
  }
}
</script>

<style scoped>
.svg-icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```

### 7.使用
```vue
<template>
  <div>
    <svg-icon name="password" color="pink" :size="40"></svg-icon>
  </div>
</template>
```
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599061327103-831918c5-5f38-4454-aa74-2ff0d1ba2164.png#height=63&id=eoufa&originHeight=126&originWidth=164&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1972&status=done&style=none&title=&width=82)

### 8.本地svg图标预览
    可以直接在浏览器中打开查看svg图标文件。<br /> 	mac平台可以安装[Gapplin](http://gapplin.wolfrosch.com/)，然后在本地直接打开查看。<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1599063395356-41288705-5738-440b-8fdd-952de9b1f16f.jpeg#height=150&id=f69kf&originHeight=598&originWidth=798&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=200)

 2.windows平台可以安装[SVG Explorer Extension](https://pan.baidu.com/s/1qkdg4nRZSQ9vJJfoy4QUTQ)。提取码：m3x5<br />    查看的时候将查看方式设为"中图标"

![](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599063431346-c7534c83-488e-4dcf-b3b9-fc704a24db9c.png#height=57&id=wh6CA&originHeight=114&originWidth=317&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=159)

![](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599063456612-1fa967b7-0dc2-4d9e-956b-0bf0885ea03b.png#height=80&id=r8UMy&originHeight=319&originWidth=825&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=206)

3.vscode的 Svg Preview插件也可以便捷的查看svg图标

### 9.react+ts项目
在react项目中使用时如果无法调用require.context()，可以

```javascript
const requireContext = require['context']('./base/svg-icon/icons', false, /\.svg$/)
```

如果使用eslint，配置：
```javascript
"no-string-literal":false,
```


## svg其他用途
制作PPT的时候可以使用一些SVG图标。通过图形填充改变颜色：

