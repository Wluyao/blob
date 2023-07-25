_写在前面：这篇文档有点标题党，并不是真实全面的BFC和DDD。只是从它们的一些思想中有所启发，并实际应用于项目中。主要讲解在前端service层来解决业务逻辑重复的问题；以及面对复杂业务逻辑的时候，如何在前端建立领域模型。_
# BFF
BFF，即Backends For Frontends，后端驱动的前端。主要用于服务于前端的后端程序，来解决多端请求中业务重复和耦合的问题。
## 作用和使用场景
### 接口聚合和透传
聚合多个接口，方便前端调用，减少HTTP请求。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659850802863-bd1e0516-7478-4546-80ae-cf2ffb2de442.png#averageHue=%23fcfcfc&clientId=uf113e441-756b-4&from=paste&height=199&id=u46d395ce&originHeight=455&originWidth=708&originalType=binary&ratio=1&rotation=0&showTitle=false&size=28196&status=done&style=none&taskId=ucd82b550-103f-4644-a0fb-ead16ae5f43&title=&width=309.90911865234375)      ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659850946934-b2dd5893-6275-443e-97a0-a1de11306201.png#averageHue=%23fbfbfb&clientId=uf113e441-756b-4&from=paste&height=196&id=Sd41b&originHeight=435&originWidth=738&originalType=binary&ratio=1&rotation=0&showTitle=false&size=32657&status=done&style=none&taskId=ua4e13208-1ce4-4b3b-b4ef-56399ed3c85&title=&width=331.727294921875)<br />&：在喻支付的的商户认证信息中，就需要同时调用商户信息和商户资质图片的接口才能得到完整的商户信息。
### 接口数据处理
前端页面只负责 UI 渲染和交互，不处理复杂的数据关系和业务逻辑。前端代码的可读性和可维护性得到很大改善。后端基础服务也只进行增删改查，降低后端服务层的耦合度。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659851534765-bfa25394-5aa4-4742-8193-83158ab96e05.png#averageHue=%23c9ddef&clientId=uf113e441-756b-4&from=paste&height=96&id=ue9ad87a4&originHeight=208&originWidth=692&originalType=binary&ratio=1&rotation=0&showTitle=false&size=17607&status=done&style=none&taskId=ua84be114-45a8-4994-9547-cea6bb296c6&title=&width=320.2727355957031)![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659851659346-cc2ba8c8-0d29-49a6-adcc-cfe9c3dc467e.png#averageHue=%23cbdeef&clientId=uf113e441-756b-4&from=paste&height=97&id=u565950bb&originHeight=178&originWidth=682&originalType=binary&ratio=1&rotation=0&showTitle=false&size=11962&status=done&style=none&taskId=ud1dee28c-ccff-4010-8a82-13e47a28269&title=&width=372)<br />&：容器云复杂的地方：后端没有处理数据之间的关系，调用k8s的api，直接将原始数据给到前端，前端自行处理这些数据，组织它们之间的关系。

### 处理多端访问
如果业务逻辑需要客户端处理，而在客户端又存在多个平台，有PC端，有移动端，移动端又有Android和IOS，那么客户端每方的开发都需要独自处理这些相同的业务逻辑。<br />如果将这些重复的业务逻辑放在一个统一的中间层进行处理，每个平台只需要和这个中间层交互就可以直接得到所需要的数据。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659852358488-c7875822-5c9d-4756-9a01-fd9a91a2c6df.png#averageHue=%23cee0f1&clientId=uf113e441-756b-4&from=paste&height=134&id=ufa3278a7&originHeight=254&originWidth=546&originalType=binary&ratio=1&rotation=0&showTitle=false&size=22276&status=done&style=none&taskId=uddd94f95-df06-498a-ae9c-7d763ec03a2&title=&width=287.0909118652344)      ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659852966066-58c5a216-1e21-41ff-bedd-16b77f083e06.png#averageHue=%23cfe1f1&clientId=uf113e441-756b-4&from=paste&height=133&id=ud0ea10ff&originHeight=272&originWidth=785&originalType=binary&ratio=1&rotation=0&showTitle=false&size=24310&status=done&style=none&taskId=u4392284c-be01-4402-955a-6b38c6be9e7&title=&width=382.90911865234375)

## 接口数据处理
虽然我们没有直接使用BFF的架构，但是面对前端开发中的一些痛点，BFF架构的作用还是有一些用武之地。<br />对于后端返回的数据，如果要进行业务逻辑的处理，我们往往是放在视图层进行处理。
#### 处理业务的位置
##### 传统做法
往往我们都是在视图层处理业务。<br />/model
```typescript
// 货品信息
export interface IGoods {
	// 货品id
	goodsId: number
	// 货品名称
	goodsName: string
	// 货品简称
	nameAcronym: string
	//  是否进口 0:是 1：否
	entrance: 0 | 1
	// 货品状态
	status: number
	// 货品图片
	goodsImg: string
	// 产地
	productionPlace: string
	// 最低限价
	lowPrice: number
	// 最高限价
	highPrice: number
	// 供应商id
	supplierId: number
	// 供应商名称
	supplierName: string
	// 供应商头像
	supplierHeadimg: string
  // 生产日期
	createdTime:string
	//  店铺是否允许赊账
	creditLimitSwitch: 0 | 1
	// 会员是否允许赊账
	customerCreditLimit: 0 | 1
}
```
/apis
```typescript
import request from '@/utils/request'
import { IGoods} from '@/model/goods'

//  获取商品详情
export const getGoodsDetail = (id: number): Promise<IGoods> => request.post(`/goods/detail/${id}`)
```
apis的作用也仅仅是定义后端接口。

/views
```vue
<script lang="ts" setup>
import apis from '@/apis'
  
const getGoodsDetail = async () => {
	const data = await apis.goods.getGoodsDetail(Number(route.params.id))
	goods.value = {
		...data,
		limitText: getCreditText(data),
	}
}

const getCreditText = (goods: IGoods) => {
	if (goods.entrance === 0) {
		return '不限额'
	}
	if (goods.creditLimitSwitch === 0 && goods.customerCreditLimit === 0) {
		return '不许赊账'
	}
}
</script>
```
##### servce层中的做法
业务逻辑放在service层进行处理。<br />/apis
```typescript

//  获取商品详情
export const getGoodsDetail = async (id: number): Promise<IGoods> => {
	const data = await request.post(`/goods/detail/${id}`)
	return {
		...data,
		limitText: getCreditText(data),
	}
}

const getCreditText = (goods: IGoods) => {
	if (goods.entrance === 0) {
		return '不限额'
	}
	if (goods.creditLimitSwitch === 0 && goods.customerCreditLimit === 0) {
		return '不许赊账'
	}
}
```

/views
```typescript
<script lang="ts" setup>
import apis from '@/apis'
  
const getGoodsDetail = async () => {
	const data = await apis.goods.getGoodsDetail(Number(route.params.id))
	goods.value  = data
}
</script>
```
  	tip：什么时候适合把这种处理放在service中？一个很简单的判断方法：当你认为某个字段(这里的limitText)，应该由后端提供，但是后端却没有直接给你的时候！

#### 接口字段不可控
不用受限于后端接口，没必要一定要和后端接口中返回的字段名以及数据结构保持一致，一是往往接口中返回的数据模型并不能很好的匹配前端的视图结构，二是要等到后端接口出来了才能开始对接。如果后端技术栈是Python，接口中的字段名还很有可能是下划线的形式，这样在视图层的组件中就会出现到处充斥着驼峰命名和下划线命名的情况，包括我们现在的接口中都偶尔会出现下划线的字段名。<br />前端完全可以定义适合自己的数据模型，并在前期开发过程中根据这些模型来自己造数据。等到后端接口完成，获取到真正的数据之后，在service中将返回的数据转换成前端模型即可，而且转换的过程中还可以增加一些默认值，还可以将属于多个字段的数据组织成一个，进行一些简单业务逻辑的处理。

/model
```typescript

// 货品信息
export interface IGoods {
	// 货品id
	id: number
	// 货品名称
	name: string
	// 货品简称
	abbreviation: string
	//  是否进口 0:是 1：否
	isEntrance: 0 | 1
	// 货品状态
	status: number
	// 货品图片
	img: string
	// 产地
	originPlace: string
	// 最低限价
	priceLow: number
	// 最高限价
	priceHigh: number
	// 供应商
	supplier: IPerson
  // 赊账信息
  creitText:string
    // 生产日期
	createdTime:string
	//  店铺是否允许赊账
	shopCreditLimit: 0 | 1
	// 会员是否允许赊账
	customerCreditLimit: 0 | 1
}
```
前端定义自己的数据模型，甚至连接口文档都不用提前知道；如果觉得后端的命名晦涩难懂，可以自行定义字段名，自己起的名字肯定比别人起的印象更深刻。<br />而且后端出于一些考量，可能更关注的是如何方便自己开发，返回的模型不具备通用性，前端也可以进行整合，比如模型里的supplier，后端分了三个字段返回，但是supplier只需要它的id、头像、名称这几个基础信息，前端完全可以定义一个通用的Person模型，这样不管是supplier还是customer还是user，只要是人，都可以进行复用。

/apis
```typescript
//  获取商品详情
export const getGoodsDetail = async (id: number): Promise<IGoods> => {
	const data = await request.post(`/goods/detail/${id}`)

	return {
		id: data.goodsId,
		name: data.goodsName,
		abbreviation: data.nameAcronym,
		isEntrance: data.entrance,
		status: data.status,
		img: data.goodsImg || 'defaultGodsImg',
		originPlace: data.productionPlace || '-',
		priceLow: data.lowPrice,
		priceHigh: data.highPrice,
		supplier: {
			id: data.supplierId,
			name: data.supplierName,
			avatar: data.supplierHeadimg || 'defaultPersonImg',
		},
		createdTime: dayjs(data.createdTime).format('YYYY-MM-DD HH:mm:ss'),
		creitText: getCreditText(data),
		shopCreditLimit: data.creditLimitSwitch,
		customerCreditLimit: data.customerCreditLimit,
	}
}
```
咋一看觉得这种处理完全没必要，但是当遇到一些可能让人难受的地方的时候，仅仅在这里多写一些"看起来无意义的"代码，对整个开发过程是利大于弊的。

#### 视图层过重
在视图层的模板中尽量不要处理过多的逻辑，可以提取出来使用专门的状态来控制。<br />/views
```vue
<template>
  <div v-if="goodsDetail.priceLow || goodsDetail.priceHigh">
      <div>货品进行了限价</div>
  </div>
</template>
```
在视图中通过一些逻辑判断来决定是是否显示提示信息，如果模板中有很多像这样的代码，整个是视图层显得杂乱无章，非常庞大。<br />在传统的前端开发中，有一种将HTML和JS分离的做法，虽然现在的前端框架都支持而且可以很方便的在HTML中写逻辑，但这样做恰恰是将HTML和JS耦合在了一起。

/model
```typescript
export interface IGoods {
// 货品是否进行了限价
 isLimitPrice:boolean
}
```
/apis
```typescript
//  获取商品详情
export const getGoodsDetail = async (id: number): Promise<IGoods> => {
	const data = await request.post(`/goods/detail/${id}`)

	return {
    isLimitPrice：data.priceLow || data.priceHigh
}
```
/views
```vue
<template>
<div v-if="isLimitPrice">
  <div>货品进行了限价</div>
  </div>
</template>
```
这里还运用了“**将隐性的概念显性化**”的思想，提高了业务代码的清晰度。<br />将priceLow和priceHigh这两个概念整合成一个概念isLimitPrice。<br />如果其他人接手这块的代码，看到“goodsDetail.priceLow || goodsDetail.priceHigh”能一下就知道是什么意思吗？结合上下文去理解？去问产品？去问后端？去找之前负责这一模块的前端？<br />但是将这个逻辑提取为一个单独的变量、属性，并在model中指明其是什么，是不是一眼就能知道它的意思呢！
## 重复逻辑处理
BFF架构还有一个作用就是处理多端访问，避免不同端都要处理相同的业务逻辑。<br />虽然我们目前没有遇到多端访问的需求。但是在实际开发中经常会遇到通过不同的接口获取到数据之后，却要处理相同的业务逻辑的情况。<br />比如有一个列表接口和一个详情接口，获取到数据之后在列表页面要处理一次业务逻辑，而在详情页中又要使用相同的方式再处理一遍业务逻辑，涉及到业务调整的时候，又要在不同的地方都进行调整。<br />有时候会想到将处理的逻辑提取到一个公用的地方，在不同的地方导入之后使用，这样只用维护一份代码即可。<br />但是我们完全可以把service层当做是BFF中的中间层来使用。<br />/apis
```typescript

//  工作负载列表
export const getWorkList = (payload)=> request.post('/api/workload/list', payload)

// 获取工作负载详情
export const getWorkDetail =  (payload)=> request.post('/api/workload/detail', payload)
```

/views/WorkloadList.vue
```vue
<script lang="ts" setup>
const getTableData = async () => {
	const data: IWorkload[] = await apis.workload.getWorkList()
	const filteredData = data.map((item) => ({
		...item,
		podList: getPodlist(item),
		customStatus: getStatus(item),
		message: getErrorMessage(item),
	}))
}

const getPodlist = () => {}
const getStatus = () => {}
const getErrorMessage = () => {}
</script>
```

/views/WorkloadDetail.vue
```typescript
<script lang="ts" setup>
const getDetail = async () => {
	const data: IWorkload = await apis.workload.getWorkDetail()
	const detail = {
		...item,
		customStatus: getStatus(item),
		message: getErrorMessage(item),
	}
}

const getStatus = () => {}
const getErrorMessage = () => {}
</script>
```
在列表和详情中都出现了处理状态和错误信息的逻辑。

可以将这种逻辑处理直接放在service层。
```typescript
//  工作负载列表
export const getWorkList = async (payload): Promise<IWorkload[]> => {
  const data = await request.post('/api/workload/list', payload)
  
  return data.map((item) => ({
    ...item,
    podList: podList(item),
    customStatus: workloadStatus(item),
    message: workloadErrorMessage(item),
  }))
}

// 获取工作负载详情
export const getWorkDetail = async (payload): Promise<IWorkload> => {
  const data = await request.post('/api/workload/detail', payload)
  return {
    ...data,
    ...{
      message: workloadErrorMessage(data),
      customStatus: workloadStatus(data),
    },
  }
}

const getPodlist = () => {}
const getStatus = () => {}
const getErrorMessage = () => {}
```

## 资料
[你学BFF和Serverless了吗 ](https://juejin.cn/post/6844904185427673095)<br />[基于 GraphQL 的 BFF 实践](https://juejin.cn/post/7101118014614929422)<br />[中台背景下如何用Node做BFF层 ](https://juejin.cn/post/6908249820587098120)<br />[聊聊我理解的前端 BFF 层](https://www.yuque.com/huajinbo/lxhzqg/yrqmtr)<br />[GraphQL及元数据驱动架构在后端BFF中的实践](https://www.yuque.com/ldbmcs/java/fwdyh5#e20cd717)<br />[基于 GraphQL 平台化 BFF 构建及微服务治理](https://zhuanlan.zhihu.com/p/460593348)<br />[基于node的BFF前端一体化架构设计](https://zhuanlan.zhihu.com/p/159303617)<br />[跨平台架构：如何设计 BFF 架构系统](https://www.jianshu.com/p/bfc652baccf7)<br />[GraphQL-BFF：微服务背景下的前后端数据交互方案](https://mp.weixin.qq.com/s/oZsF4cHdInJXJ3S2Ov9g3A)

# DDD
DDD，Domain-driven design，即领域驱动设计。它不是一个框架、标准或规则，只是一种解决复杂业务的设计思想。<br />领域模型通常是一组具有业务含义的类或者对象，它们通过方法、属性等方式封装了系统的关键业务逻辑。无论如何，只要它能被系统中的其他不同应用复用就可以。<br />通常来说我们可以通过手动创建或者抽象工厂方法构造出模型数据，把这些数据响应式地映射到视图层，再根据视图层触发的事件调用模型层里的函数或方法来更新模型层数据。
## 背景
通常后端接口中返回的数据都是比较灵活通用的，前端拿到这些数据之后往往不能直接使用，经常需要经过业务逻辑和UI逻辑的处理。接口返回的数据只能算是DTO(Data Transfer Object)，但是我们真正所要使用的是DP(Domain Primitive)。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627829579213-558bc0f7-ea84-4eed-83dc-b2264452f444.png#averageHue=%23f7f7f7&clientId=u54ab99ed-5239-4&from=paste&height=205&id=WSnTz&originHeight=273&originWidth=731&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43357&status=done&style=none&taskId=u13b8a344-eff4-44a5-9e24-b3a3fee62fc&title=&width=548)<br />如果是一个简单的组件，则可以直接在组件内部进行业务逻辑的处理。但是假如在组件中需要维护几十个状态、而很多状态之间都存在复杂的关联性，又衍生出一堆计算属性，甚至在一整个模块中的多个组件之间都存在关联，单单它们之间的通信都够喝一壶，如何还能保证代码的可维护性，甚至在开发初期都感觉难以下手。
## 示例
模拟一个售货员在商店卖东西的场景：页面中展示了商店中的所有商品，售货员选择要卖的商品，然后需要输入商品的数量、重量、单价、附加费、抹零、备注等等信息。系统需要处理这个商品是按数量卖/按重量卖，以什么单位卖，库存是多少、是否还够卖，输入框是否允许输入，一些信息是否需要显示，价格是否合理，最后的总价是多少。当然实际情况远比这样描述的复杂。<br />前端最开始知道的信息仅仅是商品的基本信息，经过一系列交互操作之后形成一条商品的销售流水信息。

建立商品的销售流水模型，goods-fow/domain.ts：
```typescript
import { observable, computed, action, reaction } from 'mobx'
import _ from 'lodash'
import { IGoods, GoodsUnit } from 'model/goods'

class GoodsFlow {
	constructor(goods: IGoods) {
		const price = _.toString(goods.standardPrice) || ''
		this.price = price
		this.goodsInfo = goods
		this.additionMoney = _.toString(goods.additionMoney)
		this.flowId =`${Date.now()}-${goods.goodsId}`
	}

	// 商品销售流水id
	flowId: string = ''

	// 商品基本信息
	@observable
	goodsInfo: Goods

	// 商品单价
	@observable
	price: string = ''

	// 附加费单价
	@observable
	additionMoney: string = ''

	// 商品数量
	@observable
	amount: string = ''

	// 商品重量
	@observable
	weight: string = ''

	// 毛重
	@observable
	grossWeight: string = ''

	// 皮重
	@observable
	tareWeight: string = ''

	// 总价
	@observable
	totalMoney: string = ''

	// 商品近10次销售均价
	@observable
	averagePrice: number = 0

	// 商品销售单位
	@observable
	saleUnit: GoodsUnit

	// 备注
	@observable
	remark: string = ''

	// 最小计数单位
	@computed
	get minAmountUnit() {}

	// 最大计数单位
	@computed
	get maxAmountUnit() {}

	// 商品id
	@computed
	get goodsId() {}

	// 附加费名称
	@computed
	get additionMoneyName() {}

	// 附加费单位名称
	@computed
	get additionMoneyUnitName() {}

	// 是否为计数商品
	@computed
	get isAmountScene() {}

	// 是否为计重商品
	@computed
	get isWeightScene() {}

	// 商品数量单位
	@computed
	get amountUnitName() {}

	// 商品重量单位
	@computed
	get weightUnitName() {}

	// 商品价格单位
	@computed
	get priceUnitName() {}

	// 库存信息
	@computed
	get storageText() {}

	// 是否可录入数量
	@computed
	get isAmountInput() {}

	// 是否可录入重量
	@computed
	get isWeightInput() {}

	// 是否可录入单价
	@computed
	get isPriceInput() {}

	// 是否可录入附加费
	@computed
	get isAdditionMoneyInput() {}

	// 是否显示附加费
	@computed
	get isAdditionMoneyShow() {}

	// 是否负库存
	@computed
	get isMinusStorage() {}

	// 售价是否低于成本价
	@computed
	get isLessCostPrice() {}

	// 售价是否低于指定价
	@computed
	get isLessAssignPrice() {}

	// 售价是否低于均价
	@computed
	get isLessAveragePrice() {}

	// 价格预警
	@computed
	get priceNotice() {}

	// 是否调整过总价
	@computed
	get isModifyTotalMoney() {}

	// 是否为退单
	@computed
	get isRefund() {}

	// 商品单位的变化调整商品的价格
	goodsPriceReaction = reaction(
		() => this.saleUnit,
		data => {},
	)

	// 设置毛重
	@action
	setGrossWeight(value: string) {}

	// 设置皮重
	@action
	setTareWeight(value: string) {}

	// 设置单价
	@action
	setPrice(value: string) {}

	// 设置附加费
	@action
	setAdditionMoney(value: string) {}

	// 设置总价
	@action
	setTotalMoney(value: string) {}

	// 设置备注
	@action
	setRemark(value: string) {}

	// 设置商品销售单位
	@action
	setSaleUnit(value: GoodsUnit) {}

	// 计算单价
	calcPrice() {}

	// 计算总价
	calcTotalMoney() {}

	// 更新最新的商品信息
	updateGoodsInfo = () => {}

	// 获取商品库存信息
	getStorageText = (amount?: number, weight?: number) => {}
}

export default GoodsFlow
```
这里领域模型的建立使用了状态管理器Mox，Mox跟框架没有关联性，不管三大框架中的哪一种都可以与之配合。我们目前使用的pinia也是一样，pinia也有自己的状态，getter(计算属性)，action(非常适合定义业务逻辑)。<br />这里去除了一部分状态，但是核心就需要这么多的状态需要管理。<br />如果将他们全部放在一个组件中，再加上组件中的交互逻辑，交互和业务混杂在一起，直接丢在视图层去处理将会是一场灾难。
## 容器云
之所以说容器云的代码比较复杂，最根本的原因是前端需要处理很多的原始数据，而且存在很多状态。在着手开发服务部署模块的时候，也考虑到可以为工作负载、负载均衡、服务发现等建立对应的领域模型。<br />但很幸运的是，这些数据之间的关联性不是很大，综合考虑之后没有使用领域模型，而是由每个模块独自处理自己的业务逻辑。如果硬要使用领域模型，反而是一种过度开发，还把问题搞复杂了。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659965633472-083cb253-16df-43a3-8801-ed7f1ee6aceb.png#averageHue=%23e2c094&clientId=u3d162b13-872f-4&from=paste&height=311&id=ua22eddb1&originHeight=427&originWidth=770&originalType=binary&ratio=1&rotation=0&showTitle=false&size=26517&status=done&style=none&taskId=u8ddbe9f1-7728-4068-a289-823f2200aad&title=&width=560)<br />比如在工作负载中，环境变量、主机调度、数据卷它们之间没有任何关系。所以环境变量的业务逻辑就直接在环境变量的组件中处理，主机调度的业务逻辑就在主机调度组件中处理。<br />但是假设把问题的复杂度上升一个层次，环境变量、主机调度、数据卷它们之间存在很强的关联性，环境变量的设置需要依赖主机调度中的某些状态，而主机调度的设置又依赖数据卷中的某些状态。甚至是工作负载跟负载均衡、服务发现之间也存在关联，那么就非常适合使用领域模型。

工作负载领域模型示意：<br />将工作负载涉及到的所有业务逻辑统一放在一个领域中，即实现了逻辑的内聚，避免视图层到处分散着相关联的逻辑，又利用了store天生状态共享的特点。
```typescript
import { defineStore } from 'pinia'
import { IWorkload } from '@/model/service'

export const useWorkLoadStore = defineStore('workload', {
	state: (): IWorkload => ({} as IWorkload),
	getters: {
		//  工作负载状态
		customStatus(state) {},
		//  错误信息
		message(state) {},
		// pod信息
		podList(state) {},
		// 环境变量字段配置
		envKeyData(state) {},
	},
	actions: {
		//  根据接口返回的数据初始化为前端状态
		init(data) {},
		// 获取密文源
		async getSecretList(query) {},
		//  获取配置映射源
		async getConfigList(query) {},
		//  获取PVC列表
		async getPvcList() {},
		//  环境变量
		setEnvList() {
			this.envList = []
		},
		// 环境变量引用的其他资源
		setEnvSourceList(state) {},
		// 卷
		setVolumeList(state) {},
	},
})
```
看起来好像就像就是一个全局状态管理器，但是为什么要强调它是一种领域模型，核心思想就是反复提到的“**_业务逻辑封装_**”，而不是简单的状态共享，如果只是为了实现状态之间的共享，那么其意义就是一个简单的store。

在视图层中使用：<br />由于业务逻辑全部放在了领域模型中处理，在具体的视图层组件中，只需要简单的获取需要的状态，调用相应的action获取数据，甚至可以直接设置状态来进行更新。<br /> WorkloadDetail.vue
```vue
<script lang="ts" setup>
import { useWorkLoadStore } from '@/store/workload'
  
const workload =  useWorkLoadStore()

onMounted(async () => {
  const data = await getWorkloadDetail()
  workload.init(data)
})
</script>
```

EnvironmentEdit.vue
```vue
<script lang="ts" setup>
import { useWorkLoadStore } from '@/store/workload'
  
const workload =  useWorkLoadStore()

const secretList = ref<Array<{ uid: string; name: string; namespace: string; data: object }>>([])

onMounted(async () => {
   secretList.value = await workload.getSecretList(route.query)
})
  
  // 添加环境变量
const handleAddEnv = () => {
	store.envList.push({
		name: '',
		value: '',
	})
}
</script>
```
在领域模型中封装着一个模块所有的业务逻辑，所以在这里不要吝啬自己的注释，每个状态为何要经过这样的处理，都在注释中写明，即能够帮助自己在后期维护的时候快速的回忆起为什么要这样做，同时对其他人也会非常友好，一个从来没有接触过这块业务的开发，一看这里的业务这么复杂，他如何去接手？但是只要把一个领域模型理解清楚，接下来的工作将会非常轻松。
## 好处
**业务层和视图层分离**<br />业务逻辑具有极高的复杂度，拿出来单独管理，全部放在领域模型中进行处理，视图层只用处理交互。让视图层尽量的轻，当业务变化时也可以快速的到领域模型中调整业务层中的代码。

**业务操作内聚**<br />每个计算属性、监听属性、方法都封装了特定的业务逻辑，它们为什么要做这样，存在的目的是什么都清清楚楚，在很大程度上避免了业务代码分散在各处导致的项目混乱、架构脆弱的问题。

**促进团队成员对业务的理解**<br />铁打的代码，流水的产研。当我们接手一块业务的时候，如何能够快速弄清楚里边的业务逻辑？看产品文档，有没有文档、是最新的吗？向老人请教，负责写这个模块的开发跑路了？所以每个业务准确的细节点，还是要看系统代码实际的逻辑规则。<br />整体的业务代码是一种聚合的状态，整块业务具体要做什么都一目了然，开发者只需要关注这个模型能够很容易去搞清楚整个模块到底要做什么，为什么要这样做。
## 资料
[前端开发-领域驱动设计 - 掘金](https://juejin.cn/post/6844903618680881165)<br />[领域驱动设计(DDD)-基础思想](https://zhuanlan.zhihu.com/p/109114670)<br />[阿里技术专家详解 DDD 系列 第一讲- Domain Primitive](https://zhuanlan.zhihu.com/p/340911587)<br />[前端架构之 React 领域驱动设计](https://mp.weixin.qq.com/s/B4l3SeiXU41uLm0jcHJPCA)


## 






