在webpack使用过程中比较容易出现打包速度慢，包体积大的问题，严重影响开发效率。那么主要就从这两个问题出发来研究一下如何进行性能上的一些优化，避免产生这样的问题。

[谷歌官方web开发](https://web.dev/learn/)



# 打包量化分析
为了对比进行优化之前和之后的效果，可以借助一些工具对整个打包过程的时间消耗和打包之后的资源体积进行量化分析。

## 打包速度分析
使用speed-measure-webpack-plugin 插件来分析打包速度，它可以测量webpack打包过程中各个插件和loader运行所花费的时间。
```javascript
const SpeedMeasurePlugin = require('speed-measure-webpack-plugin')
const smp = new SpeedMeasurePlugin()

module.exports =smp.wrap({
    plugins:[],
    module: {
        rules: [
            {
                test: /\.js$/,
                use: 'babel-loader',
                exclude: /node_modules/
            }
        ]
    }
}) 
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628000387371-559ac8c2-4b28-464e-aee8-568ba1182c8b.png#averageHue=%23403e41&clientId=u1a906918-254d-4&from=paste&height=183&id=uff91914a&originHeight=730&originWidth=664&originalType=binary&ratio=1&rotation=0&showTitle=false&size=106149&status=done&style=none&taskId=u1cbc06d4-1ec3-4100-aa41-bd17e649eb6&title=&width=166)

## 产物体积分析
使用webpack-bundle-analyzer插件来分析打包之后各个bundle文件的大小，特别针对体积比较大的bundle做优化。
```javascript
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
    plugins: [
        new BundleAnalyzerPlugin({
            analyzerMode: 'static',
            reportFilename: 'report.html'
        })
    ]
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628002212879-19f48356-dcc4-43c8-805c-494b5b4bd586.png#averageHue=%23ebebea&clientId=u1a906918-254d-4&from=paste&height=239&id=uf5000b84&originHeight=1336&originWidth=2546&originalType=binary&ratio=1&rotation=0&showTitle=false&size=566833&status=done&style=none&taskId=u0d831943-faee-43e1-95e8-68cbe346f03&title=&width=455)

比如这里使用的图表库就非常的大，就可以针对性地对其进行优化，比如按需引入，使用CDN的方式使用。

# 打包速度优化
## 缩小babel-loader的处理范围
尽可能的让babel-loader处理更少的文件，可以只用编译src中的文件，同时排除node_modules:
```javascript
const path = require('path')
const resolve = dir => path.resolve(process.cwd(), dir)

module.exports = {
	module: {
		rules: [
			{
				test: /\.js$/,
				use: 'babel-loader',
				include: resolve('src'),
				exclude: /node_modules/
			}
		]
	}
}
```

## 缓存loader处理结果
### babel-loader
     将babel-loader 的编译结果缓存到文件系统中。之后的 webpack 构建，将会尝试读取缓存，来避免在每次执行时，可能产生的、高性能消耗的 Babel 重新编译过程。babel-loader 将会提速至少两倍。
```javascript
module.exports = {
	module: {
		rules: [
			{
				test: /\.js$/,
				use: 'babel-loader?cacheDirectory=true',
				include: resolve('src'),
				exclude: /node_modules/
			}
		]
	}
}
```

### cache-loader
cache-loader可以将loader的处理结果缓存在磁盘中。
```javascript
module.exports = {
	module: {
		rules: [
			{
				test: /\.js$/,
				use: ['cache-loader', 'babel-loader'],
				include: path.resolve('src'),
				exclude: /node_modules/
			}
		]
	}
}
```

- **_保存和读取这些缓存文件会有一些时间开销，只需要对性能开销较大的 loader。_**
- **_推荐使用cache-loader针对不同的loader处理结果进行缓存。_**

## 多线程编译
因为Node是单线程运行的，所以 Webpack 在打包的过程中也是单线程的，特别是在执行 Loader 的时候，长时间编译的任务很多，这样就会导致等待的情况。可以使用一些方法将 Loader 的同步执行转换为并行，充分利用系统资源来提高打包速度。<br />使用thread-loader在特定 loader 的编译过程中，以开启多线程的方式来加速编译。
```javascript
module.exports = {
	module: {
		rules: [
			{
				test: /\.js$/,
				use: ['thread-loader', 'cache-loader', 'babel-loader'],
				include: path.resolve('src'),
				exclude: /node_modules/
			}
		]
	}
}
```
<br />

- **_仅在耗时的操作中使用此 loader。_**

## 多进程压缩
webpack默认使用UglifyJS 来压缩代码，但是这个是单进程程运行的，也就是说多个js文件需要被压缩，它需要一个个文件进行压缩。为了加快速度，可以使用 webpack-parallel-uglify-plugin 插件并行运行UglifyJS对代码进行压缩。<br />当然不止可以压缩JS 代码，还可以压缩HTML、CSS 代码，并且在压缩 JS 代码的过程中，还可以通过配置实现比如删除 console.log 这类代码的功能。
```javascript
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin')

module.exports = {
    plugins: [
        new ParallelUglifyPlugin({
            uglifyJs:{
                output:{
                    beautify: false, // 不需要格式化
                    comments: false, // 不保留注释
                }，
                compress:{
                    warnings: false,     // 在Uglify]s除没有用到的代码时不输出警告
                    drop_console: true,  // 删除所有的console语句，可以兼容ie浏览器
                    collapse_vars: true, // 内嵌定义了但是只用到一次的变量
                    reduce_vars: true,    // 取出出现多次但是没有定义成变量去引用的静态值
                }
            }，
        })
    ]
}
```

## 代码分割
代码分割能够把代码分离到不同的bundle中，然后可以按需加载或并行加载这些文件。可以用于获取更小的bundle，以及控制资源加载优先级。<br />代码分割主要出于两种原因：一是减少代码重复，比如在多页面的应用中，多个页面都同时引用了某些module的情况；二是支持缓存，比如第三方库通常是不会怎么变的，将他们单独抽离出来有利于浏览器缓存。具体来讲，在做分割时，通常会针对以下情况进行处理：

-  为webpack自己的runtime代码（manifest）单独打包。
-  为第三方依赖库(vendor)单独打包。
-  为公共业务代码单独打包。
- 为异步加载的module单独打包。

### 文件命名
给打包生成的js文件命名，方便确认打包优化之后的效果。
#### output.filename
此选项给打包后的入口文件命名。
```javascript
module.exports = {
	output: {
		filename: 'js/[name].[contenthash:6].js'
	}
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628009115514-1673d312-b868-4b76-9e05-ccc662a3f1f3.png#averageHue=%23292629&clientId=u1a906918-254d-4&from=paste&height=23&id=uf8301248&originHeight=46&originWidth=204&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2934&status=done&style=none&taskId=u13f3cf47-98e5-4946-8948-93c35ad3bf9&title=&width=102)![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628009057923-a454ff38-4991-4b06-b014-807c0d2351a7.png#averageHue=%23272427&clientId=u1a906918-254d-4&from=paste&height=23&id=u9adc47e9&originHeight=46&originWidth=284&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3557&status=done&style=none&taskId=u5d09a2ac-a574-4071-931d-6178e78ad3b&title=&width=142)
#### output.chunkFilename
此选项给打包后的非入口文件命名。
```javascript
module.exports = {
	output: {
		chunkFilename: 'js/[name].[contenthash:6].js'
	}
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628009245279-d34a3ffd-81be-49dc-9502-c0baafc2abc0.png#averageHue=%23272427&clientId=u1a906918-254d-4&from=paste&height=92&id=ufef25e3c&originHeight=184&originWidth=368&originalType=binary&ratio=1&rotation=0&showTitle=false&size=16786&status=done&style=none&taskId=u47e39d16-9f5b-4858-8040-b259cd565cd&title=&width=184)

#### webpackChunkName
指定每个业务模块的名称。可在路由懒加载中使用。<br />React中：
```javascript
import { lazy } from 'react'

const ArticleList = lazy(() => import(/* webpackChunkName:'article-list' */ '@/pages/article/list'))
```

Vue中：
```javascript
const ArticleList = () => import(/* webpackChunkName:'articleList' */ '@/pages/article/list')
```

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628009552357-dbb3a717-8dab-4816-9967-ade3440ce047.png#averageHue=%23282528&clientId=u1a906918-254d-4&from=paste&height=41&id=wMn5g&originHeight=82&originWidth=328&originalType=binary&ratio=1&rotation=0&showTitle=false&size=8265&status=done&style=none&taskId=u9647a2cb-e4c7-46f9-a7df-01b227c125e&title=&width=164)

### webpack runtime单独打包
```javascript
module.exports = {
	optimization: {
		// 分离出webpack本身的runtime代码
		runtimeChunk: {
			name: 'manifest'
		}
	
```
    此时，webpack会为runtime代码单独生成名为manifest-*.js的文件。

### 第三方依赖库和公共模块单独打包
```javascript
module.exports = {
	optimization: {
		splitChunks: {
			// 防止重复。将公共的依赖模块分离到一个单独的chunk中。
			// 将所有的第三方库抽离出来。webpack默认情况下只会对异步加载的第三方库进行分割，将chunks值改为all(默认值为async)，表示对所有的第三方库进行代码分割(包括async和initial)
			chunks: 'all',
			minSize: 20000,
			minChunks: 1,
			maxAsyncRequests: 30,
			maxInitialRequests: 15,
			cacheGroups: {
				// 分离ant-design
				antd: {
					name: 'antd-vendor',
					test: /ant-design/,
					priority: 0,
					reuseExistingChunk: true
				},
				// 分离公共组件
				components: {
					name: 'components-vendor',
					test: /[\\/]src[\\/]components[\\/]/,
					priority: -10,
					reuseExistingChunk: true
				},
				assets: {
					// 分离assets
					name: 'assets-vendor',
					test: /[\\/]src[\\/]assets[\\/]/,
					minChunks: 2,
					priority: -10,
					reuseExistingChunk: true
				},
				vendor: {
					name: 'vendor',
					test: /[\\/]node_modules[\\/]/,
					priority: -20,
					reuseExistingChunk: true
				},
				//  比如：B-module和C-module同时依赖了D-module，但是D-module在B-module和C-module中重复存在。
				//  使用cacheGroup对于被引用次数大于等于2的module进行分割。
				common: {
					name: 'common-vendor',
					minChunks: 2,
					priority: -30,
					reuseExistingChunk: true
				}
			}
		}
	}
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628009670362-b7daca4b-f86e-4ca5-8b09-9e3c7ead6a21.png#averageHue=%23262427&clientId=u1a906918-254d-4&from=paste&height=311&id=u5407b72b&originHeight=622&originWidth=372&originalType=binary&ratio=1&rotation=0&showTitle=false&size=55532&status=done&style=none&taskId=u861eb46a-84cc-423d-a984-0158e242ccf&title=&width=186)

## 缓存打包结果
使用hard-source-webpack-plugin插件缓存构建的产物(代替dll)。初次构建需要花费正常的时间，再次构建将花费明显更少的时间。   
```javascript
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin')

module.exports = {
	plugins: [new HardSourceWebpackPlugin()]
}
```

# 产物体积优化
## 使用CDN引入第三方包
一般常用的第三方包都会发布在CDN上，因此，可以在项目中以CDN的方式加载资源，这样就不用对资源进行打包，可以大大减少打包后的文件体积。<br />以CDN方式加载资源需要使用到add-asset-html-cdn-webpack-plugin插件。<br />以CDN方式加载jQuery：
```javascript
const AddAssetHtmlCdnPlugin = require('add-asset-html-cdn-webpack-plugin')

module.exports ={
    plugins: [
        new AddAssetHtmlCdnPlugin(true,{
            'jquery':'https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js'
        })
    ],
    // 表明jquery是外部的，这样打包时就不会将jQuery进行打包了
    externals:{
      'jquery':'$'
    }
}
```
代码中import $ from jquery使用到jQuery包将不会被打包。

## 压缩js代码
该插件使用 terser 来压缩 js代码。webpack5 自带 terser-webpack-plugin。不需要安装。<br />webpack-parallel-uglify-plugin：UglifyJS 在压缩 ES5 方面做的很优秀，但在 ES6 上表现不佳。<br />terser-webpack-plugin：terser 在 ES6 上做的更好，具有跟 Uglifyjs 相同的参数，推荐使用。
```javascript
const TerserPlugin = require('terser-webpack-plugin')

module.exports = {
	optimization: {
		//配置生产环境的压缩方案
		minimizer: [
			new TerserPlugin({
				// 使用缓存，加快二次构建速度
				cache: true,
				//开启多进程压缩
				parallel: true,
				//启动source-map
				sourceMap: true,
				terserOptions: {
					// 删除注释
					comments: false,
					compress: {
						// 删除无用的代码
						unused: true,
						// 删除 debugger
						drop_debugger: true,
						// 删除 console
						drop_console: true,
						// 删除无用的代码
						dead_code: true
					}
				}
			})
		]
	}
}
```

## 压缩css代码
使用optimize-css-assets-webpack-plugin压缩css文件。
```javascript
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin')
module.exports = {
    plugins: [new OptimizeCssAssetsPlugin()]
}
```

## 提取css文件
 打包之后css文件会包含在js文件中，使用mini-css-extract-plugin插件提取css到单独的文件。
```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const devMode = process.env.NODE_ENV === 'development'

module.exports = {
	plugins: [
		new MiniCssExtractPlugin({
			filename: devMode ? 'css/[name].css' : 'css/[name].[contenthash:6].css',
			chunkFilename: devMode ? 'css/[name].css' : 'css/[name].[contenthash:6].css'
		})
	],
	module: {
		rules: [
			{
				test: /\.(le|c)ss$/,
				use: [
					devMode
						? 'style-loader'
						: {
								loader: MiniCssExtractPlugin.loader,
								options: {
									publicPath: '../'
								}
						  },
					'css-loader',
					{
						loader: 'less-loader',
						options: { javascriptEnabled: true }
					},
					{
						loader: 'sass-resources-loader',
						options: {
							resources: [
								resolve('src/assets/styles/variable.less'),
								resolve('src/assets/styles/mixin.less')
							]
						}
					}
				]
			}
		]
	}
}
```
