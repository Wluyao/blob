## 图标的演化
|  | **png** | **font class** | **svg** |
| --- | --- | --- | --- |
| 优点 | 渲染性能好   | 可以通过css设置图标颜色和大小 | 可以通过css设置图标颜色和大小<br />支持多色图标<br />可以通过文本编辑器来创建和修改 |
| 缺点 | 放大之后图片质量下降<br />无法修改颜色 | 所有图标样式在一个文件中，每次增加图标需重新更新整套文件<br />只能使用单色图标 | 复杂度高会降低渲染性能<br />兼容性较差 |

## 图标的使用
### 1.png
(1)将png图片放在src/assets/icons目录中。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1647517507644-10ea5b3d-08a6-48fc-9c33-8aa49add82ab.png#clientId=uf51d47ff-16f7-4&from=paste&height=90&id=uf62ac8ac&originHeight=90&originWidth=92&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4255&status=done&style=none&taskId=u19b17806-7f5c-40c4-8cb8-f0af05b5ec9&title=&width=92)<br />(2)导入要使用的图片，把图标当做普通的图片使用。
```vue
<script >
  import iconUser from '/src/assets/icons/user.png'
</script>

<template>
  <img :src="iconUser"/>
</template>
```
(3)调整图标大小
```vue
<template>
  <img :src="iconUser" style="width: 24px; height: 24px"/> 
</template>
```
_**图片本身大小是16px*16px，这里想把图标调大一点，发现它变模糊了。**_

(4)调整图标颜色<br />**图标本身是灰色，当鼠标hover在图标上的时候，让图标变成蓝色。**<br />_**抱歉，做不到。**_

### 2.font class
####  (1)选择图标
在[阿里巴巴矢量图标库](https://www.iconfont.cn/home/index?spm=a313x.7781069.1998910419.2)挑选图标加入购物车，然后从购物车中添加至项目。<br />![](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599038666180-11c24124-9af4-42b1-b2b2-45bb99d02978.png?x-oss-process=image%2Fresize%2Cw_208%2Climit_0#from=url&height=80&id=zgarO&originHeight=90&originWidth=208&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=&width=184)

#### (2)引用图标
引入图标资源到前端项目中<br />index.html
```html
<link rel="stylesheet" href="//at.alicdn.com/t/font_862105_62svitrr35.css" />
```

也可以使用以下步骤引入

- 在项目中点击下载至本地

![](https://cdn.nlark.com/yuque/0/2020/png/1561110/1599038666398-e578ec87-433b-4c1d-8e3a-db0118ada047.png#height=38&id=VAb8h&originHeight=75&originWidth=195&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=98)

- 拷贝font和css文件到assets/fonts文件夹中

- 在main.js中引入iconfont.css
```javascript
import '@/assets/fonts/iconfont.css'
```

#### (3)使用
```vue
<template>
  <div>
    <i class="iconfont icon-user"></i>
  </div>
</template>

<style lang="scss" scoped>
  // 图标的自定义样式，以覆盖在iconfont.css文件中的默认样式。
  .iconfont{
    font-size: 24px;
    color: #1296db;
  }
</style>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1647518035754-c2ffa507-7a61-40ff-bec6-6266083b0b5a.png#clientId=uf51d47ff-16f7-4&from=paste&height=60&id=ufe6f73ba&originHeight=60&originWidth=69&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1758&status=done&style=none&taskId=u1ef4b0bd-d3c6-42cc-91b2-5b28c12a68f&title=&width=69)<br />类名iconfont是在iconfont.css文件中默认定义的,可以在这里修改图标的默认样式：
```css
.iconfont {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```
  类名icon-user对应的是图标的名字，可以在图标项目中查看：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1647517981504-c13b6207-f1cc-44a6-b9d0-1b4830ea286a.png#clientId=uf51d47ff-16f7-4&from=paste&height=151&id=u91defef4&originHeight=151&originWidth=122&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3323&status=done&style=none&taskId=u03b30c87-d4d5-4759-b24f-fe2abd5056e&title=&width=122)<br />如果需要通过css修改图标的颜色，一定要确保图标是单色图标，比如上传图标到iconfont上的时候，需要注意选择"去除颜色并提交"<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648108782209-8e0736c4-2bb2-48c6-a199-98caf784b16c.png#clientId=u0c35e152-1427-4&from=paste&height=75&id=udee25ca4&originHeight=103&originWidth=440&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4281&status=done&style=none&taskId=ub5aaaf4c-42f6-4c68-afb1-ec5c5aaac90&title=&width=322)

(4)加入新的图标<br />假设图标已经存在，使用起来也比较方便。<br />但是要加入新的图标，就需要将步骤1和2重走一遍。<br />_**先将图标加入到iconfont项目中，再修改前端项目中的图标在线链接，或者将图标资源下载下来整体替换掉项目中的旧资源。**_

### 3.svg
引入方式与FontClass基本一致。只是另外要引入js文件：

或者在public/index.html中：
```html
<script src="//at.alicdn.com/t/font_862105_62svitrr35.js"></script>
```
#### 使用
添加全局图标样式
```css
.svg-icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
```
在svg use标签中应用
```html
<svg class="icon" aria-hidden="true">
  <use xlink:href="#icon-user"></use>
</svg>
```

现在看来svg图标对比font class除了可以使用多色图标以外，在项目中引入和使用并没有很方便。

## SVG图标使用进阶
几个主要问题

- 使用的时候需要手写svg代码。
- 每次加入新的图标仍然要重新引入整套资源。
- 每次想使用某个图标的时候需要去远程iconfont上查图标的名字。

### 1.封装图标组件
```html
<script lang="ts" setup>
import { computed } from 'vue'

interface IProps {
  // 图标名称
  name?: string
  // 样式
  iconClass?: string
  // 图标大小
  size?: string | number
  //  图标颜色
  color?: string
  // 线条的粗细
  strokeWidth?: string | number
}

const props = withDefaults(defineProps<IProps>(), {
  size: 14,
  strokeWidth: 3,
})

// 图标名称
const iconName = computed(() => '#icon-' + props.name)

//  图标样式
const iconStyle = computed(() => ({
  fontSize: typeof props.size === 'string' ? props.size : `${props.size}px`,
  color: props.color,
}))
</script>

<template>
  <svg class="base-icon" :class="iconClass" :style="iconStyle" aria-hidden="true">
    <use :xlink:href="iconName" :stroke-width="strokeWidth"></use>
  </svg>
</template>

<style scoped>
.base-icon {
  display: inline-block;
  width: 1em;
  height: 1em;
  vertical-align: middle;
  overflow: hidden;
  fill: currentColor;
  stroke: currentColor;
}
</style>
```

使用
```html
 <BaseIcon  name="user" color="#0084ff" :size="18"/>
```

### 2.svg-sprite-loader
    使用svg-sprite-loader，可以按需管理图标。<br />当要添加新的图标，只需要将图标放置在icons文件夹中。<br />当需要使用某个图标时，直接可以在本地项目中查看图标名称。

#### (1)安装
```java
yarn add svg-sprite-loader -D
```

#### (2)配置
webpack
```json
const path = require("path");
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
    exclude: resolve('src/assets/icons')
  }, {
    test: /\.svg$/,
    use: {
      loader: 'svg-sprite-loader',
      options: {
        symbolId: 'icon-[name]'
      }
    },
    include: resolve('src/assets/icons')
  }]
  },
```

vue-cli
```json
const path = require("path");
const resolve = (dir) => {
  return path.resolve(process.cwd(), dir)
}

module.exports = {
  chainWebpack: config => {
    config.module.rule("svg").uses.clear();
    config.module
      .rule("icons")
      .test(/\.svg$/)
      .include.add(resolve("src/assets/icons"))
      .end()
      .use("svg-sprite-loader")
      .loader("svg-sprite-loader")
      .options({
        symbolId: "icon-[name]"
      });
  }
};
```

#### (3)添加图标
将图标svg文件放在项目assets/icons/modules文件夹中。

#### (4)自动导入图标  
  src/assets/icons/index.js
```javascript
// 自动导入所有图标
const iconContext = require.context("./modules", false, /\.svg$/);
iconContext.keys().map(iconContext);
```

src/main.js
```javascript
import "./assets/icons";
```

#### (5)使用
```html
 <BaseIcon  name="user" color="#0084ff" :size="18"/>
```

#### (6)本地svg图标预览
    	可以直接在浏览器中打开查看svg图标文件。

 	mac平台可以安装[Gapplin](http://gapplin.wolfrosch.com/)，然后在本地直接打开查看。<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1599063395356-41288705-5738-440b-8fdd-952de9b1f16f.jpeg#height=150&id=f69kf&originHeight=598&originWidth=798&originalType=binary&ratio=1&rotation=0&showTitle=false&size=0&status=done&style=none&title=&width=200)

 <br />windows平台可以安装[SVG Explorer Extension](https://pan.baidu.com/s/1qkdg4nRZSQ9vJJfoy4QUTQ)。提取码：m3x5<br />    查看的时候将查看方式设为"中图标"<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648111832380-f2ded968-80d1-4caf-8052-b63b647141b7.png#clientId=u0c35e152-1427-4&from=paste&height=235&id=u9992f810&originHeight=235&originWidth=546&originalType=binary&ratio=1&rotation=0&showTitle=false&size=47315&status=done&style=none&taskId=ueb272a09-be47-46cd-aa91-651801f026d&title=&width=546)

vscode的 Svg Preview插件也可以便捷的查看svg图标。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648111762833-ec5e55af-e1fa-45af-9da6-e9e623a26ed5.png#clientId=u0c35e152-1427-4&from=paste&height=51&id=u678c66c6&originHeight=51&originWidth=406&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4203&status=done&style=none&taskId=u6f25379a-2f11-45bd-a117-02ec2da2649&title=&width=406)

每次使用图标的时候只需进行(3)添加图标和(5)使用图标两步即可。


### 3.vite中使用
在vite项目中如果想要达到svg-sprite-loader的使用效果，需要进行自定义的插件配置。

/build/vite/plugin/svgBuilder.js
```javascript
import { readFileSync, readdirSync } from 'fs'

let idPerfix = ''
const svgTitle = /<svg([^>+].*?)>/
const clearHeightWidth = /(width|height)="([^>+].*?)"/g

const hasViewBox = /(viewBox="[^>+].*?")/g

const clearReturn = /(\r)|(\n)/g

function findSvgFile(dir) {
  const svgRes = []
  const dirents = readdirSync(dir, {
    withFileTypes: true,
  })
  for (const dirent of dirents) {
    if (dirent.isDirectory()) {
      svgRes.push(...findSvgFile(dir + dirent.name + '/'))
    } else {
      const svg = readFileSync(dir + dirent.name)
      .toString()
      .replace(clearReturn, '')
      .replace(svgTitle, ($1, $2) => {
        // console.log(++i)
        // console.log(dirent.name)
        let width = 0
        let height = 0
        let content = $2.replace(clearHeightWidth, (s1, s2, s3) => {
          if (s2 === 'width') {
            width = s3
          } else if (s2 === 'height') {
            height = s3
          }
          return ''
        })
        if (!hasViewBox.test($2)) {
          content += `viewBox="0 0 ${width} ${height}"`
        }
        return `<symbol id="${idPerfix}-${dirent.name.replace('.svg', '')}" ${content}>`
      })
      .replace('</svg>', '</symbol>')
      svgRes.push(svg)
    }
  }
  return svgRes
}

export const svgBuilder = (path, perfix = 'icon') => {
  if (path === '') return
  idPerfix = perfix
  const res = findSvgFile(path)
  // console.log(res.length)
  // const res = []
  return {
    name: 'svg-transform',
    transformIndexHtml(html) {
      return html.replace(
        '<body>',
        `
        <body>
        <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="position: absolute; width: 0; height: 0">
        ${res.join('')}
        </svg>
        `
      )
    },
  }
}
```

vite.config.ts
```javascript
import { svgBuilder } from './build/vite/plugin/svgBuilder'

export default defineConfig({
  base: './',
  plugins: [vue(), svgBuilder('./src/assets/icons/')],
})
```

使用
```html
 <BaseIcon  name="user" color="#0084ff" :size="22" />
```

图标每次的添加、使用和在vue-cli中使用svg-sprite-loader是一样的，麻烦的就是每次添加新的图标之后需要重启项目，以导入新添加的图标。

### 4.unplugin-icons
在流行的一些Vue3组件库中，它们对于图标的使用都类似以下方式。<br />ElementPlus:
```html
  <el-icon :size="20">
    <edit />
  </el-icon>
```

NaiveUI
```html
  <n-icon size="40">
    <game-controller-outline />
  </n-icon>

  <n-icon :component="CashOutline" size="40" />
```
每个图标即是一个Vue组件。和svgr的原理类似，它会将每个导入的svg图标转换成一个React组件。

在Vite项目中即不想每次添加新的图标之后手动重启项目，又想借用第三方组件库提供的图标能力，还想和它们的这种使用方式保持一致。这些都可以通过[unplugin-icons](https://github.com/antfu/unplugin-icons)来实现。

#### (1)安装
```javascript
yarn add unplugin-icons unplugin-vue-components -D
```

#### (2)vite配置
```javascript
import { defineConfig } from 'vite'
import Icons from 'unplugin-icons/vite'
import IconsResolver from 'unplugin-icons/resolver'
import Components from 'unplugin-vue-components/vite'
import { FileSystemIconLoader } from 'unplugin-icons/loaders'

const path = require('path')
const resolve = (dir: string) => path.resolve(process.cwd(), dir)

export default defineConfig({
  base: './',
  plugins: [
    Icons({
      compiler: 'vue3',
      customCollections: {
        // custom是自定义的图标库的名称
        custom: FileSystemIconLoader(resolve('src/assets/icons')),
      },
      defaultStyle:
        'display:inline-block;width:1em;height:1em;vertical-align:middle;overflow:hidden;fill:currentColor;stroke:currentColor;',
    }),
    Components({
      dts: true,
      resolvers: [IconsResolver({ customCollections: ['custom'], componentPrefix: 'icon' })],
    }),
  ],
})
```

#### (3)单独使用
```html
 <IconCustomUser color="#0084ff" font-size="22" />
```

#### (4)在el-icon中使用
```html
<el-icon color="#0084ff" :size="22">
  <IconCustomUser />
</el-icon>
```


## SVG基础知识
### 1.介绍

-  SVG 指可伸缩矢量图形 (Scalable Vector Graphics)
-  SVG 用于定义用于网络的基于矢量的图形
-  SVG 使用 XML 格式定义图形
-  SVG 图像在放大或改变尺寸的情况下其图形质量不会有损失

**SVG优势**

- 与其他图像格式相比（比如 JPEG 和 GIF），使用 SVG 的优势在于：
- SVG 图像可通过文本编辑器来创建和修改
- SVG 图像可被搜索、索引、脚本化或压缩
- SVG 是可伸缩的
- SVG 可在图像质量不下降的情况下被放大
- 体积小


**Canvas 与 SVG 的比较**

| Canvas | SVG |
| --- | --- |
| <br />- 依赖分辨率<br />- 不支持事件处理器<br />- 弱的文本渲染能力<br />- 能够以 .png 或 .jpg 格式保存结果图像<br />- 最适合图像密集型的游戏，其中的许多对象会被频繁重绘<br /> | <br />- 不依赖分辨率<br />- 支持事件处理器<br />- 最适合带有大型渲染区域的应用程序（比如谷歌地图）<br />- 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快）<br />- 不适合游戏应用<br /> |



**坐标系**<br />SVG 也使用了网格坐标系统。这种坐标系有如下几个特点: 以左上角为坐标系的原点(0,0) X 轴的正方向向右，从 0,0 点开始向右， x 逐渐增大。Y 轴的正方向向下，从 0,0 点开始向下， y 逐渐增大。坐标以像素为单位。<br />在SVG坐标系中，点x=0，y=0是左上角。因此，与普通的图形坐标系相比，y轴是相反的。随着SVG中y的增加，点、形状等向下移动，而不是向上移动。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648119065396-5ba90981-f364-471e-8ed7-d4d22495864e.png#clientId=u0c35e152-1427-4&from=paste&height=113&id=u31348dc6&originHeight=113&originWidth=116&originalType=binary&ratio=1&rotation=0&showTitle=false&size=600&status=done&style=none&taskId=ubb2c2086-1f6c-427d-be82-cad14ff4b72&title=&width=116)

### 2. 形状CSS属性
path元素和其他形状元素的 CSS属性：

| css属性 | 描述 |
| --- | --- |
| fill | 设置形状的填充颜色。 |
| fill-opacity | 设置形状的填充不透明度。 |
| fill-rule | 设置形状的填充规则。 |
| marker | 设置沿此形状的线（边）使用的标记。 |
| marker-start | 设置沿此形状的线（边）使用的开始标记。 |
| marker-mid | 设置沿此形状的线（边）使用的中间标记。 |
| marker-end | 设置沿此形状的线（边）使用的结束标记。 |
| stroke | 设置用于绘制此形状轮廓的笔触（线条）颜色。 |
| stroke-dasharray | 设置用于绘制此形状轮廓的笔划（虚线）。 |
| stroke-dashoffset | 设置用于绘制此形状轮廓的笔划（直线）破折号偏移量。 |
| stroke-linecap | 设置用于绘制此形状轮廓的笔划（线）线帽。有效值为round，butt和square。 |
| stroke-linejoin | 设置转角处使用的形状或者绘制的基础形状。有效值arcs，bevel，miter， miter-clip，round |
| stroke-miterlimit | 设置用于绘制此形状轮廓的笔划（直线）斜接限制。 |
| stroke-opacity | 设置用于绘制此形状轮廓的笔触（直线）不透明度。 |
| stroke-width | 设置用于绘制此形状轮廓的笔触（线条）宽度。 |
| text-rendering | 设置用于绘制此形状轮廓的文本渲染。 |

### 3.rect矩形
<rect> 标签可用来创建矩形，以及矩形的变种。
```html
<svg>
  <rect width="200" height="200" style="fill:pink;stroke-width:3;stroke:red"/>
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648117105856-4e97ee3e-920f-412b-a4f7-4306c0da3f05.png#clientId=u0c35e152-1427-4&from=paste&height=105&id=ue061d66a&originHeight=105&originWidth=144&originalType=binary&ratio=1&rotation=0&showTitle=false&size=555&status=done&style=none&taskId=u270ee778-e966-421c-994d-f266e9e0ca9&title=&width=144)

- rect 元素的 width 和 height 属性可定义矩形的高度和宽度。
- style 属性用来定义 CSS 属性。
- CSS 的 fill 属性定义矩形的填充颜色。
- CSS 的 stroke 属性定义矩形边框的颜色。
- CSS 的 stroke-width 属性定义矩形边框的宽度。

###  4.circle圆形
<circle> 标签可用来创建一个圆。

```html
<svg>
  <circle cx="100" cy="50" r="40" stroke="black" stroke-width="2" fill="red" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648117189573-914b8f19-eaca-4e2e-a3d1-1e31275bca04.png#clientId=u0c35e152-1427-4&from=paste&height=59&id=ueeb69065&originHeight=59&originWidth=65&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2575&status=done&style=none&taskId=u8552e163-f97d-459d-91b4-9a7520da9f5&title=&width=65)

- cx和cy属性定义圆点的x和y坐标。如果省略cx和cy，圆的中心会被设置为(0, 0)
- r属性定义圆的半径。



### 5. line直线
<line> 元素用来创建一个直线。
```html
<svg>
  <line x1="0" y1="0" x2="200" y2="200" style="stroke:rgb(255,0,0);stroke-width:2" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648117230578-8615753d-97c7-4988-9749-0bdf8f0dd92f.png#clientId=u0c35e152-1427-4&from=paste&height=76&id=u018747f4&originHeight=76&originWidth=82&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1492&status=done&style=none&taskId=uafd702e1-a044-4f3a-90b5-db1c74e4258&title=&width=82)

- x1 属性在 x 轴定义线条的开始
- y1 属性在 y 轴定义线条的开始
- x2 属性在 x 轴定义线条的结束
-  y2 属性在 y 轴定义线条的结束


### 6.path路径
path元素用于定义一个路径。path元素用于绘制由直线，圆弧，曲线等组合而成的高级形状<br />每个命令由一个字母和一组数字（坐标等）组成，它们是该命令的参数。<br />大写命令通常将坐标参数解释为绝对坐标。小写命令通常将坐标参数解释为与当前笔位置相对。
```html
<svg >
  <path d="M50,50
           A30,30 0 0,1 35,20
           L100,100
           M110,110
           L100,0" style="stroke:#660000; fill:none;" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648117320621-fbe37947-7e05-42b2-b31d-04ad26faf746.png#clientId=u0c35e152-1427-4&from=paste&height=56&id=u9832e49a&originHeight=56&originWidth=67&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1491&status=done&style=none&taskId=u96e12f6f-cef5-432f-9c55-09b0c0bc7ce&title=&width=67)

从点50,50到点100,100画一条线。
```html
<svg>
   <path d="M50 50 L100 100" style="stroke:#660000; fill:none;" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648117343669-92dc4b66-dce1-4652-8e91-6b0202bd6c97.png#clientId=u0c35e152-1427-4&from=paste&height=44&id=u49fb5101&originHeight=44&originWidth=48&originalType=binary&ratio=1&rotation=0&showTitle=false&size=561&status=done&style=none&taskId=u3abbce66-7ca1-4205-b14f-3af08505916&title=&width=48)

| **命令** | **参数** | **名称** | **描述** | <br /> |
| --- | --- | --- | --- | --- |
| M | x,y | moveto | 将笔移动到指定点x，y而不绘图。 | M50 50 表示将画笔移动到(50,50)的位置上。 |
| m | x,y | moveto | 相对于当前笔位置将笔移动到指定点x，y，而不绘制。 | <br /> |
|  |  |  |  | <br /> |
| L | x,y | Lineto | 绘制一条从当前笔位置到指定点x，y的直线。 | L(100,100)表示从起始位置画一条到(100,100)的直线。 |
| l | x,y | Lineto | 相对于当前笔位置绘制一条从当前笔位置到指定点x，y的直线。 | <br /> |
|  |  |  |  | <br /> |
| H | X | 水平线 | 画一条水平线到定义的点<br />（指定的x，笔当前的y） | M50 50 H60表示从点(50,50)的位置画一条线到点(60,50)。<br />M50 50 H40表示从点(50,50)的位置画一条线到点(40,50)。 |
| h | X | 水平线 | 向由定义的点绘制一条水平线。<br />(笔当前x+指定x，笔当前y)。 x是相对于当前笔x位置的。 | <br /> |
|  |  |  |  | <br /> |
| V | y | 垂直线 | 在由<br />（定义的当前x，指定y）定义的点上画一条垂直线。 | <br /> |
| v | y | 垂直线 | 画一条垂直线到定义的点<br />（笔当前x，笔当前y +指定的y）。y相对于笔的当前y位置。 | <br /> |
|  |  |  |  | <br /> |
| C | x1，y1 x2，y2 x，y | 曲线 | 从当前点到x，y绘制三次贝塞尔曲线。x1，y1和x2，y2是曲线的两个控制点。 | <br /> |
| c | x1，y1 x2，y2 x，y | 曲线 | 与C相同，但解释相对于当前笔尖的坐标。 | <br /> |
|  |  |  |  | <br /> |
| S | x2，y2 x，y | 速记/<br />平滑曲线 | 从当前笔尖到x，y绘制三次贝塞尔曲线。x2，y2是结束控制点。假定起点控制点与上一条曲线的终点控制点相同。 | <br /> |
| s | x2，y2 x，y | 速记/<br />平滑曲线 | 与S相同，但解释相对于当前笔尖的坐标。 | <br /> |
|  |  |  |  | <br /> |
| Q | x1，y1 x，y | 二次贝塞尔曲线 | 从当点到x，y绘制二次贝塞尔曲线。x1，y1是控制曲线弯曲方式的控制点。 | M100 100 Q100 50 50 50<br />起点是(100,100)，终点是(50,50)，控制点是(100,50)<br /> |
| q | x1，y1 x，y | 二次贝塞尔曲线 | 与Q相同，但解释相对于当前笔尖的坐标。 | <br /> |
|  |  |  |  | <br /> |
| T | x,y | 速记/平滑二次贝塞尔曲线 | 从当前笔尖到x，y绘制二次贝塞尔曲线。假定控制点与最后使用的控制点相同。 | <br /> |
| t | x,y | 速记/平滑二次贝塞尔曲线 | 与T相同，但解释相对于当前笔尖的坐标。 | <br /> |
|  |  |  |  | <br /> |
| A | rx，ry<br />x轴旋转<br />大弧标记，<br />sweepflag<br />x，y | 椭圆弧 | 从当前点到点x，y绘制一个椭圆弧。rx和ry是x和y方向上的椭圆半径。<br />X旋转确定圆弧将绕X轴旋转多少。仅当rx和ry具有不同的值时，它才似乎起作用。<br />似乎没有使用large-arc标志（可以为0或1）。值（0或1）都不会改变圆弧。<br />扫描标志确定绘制弧的方向。 | <br /> |
| a | rx，ry<br />x轴旋转<br />大弧标记，<br />sweepflag<br />x，y | 椭圆弧 | 与A相同，每个点相对于当前笔尖的坐标。 | <br /> |
|  |  |  |  | <br /> |
| Z |  | 封闭路径 | 通过从当前点到第一个点画一条线来封闭路径。 | <br /> |
| z |  | 封闭路径 | 通过从当前点到第一个点画一条线来封闭路径。 | <br /> |


### 7.text文本
<text> 元素用于定义文本。
```html
<svg>
    <text x="0" y="15" fill="red">I love SVG</text>
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648117392683-aba136bb-bb3d-4e18-b9ed-b5033550a171.png#clientId=u0c35e152-1427-4&from=paste&height=22&id=ua67fa52b&originHeight=22&originWidth=94&originalType=binary&ratio=1&rotation=0&showTitle=false&size=777&status=done&style=none&taskId=u73c075fa-4931-42b9-89d9-9dcbe855340&title=&width=94)

## 手写SVG图标
### 1.基础属性调整
```html
<svg width="24" height="24" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg">
  <rect x="5" y="8" width="38" height="24" stroke="currentColor" stroke-width="4" rx="2" />
  <rect x="19" y="32" width="10" height="9" stroke="currentColor" stroke-width="4" />
  <path d="M22 27H26" stroke="currentColor" stroke-width="4" stroke-linecap="round" />
  <path d="M14 41L34 41" stroke="currentColor" stroke-width="4" stroke-linecap="round" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648117593456-52fec081-bc71-4c62-b194-9d983a7f0974.png#clientId=u0c35e152-1427-4&from=paste&height=105&id=u41814eaf&originHeight=480&originWidth=480&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4790&status=done&style=none&taskId=uaf4c3456-2e77-42b9-be62-ceded9c1d5b&title=&width=105)

这是一个电脑的图标，由两个矩形和两个线条组成。拆解如下：
```html
 <rect x="5" y="8" width="38" height="24" stroke="currentColor" stroke-width="4" rx="2" />
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648458352143-fc59a50d-0ac5-42f6-988c-5ec581955a17.png#clientId=u2be20b59-8228-4&from=paste&height=82&id=u81d01ba1&originHeight=82&originWidth=115&originalType=binary&ratio=1&rotation=0&showTitle=false&size=578&status=done&style=none&taskId=u4ee7ad51-0071-4f0c-9344-b84f7c2922a&title=&width=115) <br />以(5,8)为起点，绘制一个宽为38，高为24的矩形，画笔颜色为单色(可继承来自CSS的字体颜色)，画笔宽度为4，矩形圆角为2。
```html
<rect x="19" y="32" width="10" height="9" stroke="currentColor" stroke-width="4" />
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648458396412-b3f2e6bb-5ab1-48fd-8a6a-940fd1c63a56.png#clientId=u2be20b59-8228-4&from=paste&height=93&id=udf37fb87&originHeight=93&originWidth=107&originalType=binary&ratio=1&rotation=0&showTitle=false&size=356&status=done&style=none&taskId=uc19bbec0-451f-4ef6-b429-8f37c678a41&title=&width=107)<br />以(19,32)为起点，绘制一个宽为10，高为9的矩形，
```html
  <path d="M22 27H26" stroke="currentColor" stroke-width="4" stroke-linecap="round" />
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648458426416-0c5092f5-83f5-470c-8fff-57559c8b7765.png#clientId=u2be20b59-8228-4&from=paste&height=44&id=u8b05236f&originHeight=44&originWidth=76&originalType=binary&ratio=1&rotation=0&showTitle=false&size=330&status=done&style=none&taskId=ue081c2b0-3c7a-498e-8f0f-f128b66cc24&title=&width=76)<br />将画笔移动到点(22,27)的位置，然后画一条到点(26,27)的线。
```html
 <path d="M14 41L34 41" stroke="currentColor" stroke-width="4" stroke-linecap="round" />
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648458450037-c5c3cb2c-9fc4-45c3-95ae-86bc6357f288.png#clientId=u2be20b59-8228-4&from=paste&height=55&id=u8393cdfd&originHeight=55&originWidth=120&originalType=binary&ratio=1&rotation=0&showTitle=false&size=387&status=done&style=none&taskId=u376fd161-cdab-4242-ba02-e2921c07908&title=&width=120)<br />将画笔移动到点(14,41)的位置，然后画一条到点(34,41)的线。

#### (1)改变线条颜色
```html
<svg width="24" height="24" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg">
  <rect x="19" y="32" width="10" height="9" stroke="pink" stroke-width="4" />
  <rect x="5" y="8" width="38" height="24" stroke="pink" stroke-width="4" rx="2" />
  <path d="M22 27H26" stroke="pink" stroke-width="4" stroke-linecap="round" />
  <path d="M14 41L34 41" stroke="pink" stroke-width="4" stroke-linecap="round" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648119699033-4e4200de-cc72-4bf5-8595-929af8d2db44.png#clientId=u0c35e152-1427-4&from=paste&height=102&id=ua4bf90ab&originHeight=102&originWidth=118&originalType=binary&ratio=1&rotation=0&showTitle=false&size=923&status=done&style=none&taskId=u476a5da2-011a-4bed-a5ef-8ea532238e1&title=&width=118)<br />直接指定currentColor图标即成为多色图标，不能再通过CSS来改变颜色。

#### (2)改变线条粗细
```html
<svg width="24" height="24" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg">
  <rect x="19" y="32" width="10" height="9" stroke="currentColor" stroke-width="3" />
  <rect x="5" y="8" width="38" height="24" stroke="currentColor" stroke-width="3" rx="2" />
  <path d="M22 27H26" stroke="currentColor" stroke-width="3" stroke-linecap="round" />
  <path d="M14 41L34 41" stroke="currentColor" stroke-width="3" stroke-linecap="round" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648119723883-7d9becb3-ac71-4451-9342-579b94787c64.png#clientId=u0c35e152-1427-4&from=paste&height=106&id=u1265dfaa&originHeight=106&originWidth=117&originalType=binary&ratio=1&rotation=0&showTitle=false&size=969&status=done&style=none&taskId=u45c85529-a3a1-430f-8d69-913a20980ad&title=&width=117)

#### (3)加长线条
```html
<svg width="24" height="24" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg">
  <rect x="19" y="32" width="10" height="9" stroke="currentColor" stroke-width="4" />
  <rect x="5" y="8" width="38" height="24" stroke="currentColor" stroke-width="4" rx="2" />
  <path d="M14 27H34" stroke="currentColor" stroke-width="4" stroke-linecap="round" />
  <path d="M14 41L34 41" stroke="currentColor" stroke-width="4" stroke-linecap="round" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648119670011-e5d7536c-44c1-448e-9250-ad39e02cf404.png#clientId=u0c35e152-1427-4&from=paste&height=108&id=u1669d273&originHeight=108&originWidth=115&originalType=binary&ratio=1&rotation=0&showTitle=false&size=984&status=done&style=none&taskId=u61f4fac6-7e07-44ca-96e0-e33aa4d4d76&title=&width=115)

### 2.基础图标变换

#### (1)基础图标
```html
<svg t="1648121483048" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="9962"
  width="200" height="200">
  <path
    d="M584.192 889.856H129.024c-56.832 0-103.424-47.104-103.424-104.448V356.352c0-57.856 46.592-104.448 103.424-104.448h610.816c56.832 0 103.424 47.104 103.424 104.448v56.832h-69.12V356.352c0-19.456-15.36-35.328-34.304-35.328H129.024c-18.944 0-34.304 15.872-34.304 35.328v428.544c0 19.456 15.36 35.328 34.304 35.328h455.168v69.632z"
    p-id="9963"></path>
  <path
    d="M955.392 501.76h-69.12V203.776c0-19.456-15.36-34.816-34.816-34.816H234.496c-18.944 0-34.816 15.872-34.816 34.816v84.48H130.56V203.776c0-57.344 46.592-104.448 103.936-104.448h616.448c57.344 0 103.936 46.592 103.936 104.448V501.76zM82.432 455.68h440.832v69.12H82.432z"
    p-id="9964"></path>
  <path
    d="M717.824 924.672c-154.624 0-280.576-125.952-280.576-280.576S563.2 363.52 717.824 363.52 998.4 488.96 998.4 643.584s-125.952 281.088-280.576 281.088z m0-492.544c-116.736 0-211.456 94.72-211.456 211.456s94.72 211.456 211.456 211.456 211.456-94.72 211.456-211.456-95.232-211.456-211.456-211.456zM166.4 655.36h117.76v69.12h-117.76z"
    p-id="9965"></path>
  <path
    d="M863.232 678.4h-291.328c-18.944 0-34.816-15.36-34.816-34.816s15.36-34.816 34.816-34.816h291.328c18.944 0 34.816 15.36 34.816 34.816s-15.872 34.816-34.816 34.816z"
    p-id="9966"></path>
  <path
    d="M717.824 823.808c-18.944 0-34.816-15.36-34.816-34.816V498.176c0-18.944 15.36-34.816 34.816-34.816 18.944 0 34.816 15.36 34.816 34.816v291.328c-0.512 18.944-15.872 34.304-34.816 34.304z"
    p-id="9967"></path>
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648121590430-4843b6b1-7649-4655-a46e-b19e44e902bb.png#clientId=u0c35e152-1427-4&from=paste&height=177&id=u65c1c619&originHeight=177&originWidth=198&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5473&status=done&style=none&taskId=u8cc97b10-5c44-41da-a46f-3a893285fe0&title=&width=198)

#### (2)去掉加号，增加一段虚线
```html
<svg t="1648121483048" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="9962"
  width="200" height="200">
  <path
    d="M584.192 889.856H129.024c-56.832 0-103.424-47.104-103.424-104.448V356.352c0-57.856 46.592-104.448 103.424-104.448h610.816c56.832 0 103.424 47.104 103.424 104.448v56.832h-69.12V356.352c0-19.456-15.36-35.328-34.304-35.328H129.024c-18.944 0-34.304 15.872-34.304 35.328v428.544c0 19.456 15.36 35.328 34.304 35.328h455.168v69.632z"
    p-id="9963"></path>
  <path
    d="M955.392 501.76h-69.12V203.776c0-19.456-15.36-34.816-34.816-34.816H234.496c-18.944 0-34.816 15.872-34.816 34.816v84.48H130.56V203.776c0-57.344 46.592-104.448 103.936-104.448h616.448c57.344 0 103.936 46.592 103.936 104.448V501.76zM82.432 455.68h440.832v69.12H82.432z"
    p-id="9964"></path>
  <path
    d="M717.824 924.672c-154.624 0-280.576-125.952-280.576-280.576S563.2 363.52 717.824 363.52 998.4 488.96 998.4 643.584s-125.952 281.088-280.576 281.088z m0-492.544c-116.736 0-211.456 94.72-211.456 211.456s94.72 211.456 211.456 211.456 211.456-94.72 211.456-211.456-95.232-211.456-211.456-211.456zM166.4 655.36h117.76v69.12h-117.76z"
    p-id="9965"></path>
  <line x1="863.232" y1="638.4" x2="563.232" y2="638.4" stroke-width="60" stroke="currentColor" stroke-linecap="round"
    stroke-dasharray="50, 80" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648121706149-4b690378-e53e-4b61-ba29-82129b26fa8b.png#clientId=u0c35e152-1427-4&from=paste&height=175&id=u0ad2c7fa&originHeight=175&originWidth=196&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5506&status=done&style=none&taskId=u8c6dd560-0ce9-4c0c-ba9f-d915bffdeab&title=&width=196)
#### (3)去掉加号
```html
<svg t="1648121483048" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="9962"
  width="200" height="200">
  <path
    d="M584.192 889.856H129.024c-56.832 0-103.424-47.104-103.424-104.448V356.352c0-57.856 46.592-104.448 103.424-104.448h610.816c56.832 0 103.424 47.104 103.424 104.448v56.832h-69.12V356.352c0-19.456-15.36-35.328-34.304-35.328H129.024c-18.944 0-34.304 15.872-34.304 35.328v428.544c0 19.456 15.36 35.328 34.304 35.328h455.168v69.632z"
    p-id="9963"></path>
  <path
    d="M955.392 501.76h-69.12V203.776c0-19.456-15.36-34.816-34.816-34.816H234.496c-18.944 0-34.816 15.872-34.816 34.816v84.48H130.56V203.776c0-57.344 46.592-104.448 103.936-104.448h616.448c57.344 0 103.936 46.592 103.936 104.448V501.76zM82.432 455.68h440.832v69.12H82.432z"
    p-id="9964"></path>
  <path
    d="M717.824 924.672c-154.624 0-280.576-125.952-280.576-280.576S563.2 363.52 717.824 363.52 998.4 488.96 998.4 643.584s-125.952 281.088-280.576 281.088z m0-492.544c-116.736 0-211.456 94.72-211.456 211.456s94.72 211.456 211.456 211.456 211.456-94.72 211.456-211.456-95.232-211.456-211.456-211.456zM166.4 655.36h117.76v69.12h-117.76z"
    p-id="9965"></path>
 
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648121825180-9b1d68e9-de06-4d4d-99f0-08392be67f11.png#clientId=u0c35e152-1427-4&from=paste&height=173&id=u30878669&originHeight=173&originWidth=196&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4935&status=done&style=none&taskId=u6a64d338-2a33-4d74-9083-49ef1f3b21d&title=&width=196)


#### 
#### (4)旋转加号变成叉
```html
<svg t="1648121483048" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="9962"
  width="200" height="200">
  <defs>
    <style type="text/css">
      .rotate_1 {
        transform: rotate(45deg) translate(240px, -695px);
      }

      .rotate_2 {
        transform: rotate(45deg) translate(240px, -695px);
      }
    </style>
  </defs>
  <path
    d="M584.192 889.856H129.024c-56.832 0-103.424-47.104-103.424-104.448V356.352c0-57.856 46.592-104.448 103.424-104.448h610.816c56.832 0 103.424 47.104 103.424 104.448v56.832h-69.12V356.352c0-19.456-15.36-35.328-34.304-35.328H129.024c-18.944 0-34.304 15.872-34.304 35.328v428.544c0 19.456 15.36 35.328 34.304 35.328h455.168v69.632z"
    p-id="9963"></path>
  <path
    d="M955.392 501.76h-69.12V203.776c0-19.456-15.36-34.816-34.816-34.816H234.496c-18.944 0-34.816 15.872-34.816 34.816v84.48H130.56V203.776c0-57.344 46.592-104.448 103.936-104.448h616.448c57.344 0 103.936 46.592 103.936 104.448V501.76zM82.432 455.68h440.832v69.12H82.432z"
    p-id="9964"></path>
  <path
    d="M717.824 924.672c-154.624 0-280.576-125.952-280.576-280.576S563.2 363.52 717.824 363.52 998.4 488.96 998.4 643.584s-125.952 281.088-280.576 281.088z m0-492.544c-116.736 0-211.456 94.72-211.456 211.456s94.72 211.456 211.456 211.456 211.456-94.72 211.456-211.456-95.232-211.456-211.456-211.456zM166.4 655.36h117.76v69.12h-117.76z"
    p-id="9965"></path>
  <path class="rotate_1" d=" M863.232 678.4h-291.328c-18.944 0-34.816-15.36-34.816-34.816s15.36-34.816 34.816-34.816h291.328c18.944 0 34.816
    15.36 34.816 34.816s-15.872 34.816-34.816 34.816z" p-id="9966"></path>
  <path class="rotate_2"
    d="M717.824 823.808c-18.944 0-34.816-15.36-34.816-34.816V498.176c0-18.944 15.36-34.816 34.816-34.816 18.944 0 34.816 15.36 34.816 34.816v291.328c-0.512 18.944-15.872 34.304-34.816 34.304z"
    p-id="9967"></path>
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648122049146-ef6cd0a4-8860-43bf-bf88-3e63fda761e7.png#clientId=u0c35e152-1427-4&from=paste&height=172&id=u9c489df7&originHeight=172&originWidth=194&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5665&status=done&style=none&taskId=u2d5e888b-bf79-441b-b0e6-380b0ab6a18&title=&width=194)

##  图标资源
### 1.iconfon和iconpark
| **iconfont** | **IconPark** |
| --- | --- |
| 图标风格不统一<br />很难对图标进行微调 <br />适合做PPT<br />先有图标，后有代码 | 技术驱动图标样式<br />标准的svg源码<br />真正给程序员用的<br />先有代码，后有图标 |

#### (1)iconfont
```html
<svg t="1648123252760" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2451"
  width="200" height="200">
  <path
    d="M753.845117 371.674021l-17.46272 0 0-83.669608c0-59.275012-22.62837-115.203812-63.715137-157.482731-42.170448-43.394323-99.369172-67.291592-161.058163-67.291592-126.040624 0-224.772276 98.731652-224.772276 224.7733l0 83.669608-16.680914 0c-62.788022 0-113.688295 50.900274-113.688295 113.688295L156.467611 842.961784c0 62.788022 50.900274 113.688295 113.688295 113.688295l483.690234 0c62.788022 0 113.688295-50.900274 113.688295-113.688295L867.534436 485.362316C867.532389 422.574295 816.633139 371.674021 753.845117 371.674021zM328.176344 288.005436c0-102.858646 80.573083-183.432753 183.431729-183.432753 50.423413 0 97.093339 19.447934 131.410935 54.762231 33.547047 34.519188 52.021817 80.214926 52.021817 128.670521l0 83.669608L328.176344 371.675044 328.176344 288.005436zM826.191842 842.961784c0 39.956014-32.390711 72.346725-72.346725 72.346725L270.154883 915.308509c-39.956014 0-72.346725-32.390711-72.346725-72.346725L197.808158 485.362316c0-39.956014 32.390711-72.346725 72.346725-72.346725l483.690234 0c39.956014 0 72.346725 32.390711 72.346725 72.346725L826.191842 842.961784z"
    p-id="2452"></path>
  <path
    d="M509.932921 580.446905c-11.416004 0-20.670785 9.254781-20.670785 20.670785l0 109.554138c0 11.414981 9.254781 20.670785 20.670785 20.670785 11.416004 0 20.670785-9.254781 20.670785-20.670785L530.603707 601.116667C530.602683 589.701686 521.348925 580.446905 509.932921 580.446905z"
    p-id="2453"></path>
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648123296715-4b1dd28f-68fc-436b-a3e5-c1dc41e2fccb.png#clientId=u35f850c0-e5cc-4&from=paste&height=130&id=u302215cd&originHeight=189&originWidth=160&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3499&status=done&style=none&taskId=u1367bc57-ae55-4c99-a2f4-59b753f5cff&title=&width=110)
#### (2)IconPark
```html
<svg width="24" height="24" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg">
  <rect x="6" y="22" width="36" height="22" rx="2" stroke="currentColor" stroke-width="4" />
  <path d="M14 22V14C14 8.47715 18.4772 4 24 4C29.5228 4 34 8.47715 34 14V22" stroke="currentColor" stroke-width="4"
    stroke-linecap="round" />
  <path d="M24 30V36" stroke="currentColor" stroke-width="4" stroke-linecap="round" />
</svg>
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1648123383856-e5fb10a1-9b44-4d10-99bc-09288b8de21f.png#clientId=u35f850c0-e5cc-4&from=paste&height=121&id=ub8d7c70b&originHeight=121&originWidth=116&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1427&status=done&style=none&taskId=uc4ab2f86-175a-4d6d-a3fc-c1b68f1f30e&title=&width=116)

[xicons](https://www.xicons.org)<br />[tablericons](https://tablericons.com/)-图标代码和iconPark一样，也是很有语义化的svg标签。<br />[iconstore](https://www.iconstore.co/)-很多好看的彩色图标<br />[iconduck](https://iconduck.com/)

# <br />










