
# 1.Vue3
## 1.1Vue2和Vue3的区别

- Vue3完全使用了TypeScript进行重写，对类型有更强的定义和判断，开发过程中可以获得更友好的类型提示。
- Vue2 的双向数据绑定是通过Object.defineProperty()对数据进行劫持，结合发布订阅模式来实现的(无法检测到通过索引修改的数组，无法检测对象属性的添加或移除)。Vue3 中使用了Es6 的 Proxy对数据代理。
- Vue2 使用Options API， Vue3 可以使用Composition API。在Vue2中，对于复杂的业务，代码量较大，业务逻辑也比较复杂，相关业务代码需要遵循option的配置写到特定的区域，后期比较难维护，代码可复用性不高。使用Composition API可以对业务逻辑进行自由组合，使用更灵活，可以写出高内聚、低耦合的代码，可维护性和复用性都要好很多。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646384423034-98b568e6-4a9f-4fc2-ba5c-84fd672f356e.png#averageHue=%236d782d&clientId=uc1141fad-5be3-4&from=paste&height=372&id=u3d70d9cc&originHeight=750&originWidth=446&originalType=binary&ratio=1&rotation=0&showTitle=false&size=97668&status=done&style=none&taskId=u1478c342-6a0f-463f-b812-781127fb515&title=&width=221)![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646384437136-8c3009e8-5af1-4650-8386-080cb2c67b22.png#averageHue=%2351ae31&clientId=uc1141fad-5be3-4&from=paste&height=370&id=ud16a68de&originHeight=524&originWidth=221&originalType=binary&ratio=1&rotation=0&showTitle=false&size=34669&status=done&style=none&taskId=u79b81c3c-5614-4041-97b2-81266a472b9&title=&width=156)![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646384476010-00538c62-0772-40cc-91d6-3b08687f3b89.png#averageHue=%23110d0a&clientId=uc1141fad-5be3-4&from=paste&height=276&id=myroK&originHeight=590&originWidth=739&originalType=binary&ratio=1&rotation=0&showTitle=false&size=113903&status=done&style=none&taskId=u0463f0eb-02fc-4bd7-9696-fa48da227c6&title=&width=346)


## 1.2 开发体验
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646382170823-8dba0d20-db5e-470a-ab5f-358ab38728a6.png#averageHue=%23ece7e0&clientId=uc1141fad-5be3-4&from=paste&height=25&id=u44ed78a5&originHeight=25&originWidth=131&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1492&status=done&style=none&taskId=ua40ba348-8bce-4ecc-b5e1-c29d33928ec&title=&width=131)
### (1)Vue2
CaptchaOne.vue
```vue
<script>
  export default {
    props: {
      time: Number,
    },
    data() {
      return {
        timer: null,
        count: this.time,
        buttonDisabled: false,
      }
    },
    methods: {
      // 发送验证码
      sendCaptcha() {
        if (this.buttonDisabled) return
        this.buttonDisabled = true
        this.startCount()
      },
      // 开始计时
      startCount() {
        this.timer = setInterval(() => {
          this.count--
          if (this.count === 0) {
            this.resetCount()
            this.$emit('finish')
          }
        }, 1000)
      },
      // 停止计时
      resetCount() {
        clearInterval(this.timer)
        this.buttonDisabled = false
        this.count = this.time
      },
    },
    beforeUnmount() {
      this.resetCount()
    },
  }
</script>

<template>
  <div @click="sendCaptcha">
    获取验证码
    <span>{{ count }}s</span>
  </div>
</template>
```

### (2)setup钩子
CaptchaTwo.vue
```vue
<script>
import { reactive, toRefs, onBeforeUnmount } from 'vue'

export default {
  props: {
    time: Number,
  },
  setup(props, { emit }) {
    let timer = null

    const state = reactive({
      count: props.time,
      buttonDisabled: false,
    })

    // 发送验证码
    const sendCaptcha = () => {
      if (state.buttonDisabled) return
      state.buttonDisabled = true
      startCount()
    }

    // 开始计时
    const startCount = () => {
      timer = setInterval(() => {
        state.count--
        if (state.count === 0) {
          resetCount()
          emit('finish')
        }
      }, 1000)
    }

    // 停止计时
    const resetCount = () => {
      clearInterval(timer)
      state.buttonDisabled = false
      count = props.time
    }

    onBeforeUnmount(() => {
      resetCount()
    })

    return {
      ...toRefs(state),
      sendCaptcha,
    }
  },
}
</script>

<template>
  <div @click="sendCaptcha">
    获取验证码
    <span>{{ count }}s</span>
  </div>
</template>
```


### (3)setup语法糖
CaptchaThree.vue
```vue
<script setup>
import { ref, onBeforeUnmount } from 'vue'

const props = defineProps({
  time: Number,
})

const emit = defineEmits(['finish'])

let timer = null

const count = ref(props.time)
const buttonDisabled = ref(false)

// 发送验证码
const sendCaptcha = () => {
  if (buttonDisabled.value) return
  buttonDisabled.value = true
  startCount()
}

// 开始计时
const startCount = () => {
  timer = setInterval(() => {
    count.value--
    if (count.value === 0) {
      resetCount()
      emit('finish')
    }
  }, 1000)
}

// 停止计时
const resetCount = () => {
  clearInterval(timer)
  buttonDisabled.value = false
  count.value = props.time
}

onBeforeUnmount(() => {
  resetCount()
})
</script>

<template>
  <div @click="sendCaptcha">
    获取验证码
    <span>{{ count }}s</span>
  </div>
</template>
```


## 1.3Hook
自定义hook，封装业务逻辑。<br />核心思想：把一个功能的所有状态、方法、都封装到一个函数里面。

**(1)发送验证码的hook**<br />src/hooks/useCaptcha.js
```javascript
import { ref, onBeforeUnmount } from 'vue'

const useCaptcha = (options) => {
  let timer = null

  const count = ref(options.time)
  const buttonDisabled = ref(false)

  // 发送验证码
  const sendCaptcha = () => {
    if (buttonDisabled.value) return
    buttonDisabled.value = true
    startCount()
  }

  // 开始计时
  const startCount = () => {
    timer = setInterval(() => {
      count.value--
      if (count.value === 0) {
        resetCount()
        options.finish && options.finish()
      }
    }, 1000)
  }
  

  // 停止计时
  const resetCount = () => {
    clearInterval(timer)
    buttonDisabled.value = false
    count.value = options.time
  }

  onBeforeUnmount(() => {
    resetCount()
  })

  return {
    count,
    sendCaptcha,
  }
}

export default useCaptcha
```

**(2)使用**
```vue
<script setup>
import useCaptcha from './hook/useCaptcha'


const { count, sendCaptcha } = useCaptcha({
  time: 10,
})

const handleClick = () => {
  sendCaptcha()
}
</script>

<template>
  <div @click="handleClick">
    获取验证码
    <span>{{ count }}s</span>
  </div>
</template>
```

**(3)为什么不用mixin**

- 变量来源不明确（隐式引入），可读性非常差。

组件里引入多个mixin，并直接隐式调用mixin里的变量、方法、生命周期，感觉非常混乱， 这些东西没在当前组件内定义，也没见有import，它们是从哪里来的，位于哪个mixin里？

- 不同mixin的生命周期会合并到一起运行，但是同名状态、同名方法无法融合，可能会导致冲突。

## 1.4状态管理
状态管理采用的是[Pinia](https://pinia.vuejs.org/)，放弃使用Vuex。

- 不需要自定义复杂的类型来支持 Typescript，具备完美的类型推断。
- 模块化设计，引入的每一个 store 在打包时都可以自动拆分他们。
- api设计简洁，使用简单方便。
- 无嵌套结构，可以在任意的 store 之间交叉组合使用。

**(1)创建appStore**
```typescript
import { defineStore } from 'pinia'
import apis from '@/apis'
import store from '@/store'
import { TokenName } from '@/config/const'
import { localMng } from '@/utils/storage-mng'
import { IUserInfo } from '@/model/common'


interface IState {
  token: string
  userInfo: IUserInfo
}

export const useAppStore = defineStore('app', {
  state(): IState {
    return {
      token: localMng.getItem(TokenName),
      userInfo: {},
    }
  },
  actions: {
    setToken(token: string) {
      this.token = token
    },
    // 获取用户信息
    async getUserInfo() {
      const res = await apis.common.getUserInfo()
      this.userInfo = res
    }
  },
})
//  在setup之外使用
export const useAppStoreWithOut = () => useAppStore(store)
```

**(2)使用**
```typescript
<script lang="ts" setup>
import { ref } from 'vue'
import { useAppStore, computed } from '@/store'

const appStore = useAppStore()
const userInfo = computed(() => appStore.userInfo)
<script>
```

# 2.ElementPlus
[NaiveUI](https://www.naiveui.com/zh-CN/os-theme)  
## 1.1主题配置
src/styles/element.less
```css
:root {
  --el-color-primary: #0084ff;
  --el-text-color-primary: #333;
  --el-text-color-regular: #666;
  --el-text-color-secondary: #999;
}
```


## 1.2 组件API
### (1)value->model-value
```vue
// element-ui
<el-input :value="value"></el-input>

//element-plus
<el-input :model-value="value"></el-input>
```

### (2)sync->v-model:
```vue
// element-ui   
<el-pagination :current-page.sync="currentPage"></el-pagination>

//element-plus
<el-pagination v-model:currentPage="currentPage"></el-pagination>
```



# 3.TypeScript
JavaScript的超集，给 JavaScript 添加语言特性。类型批注和编译时类型检查、类型推断、接口、枚举、泛型<br />、元组、类、模块等。

### (1)基本列表应用
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646644024696-a099b426-f096-418d-aae7-bcbabe457f03.png#averageHue=%23f9f8f7&clientId=u971794f6-3540-4&from=paste&height=181&id=u2e17e42a&originHeight=181&originWidth=1045&originalType=binary&ratio=1&rotation=0&showTitle=false&size=14225&status=done&style=none&taskId=u7fad90de-4126-45e2-bae4-7ba45699cb8&title=&width=1045)

**定义订单模型**<br /> 	src/model/order.ts
```typescript
//  支付订单
export interface IPayOrder {
  id: string
  // 平台支付订单号
  platOrderNo: string
  // 渠道支付订单号
  canalsSn?: string
  // 商户订单号
  outOrderNo: string
  // 第三方应用ID
  thirdAppId?: string
}
```

**订单列表api**<br />src/apis/modules/order
```typescript
import request from '@/utils/request'
import { IPayOrder } from '@/model/order'

// 支付订单列表
export const getOrderPayPage = (data): Promise<IPayOrder[]> =>
  request.post('/order/list/page', data, { perms: 'order:list:page' })
```


**获取订单列表**
```typescript
<script lang="ts" setup>
import { ref, onMounted } from 'vue'
import apis from '@/apis'
import { IPayOrder } from '@/model/order'

const tableData = ref<IPayOrder[]>([])

onMounted(() => {
  getTableData()
})

// 获取支付订单列表
const getTableData = async () => {
  try {
    const res = await apis.order.getOrderPayPage({})
    tableData.value = res
  } catch (err) {
    console.error(err)
  } finally {
  }
}
</script>
```

### (2)详情应用
```vue
<script lang="ts" setup>
  import { ref, computed } from 'vue'
  import apis from '@/apis'
  import { IPayOrder } from '@/model/order'
  
  const detail = ref<IPayOrder>({} as IPayOrder)
  
  // 获取订单详情
  const getOrderDetail = async () => {
    try {
      initLoading.value = true
      const res: IPayOrder = await apis.order.getOrderPayDetail(detail.value.platOrderNo)
      detail.value = {
        ...res,
        merId: res.merId || '-',
        refundFee: res.refundFee / 100,
      }
    } catch (err) {
      console.error(err)
    } finally {
      initLoading.value = false
    }
  }
  
  const handleClose = () => {
    visible.value = false
  }
</script>
```

### (3)智能提示
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646813162180-2ef17933-c461-4274-a43a-e43da3298950.png#averageHue=%23403d41&clientId=u4e5f4192-0587-4&from=paste&height=170&id=uf4ab579c&originHeight=170&originWidth=570&originalType=binary&ratio=1&rotation=0&showTitle=false&size=15614&status=done&style=none&taskId=u09625e86-9d0d-4736-859e-b01533b2e52&title=&width=570)<br />**速览定义**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646813362651-079d08c9-096f-4c64-bd25-12f4d6631984.png#averageHue=%23322f33&clientId=u4e5f4192-0587-4&from=paste&height=157&id=ud904d6e6&originHeight=195&originWidth=582&originalType=binary&ratio=1&rotation=0&showTitle=false&size=24102&status=done&style=none&taskId=u4cdcf8b3-27a1-4d1b-9877-6f7cb030df9&title=&width=469)![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646813418454-970f270b-4316-449c-8913-c4d53a20db24.png#averageHue=%23424043&clientId=u4e5f4192-0587-4&from=paste&height=153&id=u33c44983&originHeight=187&originWidth=272&originalType=binary&ratio=1&rotation=0&showTitle=false&size=13376&status=done&style=none&taskId=u0e9a915b-2278-473c-b8b5-d41ad4e2aa9&title=&width=223)

**函数注释提示**<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646717826607-8f838745-21ac-4db7-b713-5658892200fe.png#averageHue=%233d3b3f&clientId=uf5cfae7b-3614-4&from=paste&height=181&id=uded9a7d0&originHeight=181&originWidth=790&originalType=binary&ratio=1&rotation=0&showTitle=false&size=20932&status=done&style=none&taskId=u0a70ae32-bf3e-4275-b3f6-59cb3e47337&title=&width=790)

### (4)快速重命名
在model中选中某个属性，按下F2，修改属性名称之后，项目中所有用到该属性的地方都会自动修改。<br />在后端调整字段名称之后，前端可通过此方式快速同步做出调整。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646892270623-4ffeb0ab-457e-4f75-b314-4c8a4cc89dfc.png#averageHue=%233a383b&clientId=u1bed35fa-1aab-4&from=paste&height=84&id=u7089b92f&originHeight=84&originWidth=267&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9207&status=done&style=none&taskId=ub8680485-0163-4ab7-8385-50f7ea127c9&title=&width=267)


# 4.Vite
💡 极速的开发服务器启动<br />⚡️ 轻量快速的热模块重载（HMR）<br />🛠️ 丰富的功能<br />📦 自带优化的构建<br />🔩 通用的插件接口<br />🔑 完全类型化的 API

## 1.快速搭建项目模板
```javascript
# 生成模板
yarn create vite vue-app --template vue-ts

# 安装依赖
yarn

# 启动
yarn dev
```

## 2.基础配置
```typescript
import { defineConfig } from 'vite'
import Vue from '@vitejs/plugin-vue'
import Components from 'unplugin-vue-components/vite'
import AutoImport from 'unplugin-auto-import/vite'
import Icons from 'unplugin-icons/vite'
import IconsResolver from 'unplugin-icons/resolver'
import { FileSystemIconLoader } from 'unplugin-icons/loaders'

const path = require('path')
const resolve = (dir: string) => path.resolve(process.cwd(), dir)

export default defineConfig({
	base: './',
	plugins: [
		Vue(),
		Icons({
			compiler: 'vue3',
			customCollections: {
				custom: FileSystemIconLoader(resolve('src/assets/icons')),
			},
		}),
		AutoImport({
			imports: ['vue', 'vue-router'],
		}),
		Components({
			dts: true,
			resolvers: [
				IconsResolver({
					prefix: 'icon',
					customCollections: ['custom'],
				}),
			],
		}),
	],
	resolve: {
		alias: {
			'@': resolve('src'),
		},
		dedupe: ['vue'],
	},
	css: {
		postcss: {
			plugins: [require('tailwindcss')('tailwind.config.js')],
		},
		preprocessorOptions: {
			less: {
				javascriptEnabled: true,
				globalVars: {
					primary: '#0096ff',
					warning: '#fca700',
					danger: '#ff1d52',
					success: '#00b246',
					'info-primary': '#333',
					'info-regular': '#666',
					'info-secondary': '#999',
				},
			},
		},
	},
	server: {
		open: false,
		host: '0.0.0.0',
		proxy: {
			'/api': {
				target: 'http://10.10.1.100:9000',
				changeOrigin: true,
				ws: true,
				secure: false,
				// rewrite: (path) => path.replace(/^\/services/, ''),
			},
		},
	},
	build: {
		rollupOptions: {
			output: {
				manualChunks: {
					vue: ['vue', 'vue-router', 'pinia'],
					lodash: ['lodash'],
					element: ['element-plus'],
				},
				chunkFileNames: 'js/[name]-[hash].js',
				entryFileNames: 'js/[name]-[hash].js',
				assetFileNames: '[ext]/[name]-[hash].[ext]',
			},
		},
	},
})

```
## 3.打包时取消TypeScript检查

执行yarn build构建失败，出现类似以下的错误信息：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1646814198900-c608a5c3-ec3a-4403-823b-423eb3cf758c.png#averageHue=%23413f42&clientId=u4e5f4192-0587-4&from=paste&height=73&id=u60a9dab6&originHeight=73&originWidth=1133&originalType=binary&ratio=1&rotation=0&showTitle=false&size=11355&status=done&style=none&taskId=uca202073-1af4-457b-aa6e-f407d092330&title=&width=1133)<br />原因为vite默开启了vue-tsc对vue3的类型检测。

package.json
```typescript
  "scripts": {
    "serve": "dev",
    "build": "vue-tsc --noEmit && vite build",
    "preview": "vite preview"
  },
```

取消vue-tsc的检测：
```typescript
 "build": "vite build",
```

只取消vue-tsc对node_modules的检测：
```typescript
"build": "vue-tsc --noEmit --skipLibCheck && vite build"
```



# 5.学习资源
[Vue3](https://v3.cn.vuejs.org/guide/introduction.html)<br />[Vue Router](https://router.vuejs.org/zh/installation.html)<br />[Vite](https://vitejs.cn/guide/)<br />[VueUse](https://vueuse.org/guide/)<br />[Vue3+TypeScript？看这一篇就够了](https://blog.csdn.net/qq_40280582/article/details/112444461)
