Taro的使用可以参考官方文档，这里主要总结开发过程中遇到的一些问题，以及如何一步步解决它们。因为并不算是一篇教程，所以并没有直接给出每个问题的解决方案，而是包含了一些自己摸索和思考的过程。

## 项目结构
```
├── config              编译配置
│   ├── dev.js             开发模式配置
│   ├── index.js           默认配置
│   └── prod.js            生产模式配置
├── dist                构建目录
├── docs                文档
├── src                 源码目录
│   ├── assets             静态资源
│	    ├── fonts                字体
│	    ├── images               图片
│	    └── styles               样式
│   ├── components         组件
│       ├── base               基础组件
│       ├── business           业务组件
│       └── taro-ui            taro-ui
│   ├── hooks              Reack hook
│   ├── model              通用数据模型
│   ├── pages              页面
│   ├── service            全局接口
│   ├── store              全局状态
│   ├── utils              全局工具库
│   ├── app.tsx				  根组件
│   └──app.config.js    全局配置
├── .eslintrc.js        ESLint 配置
├── .gitignore          git提交时忽略的文件
├── .babel.config.js    babel配置
├── global.d.ts         全局类型定义
├── package.json        项目基本信息
├── project.config.json 小程序项目配置
├── README.md           项目说明
└── tsconfig.json	      TypeScript配置
```

## 字体图标
**1.在iconfont上下载图标资源**<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627804572939-9b7e7936-43db-4d97-b697-16e6c2b75db7.png#clientId=u3048be33-3d9f-4&from=paste&height=29&id=uf883161c&originHeight=116&originWidth=1564&originalType=binary&ratio=1&size=22867&status=done&style=none&taskId=u35906b16-f6d2-4c94-8eab-8ef73d2f40b&width=391)

**2.引入资源**<br />将下载的文件中的iconfont.css放入项目的assets/fonts目录中，其他文件可以不要，要了很占空间。

**3.修改@font-face**<br />修改iconfont.css中的@font-face为iconfont项目库unicode编码的内容。![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627805055608-0d81f932-d8e6-4183-beee-afdf1ae1b0d6.png#clientId=u3048be33-3d9f-4&from=paste&height=106&id=u4f84f264&originHeight=424&originWidth=1448&originalType=binary&ratio=1&size=91638&status=done&style=none&taskId=uead59598-3d22-44c5-b769-6fc6ab32340&width=362)<br />iconfont.css
```css
@font-face {
  font-family: 'iconfont';  /* project id 862105 */
  src: url('//at.alicdn.com/t/font_862105_62svitrr35.eot');
  src: url('//at.alicdn.com/t/font_862105_62svitrr35.eot?#iefix') format('embedded-opentype'),
  url('//at.alicdn.com/t/font_862105_62svitrr35.woff2') format('woff2'),
  url('//at.alicdn.com/t/font_862105_62svitrr35.woff') format('woff'),
  url('//at.alicdn.com/t/font_862105_62svitrr35.ttf') format('truetype'),
  url('//at.alicdn.com/t/font_862105_62svitrr35.svg#iconfont') format('svg');
}

```

**4.封装Icon组件**
```jsx
import React, { useMemo } from 'react'
import Taro from '@tarojs/taro'
import { Text } from '@tarojs/components'
import classNames from 'classnames'

interface IIcon {
  name: string
  size?: number
  color?: string
  className?: string[] | string
  style?: React.CSSProperties
  onClick?: (e) => void
}

/**
 * name：图标
 */
const Icon: React.FC<IIcon> = (props) => {
  const { name, size = 16, color, className, style, onClick } = props

  const handleClick = (e) => {
    onClick && onClick(e)
  }

  const iconStyle = useMemo(
    () => ({
      fontSize: Taro.pxTransform(size * 2),
      color
    }),
    [size, color]
  )

  return (
    <Text
      className={classNames('iconfont', className, `icon${name}`)}
      style={{
        ...style,
        ...iconStyle,
        display: 'inline-block'
      }}
      onClick={handleClick}
    />
  )
}

export default Icon
```


**5.使用**
```jsx
<Icon name="arrow" color="#ddd" size={14}
```
## 
## 图片管理
图片这样的静态资源比较占用小程序的空间，非必要情况下不要直接将图片放在本地，可以先上传到远程，然后在本地进行统一管理。<br />assets/images/index.ts
```jsx
/**
 * 在vs code中安装 Image preview插件可在以下代码中实现图片的预览
 */

const images = {
  common: {
    left: 'https://tenfei03.cfp.cn/creative/vcg/veer/800water/veer-302926637.jpg',
    right: 'https://alifei01.cfp.cn/creative/vcg/veer/800water/veer-105391430.jpg'
  }
}
```

使用：
```jsx
import images from '@/assets/images'

<Image  mode="widthFix" src={images.common.left}/>
```


## 尺寸
### 尺寸单位
引用官方文档上的话：
> 在 Taro 中尺寸单位建议使用 `px`、 `百分比 %`，Taro 默认会对所有单位进行转换。在 Taro 中书写尺寸按照 1:1 的关系来进行书写，即从设计稿上量的长度 `100px`，那么尺寸书写就是 `100px`，当转成微信小程序的时候，尺寸将默认转换为 `100rpx`，当转成 H5 时将默认转换为以 `rem` 为单位的值。
> 如果你希望部分 `px` 单位不被转换成 `rpx` 或者 `rem` ，最简单的做法就是在 px 单位中增加一个大写字母，例如 `Px` 或者 `PX` 这样，则会被转换插件忽略。
> 结合过往的开发经验，Taro 默认以 `750px` 作为换算尺寸标准，如果设计稿不是以 `750px` 为标准，则需要在项目配置 `config/index.js` 中进行设置，例如设计稿尺寸是 `640px`，则需要修改项目配置 `config/index.js` 中的 `designWidth` 配置为 `640`
> 建议使用 Taro 时，设计稿以 iPhone 6 `750px` 作为设计尺寸标准。
> 如果你的设计稿是 `375` ，不在以上三种之中，那么你需要把 `designWidth` 配置为 `375`。


	看明白了吗？我也没看太明白！！！<br />    	以下是taro生成的模版中的配置。config/index.js：
```javascript
const config = {
  // 设计稿尺寸
  designWidth: 750,
  // 设计稿尺寸换算规则
  deviceRatio: {
    640: 2.34 / 2,
    750: 1,
    375: 2 / 1,
    828: 1.81 / 2
  }
}
```

	不改动这个配置，看看效果：
```css
.page-home {
  &__header {
    font-size: 16px;
  }
}
```

```jsx
const Home: React.FC = () => {
  return (
    <View className="page-home">
      <View className="page-home__header">这是一个标题</View>
    </View>
  )
}
```

![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406298-883674fc-3465-44dd-ad12-1bea645b5f7a.jpeg#height=53&id=YlquB&originHeight=106&originWidth=316&originalType=binary&ratio=1&size=0&status=done&style=none&width=158)

按照官方的说法，100px被转换为100rpx，没问题。以iphone6为例，在小程序中的100rpx，相当于是50px，于是这里的16px，就相当于真实的8px。但是我想要的效果是从设计图上测量的是16px，代码中就写16px，而且真实大小也是16px，即32rpx，而不是16rpx。

    好，把designWidth设置成375，结果发现真实尺寸又被缩小了一倍。按照这种推理，designWidth为750的话，deviceRatio中的750就应该配置为1 / 2 。按照这种方式，达到了想要的效果。但是taro-ui又不是这样干的啊，哭！！！

怎么办？让设计师改尺寸？把所有尺寸放大一倍，比如16px的文字统一改为32px。那么测量的是多大，在代码中就怎么写。比如测量的是32px，就写32px，那么实际效果就是32rpx，也就是16px。但是这样我们就会失去对大小的概念。一般我们看到16px，就知道这个字实际是多大，但是标个32px，我们一看就蒙了，32px的字是多大？而且还需要把这个32px的字想象而16px 。

 那么设计还是按照传统的方式。开发时先测量是16px，然后在大脑中形成16px大小的画面，但是在代码中书写时应该写成32px。虽然每次都要乘以2不是多大个事，100以内的数都能算错，找块丑石头撞死吧！但是每次都算，每天又要多掉3根头发吧。

我们再按照另一种方式，将单位写成"Px"，那么这个时候就不会对尺寸单位进行转化，测量的是16px，写成16Px，实际大小就是16px。但是要知道将单位进行转换可以实现响应式，这样写死的话，不论是在iphone6还是在ipad上看到的字永远都是那么大。在iphone6看着正常，跑到ipad上，还是那么大，这是不能容忍的。

	说了这么多，有没有终极方案呢？没有解决办法，我也不会提这个事了。我们的目标是什么？测量的是多大-->在代码中就写成多大-->实际效果就是多大-->在不同设备还能是响应式的。而且前端能控制的环节就是写代码的时候，其实很简单，搞个函数让它帮我们处理不就好了，写成16px，自动转化成32px，不用我们每次都自己算。

assets/styles/function.scss
```css
// 尺寸大小转换
@function size($value) {
  @return $value * 2px;
}
```

home/style.scss
```css
.page-home {
  &__header {
    font-size: size(16);
  }
}
```

实际效果：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406084-630ca41a-f054-4681-8077-b31c6aaa7c28.jpeg#height=47&id=wGmRF&originHeight=94&originWidth=302&originalType=binary&ratio=1&size=0&status=done&style=none&width=151)

### 内联样式中的尺寸
在做h5项目的时候会借助“postcss-pxtorem”来进行px到rem的转换，但无法自动转换内联样式。但有时候又需要用到内联样式，一种常见的场景就是将组件的props直接用于内联样式上。可以封装一个工具方法来完成这种转换：
```javascript
/**
 * 将px转换为rem
 * @param {number} size 大小
 */
export function pxtorem(size:number) {
    const rootValue = document.documentElement.style.fontSize
    return size / parseFloat(rootValue) + 'rem'
}
```
 <br />在Taro项目中也一样，而且Taro已经封装了一个叫做pxTransform()的方法，在小程序中将单位转换为rpx，在h5中转换为rem。好，我们看看用着怎么样：
```jsx
<View style={{ fontSize: Taro.pxTransform(16) }}>这是一个标题</View>
```

![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164407548-202119bf-fe76-4fa2-ab0e-7850fbdbb48d.jpeg#height=45&id=UaTJI&originHeight=90&originWidth=288&originalType=binary&ratio=1&size=0&status=done&style=none&width=144)<br />	不是我想要的效果诶！因为16px被转换成了16rpx，所以实际显示效果是8px。看下源码：
```javascript
function pxTransform(size) {
  var _ref = this.config || {},
      _ref$designWidth = _ref.designWidth,
      designWidth = _ref$designWidth === void 0 ? 750 : _ref$designWidth,
      _ref$deviceRatio = _ref.deviceRatio,
      deviceRatio = _ref$deviceRatio === void 0 ? {
    640: 2.34 / 2,
    750: 1,
    828: 1.81 / 2
  } : _ref$deviceRatio;

  if (!(designWidth in deviceRatio)) {
    throw new Error("deviceRatio \u914D\u7F6E\u4E2D\u4E0D\u5B58\u5728 ".concat(designWidth, " \u7684\u8BBE\u7F6E\uFF01"));
  }

  return parseInt(size, 10) / deviceRatio[designWidth] + 'rpx';
}
```

	这里取了配置中的参数进行计算。但是又不好去改它配置中的deviceRatio。去看看Taro自己是怎么用的吧，在taro-ui中发现又单独写了一个pxTransform方法。看到这里边把designWidth直接固定为750，deviceRatio为1。这也就印证了之前的一种尝试中将deviceRatio改为1 / 2之后和taro-ui的效果不一样了。
```javascript
function pxTransform(size) {
    if (!size)
        return '';
    const designWidth = 750;
    const deviceRatio = {
        640: 2.34 / 2,
        750: 1,
        828: 1.81 / 2
    };
    return `${size / deviceRatio[designWidth]}rpx`;
}
```

	再看taro-ui中对于该方法的使用，在icon组件中：
```javascript
 const rootStyle = {  fontSize: `${pxTransform(parseInt(String(size)) * 2)}`, color }
```
还是需要手动乘以2。所以我们在使用Taro.pxTransform()方法也是需要手动乘以2的：
```jsx
<View style={{ fontSize: Taro.pxTransform(16 * 2) }}>这是一个标题</View>
```

![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406625-5482e163-9c60-4088-87c8-b3d74ffff7db.jpeg#height=48&id=cw517&originHeight=96&originWidth=274&originalType=binary&ratio=1&size=0&status=done&style=none&width=137)

## 分包
众所周知，一个小程序的项目不能超过2M。小程序的核心就是"小"，初衷是集成一些轻巧的应用。当然用小程序原生方式一般不会超过这个限制。但是我们使用Taro、React、TypeScript这种在web项目中的工程化方式来开发就有点扯了，很可能一行代码还没写，单单把依赖装起来就超标了。<br />不过微信也早就意识到了这种限制确实有点扯。于是推出了分包机制。简单来说就是可以把整个项目划分为很多个模块，只有在进入到某个模块的时候才去加载。这也就是为什么有时候打开一个页面的时候会一闪而过一个“模块正在加载中”的提示窗。虽然公用的一些模块还是会被构建到主包中，但是聊胜于无吧，也能够从主包中分出约200k的资源出来。<br />	按照官方文档中的说法。假设支持分包的小程序目录结构如下：
```jsx
├── app.js
├── app.json
├── app.wxss
├── packageA
│   └── pages
│       ├── cat
│       └── dog
├── packageB
│   └── pages
│       ├── apple
│       └── banana
├── pages
│   ├── index
│   └── logs
└── utils
```
在 app.json的 subpackages字段声明项目分包结构:
```javascript
{
  "pages":[  "pages/index",  "pages/logs" ],
  "subpackages": [
    {
      "root": "packageA",
      "pages": [  "pages/cat",  "pages/dog"  ]
    }, {
      "root": "packageB",
      "pages": [
        "pages/apple",
        "pages/banana"
      ]
    }
  ]
}
```
这里pages目录为主包，packageA、packageB为分包目录。官方都这样说了，这样划分当然是可以的。但是有没有更好的划分方式呢？先提出两个问题：<br />(1)开发者在最初开发时没有预估到项目会超标，等超标的时候才去做分包，那么之前在各个地方进行页面跳转时的url都需要修改。<br />(2)在前端项目中，页面都会统一放在pages中，这里直接在src源码目录中又多出一些分包目录。而分包目录中又要有一个pages来放该分包下的页面。总感觉乱乱的，很别扭。

基于这两点思考， 能不能在不改动项目结构的条件下进行分包呢？
```
├── app.js
├── app.json
├── app.wxss
├── pages
│   ├── index
│   ├── logs
│   ├── animal
│      ├── cat
│      └── dog
│   ├── fruit
│      ├── apple
│      └── banana
└── utils
```
项目结构不动，那么就不需要修改代码中进行页面跳转时的url，划分方式还是按照我们习惯的来。只需要配置分包就好：
```json
{
  pages: ['pages/index/index', 'pages/logs/logs'],
  subPackages: [
    {
      root: 'pages/animal',
      pages: ['cat', 'dog']
    },
    {
      root: 'pages/fruit',
      pages: ['apple', 'banana']
    }
  ]
}
```
项目结构是不是更清晰呢？

## Promise
我们经常会写这样的代码：
```javascript
// 获取店铺信息
useEffect(() => {
  setLoading(true)
  getShopInfo()
    .then((res) => {
      setShopInfo(res.shopInfo)
    })
    .catch((err) => {
      Taro.showToast({
        title: err.msg || '获取店铺信息失败',
        icon: 'none'
      })
    })
    .finally(() => {
      setLoading(false)
    })
}, [])
```

	开发过程中一切正常，在安卓手机上看也没问题。但是当我拿到iphone上看时就出问题了，那个loading的圈怎么一直在转？明明最后设置取消了啊！回到问题本身，loading没取消当然是finally没执行，因为finally算是一个比较新的api，在低版本的安卓系统以及ios平台上可能会存在兼容性问题。在ios小程序的控制台中也可以看到错误信息：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406216-9ef3c69f-d529-4df1-9fab-6f1cb657cf94.jpeg#height=45&id=q3bdi&originHeight=45&originWidth=380&originalType=binary&ratio=1&size=0&status=done&style=none&width=380)<br />有以下下几种解决方式。

### 扩展finally方法
	在根组件app.tsx中:
```javascript
Promise.prototype.finally = function (callback: () => void) {
  let P = this.constructor
  return this.then(
    (value) => {
      P.resolve(callback()).then(() => value)
    },
    (reason) => {
      P.resolve(callback()).then(() => {
        throw reason
      })
    }
  )
}
```
### 
### 使用then代替finally
```javascript
getShopInfo()
.then((res) => {})
.catch((err) => {})
.then(() => {
  setLoading(false)
})
```
参考[api.get(…).then(…).catch(…).finally is not a function](https://stackoverflow.com/questions/57883187/api-get-then-catch-finally-is-not-a-function)。

### 使用async、await
使用这种方式还有比较好的一点就是代码更易读。如果在then回调中又需要进行其他异步操作，很容易在代码中产生“回调地狱”。
```javascript
getShopInfo()
  .then((shop) => {
    getGoodsList()
      .then((goods) => {})
      .catch((err) => {})
  		.fillany(()=>{})
  })
  .catch((err) => {})
  .fillany(()=>{})
```

```javascript
try {
  const shop = await getShopInfo()
} catch (err) {
  try {
     const goods = await getGoodsList()
  } catch (err) {

  } finally { }
} finally { }
```
这里没参杂业务逻辑，当业务逻辑加进来之后会发现自己写的代码自己都不想看。使用await，异步回调紧跟在异步操作之后，代码风格略清晰。

## 构建测试包
### 需求
Taro生成的package.json中默认提供了两种构建模式，一种开发模式，一种生产模式：
```json
"scripts": {
    "dev:weapp": "npm run build:weapp -- --watch",
    "build:weapp": "taro build --type weapp"
 }
```
如果想要在手机上预览，可在微信开发者工具上生成一个二维码，扫码即可在手机上查看，这时会出现资源大小超出限制的提示：

![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406495-75cfba0f-7a30-4aa7-8104-4fccdda25f66.jpeg#height=70&id=B670X&originHeight=139&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=340)<br />因为这个包是在开发模式下生成的，代码没有经过压缩处理。于是构建生产模式，则资源大小降低到了2M以内。<br /> 同时我们会在应用配置中根据构建模式的不同，来分配不同的域名，开发模式需要使用测试服，而生产模式使用的是正式服：<br />src/config.ts
```javascript
//基础配置
const CONFIG_BASE = {}

// 开发配置
// 因为在开发时直接使用的测试服，所以这里配置的是测试服域名。如果要使用本地服务，可改为本地代理的域名。
const CONFIG_DEV = {
  domain: 'http://cgtest.senguo.me'
}

// 生产配置
const CONFIG_PRO = {
  domain: 'https://cg.senguo.cc'
}

const ENV_CONFIG_MAP = {
  development: CONFIG_DEV,
  production: CONFIG_PRO
}
export default { ...CONFIG_BASE, ...ENV_CONFIG_MAP[process.env.NODE_ENV] }
```
预览版本当然不能是正式服，所以需要手动将CONFIG_PRO中的域名修改为测试服：
```javascript
// 生产配置
const CONFIG_PRO = {
  // domain: 'https://cg.senguo.cc'
  domain: 'http://cgtest.senguo.me'
}
```
这样就能以生产模式构建，而且还可以使用测试服，正是我们想要的结果。但是这种做法要靠人工去控制，实际上是非常危险的。如果到了需要正式发布的时候，忘记把域名改回来，发布了个测试服的出去，估计要被拉去祭天，去陪之前的那位暴风程序员了。

### 增加测试构建模式的尝试
既然需要使用生产模式构建，而域名却使用测试服，同时还不用每次手动修改。那么可以增加一条测试命令。<br />package.json
```json
"scripts": {
   "dev:weapp": "npm run build:weapp -- --watch",
   "test:weapp": "taro build --type weapp --env test",
   "build:weapp": "taro build --type weapp"
 }
```

在编译配置中增加测试配置：<br />/config/test.ts
```javascript
module.exports = {
  env: {
    NODE_ENV: '"test"',
  }
}
```

/config/index.ts
```javascript
module.exports = function (merge) {
  const env = process.env.NODE_ENV
  if (env === 'development') {
    return merge({}, config, require('./dev'))
  } else if (env === 'test') {
    return merge({}, config, require('./test'))
  } else {
    return merge({}, config, require('./prod'))
  }
}
```

	在应用配置中，根据不同的node环境变量来决定使用哪种域名：<br />src/config.ts
```javascript
//基础配置
const CONFIG_BASE = {}

// 开发配置
const CONFIG_DEV = {
  domain: 'http://cgtest.senguo.me'
}

// 测试配置
const CONFIG_TEST = {
  domain: 'http://cgtest.senguo.me'
}

// 生产配置
const CONFIG_PRO = {
  domain: 'https://cg.senguo.cc'
}

const ENV_CONFIG_MAP = {
  development: CONFIG_DEV,
  test: CONFIG_TEST,
  production: CONFIG_PRO
}

export default { ...CONFIG_BASE, ...ENV_CONFIG_MAP[process.env.NODE_ENV] }
```

	好了，执行yarn test:weapp，报错了，而且这个错误非常莫名其妙，之前在使用Taro生成的mobx模版中也会出现这个错误：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164409068-75f76754-51cb-4588-9c88-09b66cc7c809.jpeg#height=108&id=a3odv&originHeight=216&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=340)

 再解析一下 "taro build --type weapp --env test"这条命本身，执行taro build --type weapp，环境变量使用的是production，这里又将它改成了test，Taro内部可能还需要这个变量去做其他的事情，而不是仅仅暴露出来让我们能够根据它的不同来进行一些判断处理，感兴趣的可以去研究一下taro-cli的源码。所以应该是不能随意去改动这个配置的。

### 指定编译参数
	在之前的尝试中一直有个东西在眼前晃来晃去，没错，就是这个process，好像不用它的话就解决不了问题，那就拿他开刀。<br />	process是Node环境中的一个全局对象，而它拥有一个叫做argv的属性， argv属性会返回一个数组，其中包含当 Node.js 进程被启动时传入的命令行参数。那么是否可以通过命令参数来判断当前构建的模式呢？

package.json
```json
"scripts": {
  	"dev:weapp": "npm run build:weapp -- --watch --mode development",
  	"test:weapp": "taro build --type weapp --mode test",
    "build:weapp": "taro build --type weapp --mode production"
}
```

输出argv看看有没有出现传入的参数。由于使用了TypeScript，所以需要先定义一下argv。<br />global.d.ts
```typescript
// @ts-ignore
declare const process: {
  argv: string[];
  env: {
    TARO_ENV: 'weapp' | 'swan' | 'alipay' | 'h5' | 'rn' | 'tt' | 'quickapp' | 'qq' | 'jd';
    [key: string]: any;
  }
}
```

/config/index.ts
```javascript
console.log(process.argv)
```

![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406470-92ea1409-364d-4cd3-a62d-71e86302b2e7.jpeg#height=105&id=iMxdd&originHeight=209&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=340)<br />这里看到了传入的参数，现在问题是如何获取这个参数呢？当然可以通过该数组中"--mode"后边的这一项来获取。或者通过判断该数组中是否存在传入的参数来知道当前的构建模式：
```javascript
if(process.argv.includes['test']){
    // ...
}
```

	但是想一下，Taro又是如何从“--type weapp”中获取想要的参数的呢？在看taro-cli源码的时候，发现以下这段代码：
```javascript
const args = minimist(process.argv.slice(2), {
  alias: {
    version: ['v'],
    help: ['h']
  },
  boolean: ['version', 'help']
})
```

	这个minimist是干什么的？它是一个用来解析命令行选项的库。输出来就很直观的看到了传入的参数：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406655-91cb6b3d-0eae-4582-8eb9-b4b1aa5d5475.jpeg#height=19&id=RkXjG&originHeight=38&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=340)

/config/index.ts
```javascript
const minimist = require("minimist");

module.exports = function (merge) {
  const args = minimist(process.argv.slice(2))
  const { mode } = args
  if (mode === 'development') {
    return merge({}, config, require('./dev'))
  } else if (mode === 'test') {
    return merge({}, config, require('./test'))
  } else {
    return merge({}, config, require('./prod'))
  }
}
```

	执行yarn test:weapp，这样即使用生产模式构建了项目，又使用了测试服的域名。于是可以愉快的扫码预览啦！如果要正式发布，只需要执行yarn build:weapp，这样就不需要手动将域名修改为正式服。

	别急，还没完，还没验证开发模式呢。执行yarn dev:weapp，纳尼！怎么构建的是正式服？看下编译参数，竟然出现了两个模式：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164405919-73610441-4507-4203-8f4a-c7b871d7047a.jpeg#height=87&id=mRMoH&originHeight=174&originWidth=584&originalType=binary&ratio=1&size=0&status=done&style=none&width=292)

	再看执行yarn dev:weapp时真正执行的命令：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164407049-1b64f406-a9e4-4e0b-af05-e2cf19c3ee81.jpeg#height=20&id=ZnohL&originHeight=20&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=680)<br />	从这里可以看到第一个mode是taro传入的，而第二个mode并没有覆盖掉第一个。于是修改命令：
```json
"scripts": {
 		"dev:weapp": "taro build --type weapp --mode development --watch",
}
```
这下终于大功告成了。

## 版本更新
按照微信官方的说法：“最差情况下，也在发布之后24小时之内下发新版本信息到用户”。但如果微信还没来得及下发新版本，而且用户打开小程序时属于热启动，那么此时仍然使用的是旧版本。<br />可以在启动小程序时进行版本检查，如果当前小程序不是最新，则用户点击提示模态窗的确认按钮之后自动进行更新。<br />app.tsx
```javascript
class App extends React.Component {
  componentDidShow() {
    this.updateApp()
  }

  // 版本更新
  private updateApp = () => {
    const updateManager = Taro.getUpdateManager()
    updateManager.onCheckForUpdate(function (res) {
      if (!res.hasUpdate) return

      updateManager.onUpdateReady(function () {
        Taro.showModal({
          title: '更新提示',
          content: '新版本已经准备好，是否重启应用？',
          success(res) {
            if (res.confirm) {
              updateManager.applyUpdate()
            }
          }
        })
      })

      updateManager.onUpdateFailed(function () {
        Taro.showModal({
          title: '已经有新版本',
          content: '新版本已上线，请您删除当前小程序，重新搜索打开。'
        })
      })
    })
  }
}

export default App
```


## wx-f2
关于图表库，老大哥当属[ECharts](https://echarts.apache.org/zh/index.html)，但是自从接触到了[AntV](https://antv.vision/zh)，就被它深深吸引了。ECharts像是"大叔"，Antv像"小鲜肉"，不过小鲜肉只是表面的“鲜”，实际上也是一个非常成熟的产品。

	以下是AntV几款常用的产品：

- [G2](https://antv.vision/zh)：基础图表库，对于前端技术栈没有限制，开发者可根据所使用的技术以及业务进行灵活的封装。
- [G6](https://g6.antv.vision/zh)：适用于脑图、树形图等图分析或图编辑器应用。
- [F2](https://f2.antv.vision/zh)：移动端图表库。
- [F7](https://l7.antv.vision/zh)：地理空间图表库。
- [BizCharts](https://bizcharts.net/product/BizCharts4/gallery)：G2的React封装。
- [BizGoblin](https://bizcharts.net/product/bizgoblin/gallery)：F2的React封装。<br />     咋一看，好像没有适合小程序使用的产品。既然有了F2，那么再稍稍改造，wx-f2就产生了。[聊一聊F2与小程序](https://www.yuque.com/antv/blog/bg9sxf)。虽然在Taro中使用产生了一些问题，但仍然抵挡不住我对它的喜欢。而且基于ECharts封装的ec-canvas大小为450k，而wx-f2只有230k，就为了画几个图，占用了1/4的资源，导致项目大小直接超标了。下面就聊一聊wx-f2的使用，以及遇到的一些问题。

### 引入
(1)[源码下载](https://github.com/antvis/wx-f2/tree/custom-components)<br />(2)将f2-canvas放到到项目中。这里放置在src目录下。
```jsx
├── f2-canvas                    // f2-canvas 组件
│   ├── f2-canvas.js
│   ├── f2-canvas.json
│   ├── f2-canvas.wxml
│   ├── f2-canvas.wxss
│   └── lib                      // f2-canvas 组件依赖的类库
│       ├── EventEmitter.min.js  // 事件发射器，用于监听、触发事件， 3.1K
│       ├── f2.js                // f2 脚本（压缩），155K
│       └── renderer.js          // f2 专为适配微信小程序绘图上下文 context 而封装的伪 Canvas
├── pages                        // f2-canvas 组件使用示例
```

### 使用
    在pages/home中使用。<br />(1)页面配置中指定所要使用的组件<br />home.config.ts:
```javascript
export default {
  navigationBarTitleText: '首页',
  navigationBarTextStyle: 'white',
  usingComponents: {
    'ff-canvas': '../../f2-canvas/f2-canvas'
  }
}
```

(2)绘制图表<br />Home.tsx:
```jsx
import React, { useState, useEffect } from 'react'
import { View } from '@tarojs/components'
import F2 from '../../f2-canvas/lib/f2'
import './style.scss'

interface IItem {
  day: string
  value: number
}

const Home: React.FC = () => {
  // 图表数据
  const [data, setData] = useState<IItem[]>([])
  // 图表
  const [chart, setChart] = useState<any>(null)

  // 获取图表数据
  useEffect(() => {
    setData([
      {
        day: '周一',
        value: 300
      },
      {
        day: '周二',
        value: 400
      },
      {
        day: '周三',
        value: 350
      },
      {
        day: '周四',
        value: 500
      },
      {
        day: '周五',
        value: 490
      },
      {
        day: '周六',
        value: 600
      },
      {
        day: '周日',
        value: 900
      }
    ])
  }, [])

  // 数据改变之后刷新图表
  useEffect(() => {
    // 如果图表初始创建之前就拿到了数据，那么图表创建时就会使用最终的数据。
    // 如果图表创建的时候还没拿到数据，那么拿到数据之后再调用该方法。
    if (!chart) return
    chart.changeData(data)
  }, [data])

  const chartOpts = {
    onInit(canvas, width, height) {
      const chart = new F2.Chart({
        el: canvas,
        width,
        height,
        padding: [50, 10, 30, 30]
      })
      
      chart.source(data, {
        value: {
          alias: '访问量'
        }
      })
      
      chart.tooltip({
        showTitle: true,
        showCrosshairs: true,
        showItemMarker: true
      })
      
      chart.line().position('day*value').color('#009688').shape('smooth')
      chart.render()
      setChart(chart)
      return chart
    }
  }

  return (
    <View >
      {/* @ts-ignore */}
      <ff-canvas canvas-id="f2-canvas" opts={chartOpts}></ff-canvas>
    </View>
  )
}

export default Home
```

### getcomputedstyle错误
    好吧，一上来就报错了：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164407552-0414df54-3fb9-46ba-b016-e6f58adea76c.jpeg#height=34&id=m6Zi1&originHeight=68&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=340)<br />    	getcomputedstyle()方法是属于window对象的，小程序中没有window对象，那怎么办？我们去源码中看看，于是在f2.js中发现这行代码：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164405862-f26fe115-4b2d-427c-9e23-5d801ac4b613.jpeg#height=16&id=jlA5x&originHeight=16&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=680)

	意思就是先获取元素的样式，如果获取不到就会去调用getcomputedstyle方法。那么怎么才能获取到样式呢，当然是为它添加样式，而且看到官方文档中也提出了类似的问题：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406369-9c510809-0655-4f35-80d6-da366af0b921.jpeg#height=254&id=KbuWM&originHeight=338&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=510)

 于是开心地为组件添加样式：
```css
.chart {
  width: 100%;
  height: size(200);
}
```

```jsx
<ff-canvas class="chart" canvas-id="f2-canvas" opts={chartOpts}></ff-canvas>
```
嗯哼，依然报错。暂时不知道怎么搞了。去官方仓库的issues下看看吧。看到一条问题中提到了在Taro中使用产生的问题，但是并没有提到getcomputedstyle的报错，难道他的代码不会产生这个问题吗？于是照着上边截图中的代码敲了一遍。果然没有出现getcomputedstyle报错。对比了下不同，发现他的宽高样式定义在父元素上：
```jsx
<View className="chart" >
  {/* @ts-ignore */}
 <ff-canvas canvas-id="f2-canvas" opts={chartOpts}></ff-canvas>
</View>
```

    	后来在[《在小程序上渲染F2》](https://www.yuque.com/antv/f2/miniprogram)这篇文章中，也有网友提出了这种问题的解决方式：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164407098-31cdcd6f-6130-4ae5-a9d1-cc7023066ff4.jpeg#height=160&id=N3Hrf&originHeight=213&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=510)

### addListener报错
	解决一个问题，又迎来了新的问题：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164406681-c8d97715-947b-4a80-9613-6cb1be5b93f1.jpeg#height=42&id=tlakG&originHeight=84&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=340)<br />	在源码中的EventEmitter.min.js中找到了相关联的东西，但是min文件是经过压缩了的，看不太明白。在《聊一聊F2与小程序》中，看到说使用了 [wolfy87-eventemitter](https://github.com/Olical/EventEmitter) ，于是将源码下载下来，看EventEmitter.js中的东西：
```javascript
    proto.addListener = function addListener(evt, listener) {
        if (!isValidListener(listener)) {
            throw new TypeError('listener must be a function');
        }

        var listeners = this.getListenersAsObject(evt);
        var listenerIsWrapped = typeof listener === 'object';
        var key;

        for (key in listeners) {
            if (listeners.hasOwnProperty(key) && indexOfListener(listeners[key], listener) === -1) {
                listeners[key].push(listenerIsWrapped ? listener : {
                    listener: listener,
                    once: false
                });
            }
        }

        return this;
    };
```
	并没什么用，依然没看出名堂。<br />  <br />	既然报错中说"source.addListener is not a function"，那么这个错误是不是和source有关。再次回到《聊一聊F2与小程序》，看到作者的封装思路中提到了以下事件覆盖，这里边不就有source这个字眼吗：
```javascript
// source 即为我们封装的 Renderer 伪 Canvas 对象
F2.Util.addEventListener = function (source, type, listener) {
  source.addListener(type, listener);
};

F2.Util.removeEventListener = function (source, type, listener) {
  source.removeListener(type, listener);
};
```

	在源码中的f2-canvas.js文件中找到了这段代码，并打印出source，看到了比较奇怪的事情，TaroDocument竟然乱入了：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164407220-de0ab440-7f87-475e-9e36-0736af586173.jpeg#height=120&id=DFlXO&originHeight=120&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=680)<br />都是这个TaroDocument搞的鬼。怎么办？需要判断source是Renderer就添加事件，不是就不添加，感觉有点麻烦，直接对错误进行捕获吧！于是修改源码。f2-canvas.js:
```javascript
// 适配小程序的事件机制
F2.Util.addEventListener = function (source, type, listener) {
  try {
    source.addListener(type, listener)
  } catch (err) {}
};

F2.Util.removeEventListener = function (source, type, listener) {
  try {
    source.removeListener(type, listener)
  } catch (err) {}
};
```
     保存、编译，嘿嘿！心心念念的图表终于出来了。<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164408489-fa3a6c7c-b0f1-46a6-9c90-a83a193cbd98.jpeg#height=173&id=rAxXw&originHeight=345&originWidth=680&originalType=binary&ratio=1&size=0&status=done&style=none&width=340)

## 其他问题
### MiniCssExtractPlugin
	项目出现警告信息：“WARNING in chunk common [mini-css-extract-plugin]Conflicting order between:...”.大概意思是在不同页面中引入的自定义组件顺序不一致造成的。如果一个页面只引入base组件，那么只需要按组件名进行有序的导入即可，问题是页面还需要引入属于本模块内的组件，而模块内的组件又引入的有base组件。这种嵌套顺序是无法控制的。实际使用上组件引入顺序的不一致没有造成什么问题。需要在Taro的编译配置中忽略这个规则。

config/index.js
```javascript
const config = {
  mini: {
    miniCssExtractPluginOption: {
      ignoreOrder: true
    }
  }
}
```

### taro-ui版本问题
	目前使用的taro版本为3.0+，安装taro-ui之后运行出现以下错误：<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164408600-851d6aa3-cb29-4198-bca2-df10b9170bd3.jpeg#height=124&id=a49X3&originHeight=124&originWidth=1898&originalType=binary&ratio=1&size=0&status=done&style=none&width=1898)

去taro-ui源码中查看，确实不存在weapp这个目录。后来发现默认安装的版本号为“2.3.4”，一般一个技术生态大版本号都是对应的，而且看官方issue上一堆人都提出了类似的问题。这里需要安装最新版本的taro-ui：
```
yarn add taro-ui@3.0.0-alpha.3
```
虽然还是个alpha版的，而且有的组件还是有问题，有问题的组件就只能自己封装了。
### 
### 自定义toast图标
	当微信内置的图标无法满足UI需求的时候，可以为 toast传入一个自定义的图标。一般我们会将这个图标放在assets/images中，但是使用的时候，却无法正常显示。

	如果使用小程序原生方式开发，这样做是没问题的。但是在Taro中为什么不行呢？因为这种小图片在经url-loader处理之后被转换成了base64，如果去dist的img目录中查看，是不存在这个图片的。而且toast又是小程序的原生组件，在toast中使用图片跟在Image这种组件中使用是不同的。那么这里的图标就不能让url-loader处理。

	首先需要将资源放在static目录中。src/static/images/toast<br />	然后在编译配置中指定copy。/config/index.ts
```javascript
const config = {
  copy: {
    patterns: [{ from: 'src/static', to: 'dist/static' }],
    options: {}
  }
}
```

	最后使用的时候以dist中的相对路径为准：
```javascript
Taro.showToast({
  title: '操作成功',
  image: '../../static/images/toast/success.png'
})
```

	虽然图标最后显示出来了，但是会发现偏小，而且无论原图多大，最终显示出来的大小始终是固定的。微信也没有暴露可以设置图标大小的接口。所以如果遇到这种需要自定义toast图标的需求，最好和设计沟通，尽量使用微信原生提供的东西。不按照微信的风格来，很多东西如果都要自定义的话，不仅开发繁琐，而且效果也不尽人意。<br />![](https://cdn.nlark.com/yuque/0/2020/jpeg/1561110/1602164405707-40c5a3c5-91e5-421a-971f-d83abee7c887.jpeg#height=62&id=jeua4&originHeight=246&originWidth=248&originalType=binary&ratio=1&size=0&status=done&style=none&width=62)

### 日期格式化问题
	这个问题主要会出现在ios平台上。比如想格式化如下日期:
```javascript
const date = '08-15'
dayjs(date).format('MM-DD')
```
ios上就会返回“invalid date”。因为“08-15”算不上是一个标准格式的日期。所以原始日期尽量使用标准一点的：
```javascript
const date = '2020-08-15'
dayjs(date).format('MM-DD')
```


	 在ios上查看一个横坐标为月份的图表时，发现图表没出来。图表原始数据如下：
```javascript
const data = [{
  date:'2020-08',
  money:1000
}]
```

	图表定义：
```javascript
chart.source(data, {
  date: {
    alias: '日期',
    type: 'timeCat',
    range: [0, 1]
  },
  money: {
    alias: '金额'
  }
})
```

	这里实在是有点不地道，连"YYYY-MM"这种格式的都处理不了。传入标准的日期：
```javascript
const data = [{
  date: dayjs('2020-08').format('YYYY-MM-DD'),
  money:1000
}]
```

	创建图表的时候才对日期进行格式化：
```javascript
chart.source(data, {
  date: {
    alias: '日期',
    type: 'timeCat',
    mask: 'YYYY-MM',
    range: [0, 1]
  },
  money: {
    alias: '金额'
  }
})
```


### 项目大小问题
如果发现项目超标，很可能是使用了taro-ui。 <br />taro-ui非常占用项目空间，使用webpack-bundle-analyzer分析显示其本身大小为360k，但它还会依赖其他包，移除之后项目空间减少了1M+。而且其本身使用的场景非常有限，几乎没有组件能够满足小程序业务和UI需求。如果有强需求要用到某个组件，可以直接将对应的组件移植到项目中，放在components/taro-ui中，而不是安装整个包。
