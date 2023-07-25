
TypeScript 是JavaScript的一个超集，扩展了JavaScript的语法。因此已有的JavaScript代码可以和TypeScript一起运行无需任何修改。TypeScript通过类型注解提供编译时的静态类型检查。

可通过tsc命令来查看ts文件编译后的js文件。
安装typescript：
:::info
npm install -g typescript
:::
执行tsc命令编译ts文件：
:::info
tsc filename.ts
:::

## 一、基础类型
```typescript
// 布尔值
const isShow: boolean = false

// 数值
const age: number = 6

// 字符串
const fileName: string = 'bob'

// 数组
const arr1: number[] = [1, 2, 3]
const arr2: Array<number> = [1, 2, 3]

// 元组
const tuple: [string, number, string] = ['hello', 10, 'www']

// 枚举
enum Color {
  red,
  green,
  blue,
}

// any
let notSure: any

// void
const warnUser = (): void => {}

// null、undefined
const u: undefined = undefined
const n: null = null
```
不要使用类型Number，String，Boolean或Object， 这些类型指的是非原始的包装对象，

### 字面量类型定义
实际开发中经常遇到诸如状态、类型这样的字段，它们的值都是一系列固定的值，可以通过以下几种方式来指定。
##### 1.使用"|"操作符
直接在接口中指定值，适用于该状态属性只在一个地方用到的情况。
```typescript
interface IButton{
  shape?: 'circle' | 'round'
}

const btn: IButton = {
  shape: 'circle'
}
```

##### 2.自定义类型
适用于在多个地方用到该字段的情况
```typescript
export type ButtonShape = 'circle'  | 'round'

interface IBaseButton {
  shape?: ButtonShape;
}

interface IAdvButton {
  shape?: ButtonShape;
}


const btn: IBaseButton = {
  shape: 'circle'
}



```

##### 3.定义元组
适用于值的类型很多的情况，避免总是写“|”操作符。
```typescript
const tupleStr = <T extends string[]>(...args: T) => args;
const tupleNum = <T extends number[]>(...args: T) => args

const buttonShape = tupleStr('circle',  'round');
// 定义了一个ButtonShape类型，表示只接受'circle',  'round'这个两个字符串值
// buttonShape是一个具体的值 ['circle', 'round']，
// typeof结合索引类型得到类型ButtonShape
type ButtonShape = (typeof buttonShape)[number];

interface IButton {
  shape?: ButtonShape;
}

const btn: IButton = {
  shape: 'circle'
}
```
```typescript
// 将字符串列表映射至 `K: V`
const enumStr = <T extends string>(o: Array<T>): { [K in T]: K } =>
    o.reduce((acc, key) => {
        acc[key] = key
        return acc
    }, Object.create(null))


const enumNum = <T extends number>(o: Array<T>): { [K in T]: K } =>
    o.reduce((acc, key) => {
        acc[key] = key
        return acc
    }, Object.create(null))


const buttonShape = enumStr(['circle',  'round']);  // {circle: 'circle', round: 'round'}

type ButtonShape = keyof typeof buttonShape

const btn: IButton = {
  shape: 'circle'
}
```
使用这种方式需要直接将参数数组传入，不能从外边先定义好数组然后传入：
```typescript

// 无效, 因为arr会被推断为string[]
const arr = ['circle',  'round']
const buttonShape = enumStr(['circle',  'round']);
```


如果项目中很多个组件之间出于通信需求，可以编写一个事件管理器，而事件名称则严格定义在这个管理器中。
自定义事件多了以后会导致很难维护，在某一个组件中触发了某个事件，而事件监听者在另一个隐藏的很深的组件中，很难从全局查看项目中到底维护了哪些自定义事件。所以在一个统一的地方定义好每个事件的名称尤为重要，既可以在使用的时候方便快速的通过类型提示传入需要的事件名，又可以在这里一览所有的自定义事件，
```typescript
// 项目中所有自定义事件名称
const eventName = tupleStr(
	// 设置模式
	'changeMode',
	// 设置主题
	'changeTheme'
)

type EventName = typeof eventName[number]

class Event {
	public eventName: EventName
	public handler: (...args: any[]) => void

	constructor(eventName: EventName, handler: (...args: any[]) => void) {
		this.eventName = eventName
		this.handler = handler
	}
}

class EventBus {
	private events: Event[] = []

	public on(eventName: EventName, handler: (...args: any[]) => void) {
		const event = new Event(eventName, handler)
		this.events.push(event)
		const eventIndex = this.events.length - 1
		return () => {
			this.events.splice(eventIndex, 1)
		}
	}

	public off(eventName: EventName) {
		const eventIndex = this.events.findIndex(item => item.eventName === eventName)
		this.events.splice(eventIndex, 1)
	}

	public emit(eventName: EventName, ...args: any[]) {
		const event = this.events.find(item => item.eventName === eventName)!
		event.handler(...args)
	}
}

const eventBus = new EventBus()

/**
 * @example

```
useEffect(() => {
	const removeHandler = eventEmitter.on('change_mode', () => {})
	return removeHandler
}, [])
```
 */

export default eventBus
```

## 二、类型推断
如果没有明确的指定类型，那么 TypeScript 会依照类型推论的规则推断出一个类型。
```typescript
let num = 7   // num 被推断为number类型
num = 'serven'  //不能将类型“string”分配给类型“number”。ts(2322)
```

```typescript
// 函数返回值被推断为string类型
const greeter = (name: string) => {
  return 'hello' + name
}

```

## 三、断言
如果比TypeScript的类型推断更加肯定并清楚的知道某个值的详细信息，可以使用类型断言指定类型。

### 类型断言
类型断言有两种形式。一是“尖括号”语法：
```typescript
const someValue: any = "this is a string";
const strLength: number = (<string>someValue).length;
```
另一个为as语法：
```typescript
const someValue: any = 'this is a string'
const strLength: number = (someValue as string).length
```

#### 实际应用
```typescript
// 一个IPerson接口，指定了每个属性都是必填
interface IPerson {
	name: string
	age: number
}


// 初始化状态的时候，需要给每个属性指定初始值。
const tom = ref<IPerson>({
	name: '',
	age: 0,
})

const jack = ref<IPerson>({} as IPerson)

//  或者使用Partial将每个属性变为可选值
const peter = ref<Partial<IPerson>>({})

```

### 非空断言
```typescript
const getScrollTop = (elem: HTMLElement | Window) => {
  if (elem === window) {
    const top = window.pageYOffset || document.body.scrollTop || document.documentElement!.scrollTop
    console.log(top)
  }
  //  将一个联合类型的变量指定为一个更加具体的类型
  const top = (elem as HTMLElement).scrollTop
  console.log(top)
}

const box = document.getElementById('box')
//  类型推断box为 HTMLElement | null
//  null值不能作为getScrollTop方法的参数，如果可以肯定页面中存在box这样一个元素，则可以使用类型断言，指明box一定存在。
getScrollTop(box!)
```

#### 实际应用
```typescript
interface IPerson {
  id: number
  name: string
}

// person列表
const personList: IPerson[] = []

// 通过id获取name
const getPersonName = (id: number) => {
  const person = personList.find((item) => item.id === id)
  //  因为id来自于点击的person列表中的某一项，而又是从person列表中去查询，那么必定可以找到这么一个person。
  // 但是数组的find()方法又可能返回undefined值，所以可以通过类型断言来指定查询结果一定存在
  return person!.name
  // 如果顺应TypeScript，也可以假设person可能不存在，使用可选链式操作进行处理。
  // return  person?.name
  
}

// 点击person中的某一项，调用getPersonName()方法
getPersonName(123)
```

## 四、接口

使用接口（Interfaces）来定义对象的类型。
```typescript
interface IPerson {
	name: string
	age: number
}
```
定义了一个接口 IPerson，接着定义了一个变量 tom，它的类型是 IPerson 。这样，就规定了 tom 的形状必须和接口 IPerson 一致。
```typescript
// 不允许缺少属性
const tom: IPerson = {
	name: 'Tom',
}

// 多一些属性也不被允许
const tom: IPerson = {
	name: 'Tom',
	age: 25,
	gender: 'male',
}
```

### 可选属性
	接口里的属性不全都是必需的。
```typescript
interface IPerson {
	name: string
	age?: number
}

// 正确
const tom: IPerson = {
	name: 'Tom',
}
```

### 任意属性
有时候希望一个接口允许有任意的属性。
```typescript
interface IPerson {
    name: string;
    age?: number;
    [propName: string]: any;
}

const tom: IPerson = {
  name: 'Tom',
  gender: 'male',
  job: 'FED',
  body: {
    weight: 60,
    height: 150
  }
};


```
一旦定义了任意属性，那么确定属性和可选属性都必须是它的子属性：
```typescript
interface IPerson {
    name: string;
    age?: number;
    [propName: string]: string;
}

// 错误：任意属性的值允许是string，但是可选属性age的值却是number，number不是string的子属性。
const tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

```

#### 实际应用
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659366255090-48346abc-f8a8-4208-9d67-76b69141d2ce.png#clientId=u3f62b2ca-8225-4&errorMessage=unknown%20error&from=paste&height=155&id=u3aa6bfa6&originHeight=347&originWidth=1504&originalType=binary&ratio=1&rotation=0&showTitle=false&size=20633&status=error&style=none&taskId=ub76897e1-82e4-49e4-94e6-7bad32a37d3&title=&width=671)
```typescript
export interface IPvc {
// 标签
	labels: {
		[key: string]: string
	}
  // 注释
	annotations: {
		[key: string]: string
	}
}
```

### **函数类型接口**
接口也可以用来描述函数类型。
```typescript
interface SearchFunc {
	(source: string, subString: string): boolean
}

const mySearch: SearchFunc = (source: string, subString: string) => {
	return source.search(subString) > -1
}

```

### **接口继承接口**
接口也可以相互继承， 这样就能够从一个接口里复制属性到另一个接口里。
```typescript
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}
```

一个接口可以继承多个接口:
```typescript
interface IShape {
  color: string;
}
interface IPenStroke {
  penWidth: number;
}

interface ISquare extends IShape, IPenStroke {
  sideLength: number;
}
```

#### 实际应用
定义Person模型：
```typescript
// model/person.ts
interface IPerson {
  name: string
  age: number
}

```
视图层有一个person表格，可以勾选每一个人，需要一个checked属性，可以通过继承的方式来增加这样一个交互上的checked属性。
当然如果Person接口只在PersonList用到，或者接口本身比较简单，也可以偷懒直接将checked定义在Person接口上。但是model模块中最好只定义业务相关的属性，交互属性可以放在具体的视图层进行定义。
```vue
// PersonList.vue
<script lang="ts" setup>
  import { ref } from 'vue'
  import { IPerson } from '@/model/person'
  
  interface IPersonItem extends IPerson {
    checked: boolean
  }
  
  const personList = ref<IPersonItem[]>([])
</script>
```

继承一个div的所有props：
```typescript
interface IProps extends React.HTMLAttributes<HTMLDivElement> {
    text:string
}

// FlexibleText组件
const FlexibleText: React.FC<IProps> = (props: IProps) => {
    const { text, ...reset } = props
    return <div {...reset}>{text}</div>
}
```

### **接口继承类**
接口也可以继承一个类类型。
```typescript
class Person {
  name: string = ''
  age: number = 0
}

interface Student extends Person {
  school: string
}

const student: Student = {
  name: 'Tom',
  age: 20,
  school:'pk'
}
```
#### 实际应用
React的类组件中，在state文件中统一定义组件的状态：
```typescript
//  state.ts
class State {
  // 项目名称
  public projectName: string;
  // 项目id
  public projectId: number;
  public submitLoading: boolean = false;
  // 支付id
  public payId?: number;
}

// 初始化状态
export const initState = { ...new State() };
// IState接口继承了类State
export interface IState extends State {}
```
```tsx
//Project.tsx
import { RouteComponentProps } from "react-router-dom";
import { initState, IState } from "./State";

interface IProps extends RouteComponentProps {}

class ProjectDetail extends React.Component<IProps, IState> {
  public state = initState;

  constructor() {}

  public render() {}
}

export default ProjectDetail
```

为什么接口可以直接继承一个类？因为在TypeScript中，类本身就可以直接作为接口来使用：
```typescript
class Point {
  public x: number = 0;
  public y: number = 0;
}

// 直接使用类作为变量的类型
const point2d: Point = { x: 1, y: 2 };
```
```typescript
import ReconnectingWebSocket from 'reconnecting-websocket'

const ws: ReconnectingWebSocket = new ReconnectingWebSocket(url)
```
	

同时类也可以实现接口：
```typescript
interface IClock {
	currentTime: Date
	setTime: (d: Date) => void
}
  

class Clock implements IClock {
	currentTime: Date = new Date()
	setTime(date: Date) {
		this.currentTime = date
	}
	constructor(hour: number) {}
}
```

## 五、泛型
泛型是一种**类型变量**，只用于表示类型而不是值。

一个identity函数，不用泛型：
```typescript
function identity(arg: number): number {
  return arg + 1;
}
```
如果不能确定arg到底是什么类型，使用any来定义函数：
```typescript
function identity(arg: any): any{
  return arg;
}
```
使用any类型会导致这个函数可以接收任何类型的arg参数，这样就丢失了一些信息：传入的类型与返回的类型应该是相同的。需要一种方法使返回值的类型与传入参数的类型是相同的。 
```typescript
function identity<T>(arg: T): T {
    return arg;
}
```
给identity添加了类型变量T。 T能够捕获用户传入的类型（比如：number），之后就可以使用这个类型。
把这个版本的identity函数叫做泛型，因为它可以适用于多个类型。不同于使用any，它不会丢失信息，从而保持准确性，传入数值类型并返回数值类型。

#### 实际应用
```typescript
// hooks/useConfirm.ts
import { h, render } from 'vue'
import BaseConfirm from '@/components/base/BaseConfirm.vue'

const container = document.createElement('div')

// 泛型接口
// 事先不确定row的类型是什么，只有在具体调用onConfirm()方法的时候才能确定其类型
interface IOptions<T> {
  onConfirm: (row: T) => Promise<void>
}

const useConfirm = <T>(options: Partial<IOptions<T>>) => {
  const handler = (row: T) => {
    const { onConfirm } = options

    const confirmNode = h(BaseConfirm, {
      async onConfirm() {
        if (onConfirm) {
          await onConfirm(row)
        }
      },
    })

    render(confirmNode, container)
  }

  return handler
}

// 使用的时候，row的类型就可以确定为IHpa
const handleRemoveHpa = useConfirm({
  onConfirm: async (row: IHpa) => {}
})

// row的类型就可以确定为IPvc
const handleRemovePvc = useConfirm({
  onConfirm: async (row: IPvc) => {}
})
```

```typescript
function createArray<T>(length: number, value: T): Array<T> {
  const result: T[] = [];
  for (let i = 0; i < length; i++) {
      result[i] = value;
  }
  return result;
}

// 这样arr就会被推断为一个确定的字符串数组，从而避免类型丢失的情况。
cosnt arr = createArray(3, 'x');  // ['x', 'x', 'x']

```

### 泛型类
泛型除了在接口中使用，也可以在定义类的时候使用：
```typescript
class GenericNumber<T> {
	zeroValue: T
	add: (x: T, y: T) => T
}

// 实例化类，并且指定T代表的类型是number
let myGenericNumber = new GenericNumber<number>()
myGenericNumber.zeroValue = 0
myGenericNumber.add = (x, y) => x + y
```
使用泛型类实现泛型接口：
```typescript
// 泛型接口
interface DB<T>{
  accounts:T[];
  add(account:T):boolean;
}

// 泛型类
// 要实现泛型接口，类也必须是泛型类
class MysqlDb<T> implements DB<T> {
  accounts: T[] = []
  add(account: T): boolean {
    this.accounts.push(account);
    return true;
  }
  getAccounts(): T[] {
    return this.accounts;
  }
}

let db = new MysqlDb<Account>();
```

### 泛型参数的默认值
可以为泛型中的类型参数指定默认类型。当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用。
```typescript
// 指定泛型T的默认类型为string
function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
```


## 六、常用内置工具类型
### typeof
一般都是先定义类型，再去赋值使用：
```typescript

interface IOptions{
  a: number
}

const options:IOptions = {
  a: 1
}
```
使用 typeof 可以把使用顺序倒过来:
```typescript
const options = {
  a: 1
}
// 直接根据一个具体的值定义类型
type Options = typeof options

```
#### 实际应用
在项目中的@types模块定义全局类型：
```typescript
declare global {
	interface Window {
    // 根据apis对象来生成类型
		$apis: typeof import('@/apis')['default']
		$message: typeof import('@/utils/message')['default']
	}
}

export {}
```

### Partial
Partial用于将一个接口里的所有属性变为可选属性，具体实现：
```typescript
// 使用泛型和映射类型keyof实现Partial
type Partial<T> = {
    [P in keyof T]?: T[P];
}

```

   - keyof T拿到T所有属性名, 
   - 然后in进行遍历, 将值赋给P ,
   - 最后T[P]索引取得相应属性的值，
   - 中间的 ? 用来进行设置为可选值。

#### 实际应用
如果 props 所有的属性值都是可选的，可以借助 Partial 这样实现：
```tsx
import { MouseEvent } from 'react'
import * as React from 'react'

interface IProps {
  color: 'red' | 'blue' | 'yellow',
  onClick(event: MouseEvent<HTMLDivElement>): void,
}

const Button: SFC<Partial<IProps>> = ({ onClick, children, color }) => {
  return (
    <div onClick={onClick}>
      {children}
    </div>
  )

```

### **Required**
Required的作用和Partial相反。使用Required将所有 props 属性都设为必填项
```typescript
type Required<T> = {
	[P in keyof T]-?: T[P]
}
```
-? 的功能就是把可选属性的 ? 去掉使该属性变成必选项，对应的还有 +? ，作用与 -? 相反，是把属性变为可选项。

### **Exclude**
从T中排除那些可以赋值给 U 的类型。
```typescript
type Exclude<T, U> = T extends U ? never : T
```
使用：
```typescript
// 此时 T 类型的值只可以为 1 、2 、 5 。
type T = Exclude<1 | 2 | 3 | 4 | 5, 3 | 4>   // T = 1 | 2 | 5
type T = Exclude<'a' | 'b' | 'c' | 'd', 'a' | 'c' | 'f'>  // "b" | "d"
type T = Exclude<string | number | (() => void), Function> // string | number

```

### Extract
从 T 中提取那些可以赋值给 U 的类型。
```typescript
type Extract<T, U> = T extends U ? T : never;
```
使用：
```typescript
//此时T类型的值只可以为 3 、4。
type T = Extract<1 | 2 | 3 | 4 | 5, 3 | 4> // T = 3 | 4
type T = Extract<'a' | 'b' | 'c' | 'd', 'a' | 'c' | 'f'>  // "a" | "c"
type T = Extract<string | number | (() => void), Function> // () => void
```

### Pick
从 T 中取出一系列 K 的属性。
```typescript
type Pick<T, K extends keyof T> = {
  [P in K]: T[P]
}
```
Pick和Extract的区别：Extract是从字面量类型中挑选类型；Pick是从类型的属性中挑选属性。
#### 
#### 实际应用
假如现在有一个类型其拥有 name 、 age 、 sex 属性，当想生成一个新的类型只支持 name 、age 时可以像下面这样：
```typescript
interface Person {
  name: string,
  age: number,
  gender: string,
}

const person: Pick<Person, 'name' | 'age'> = {
  name: '小王',
  age: 21,
}

```

### **Record**
将 K 中所有的属性的值转化为T类型。
```typescript
type Record<K extends keyof any, T> = {
  [P in K]: T
}
```
#### 实际应用
`将 name 、 age 属性全部设为 string 类型：
```typescript
const person: Record<'name' | 'age', string> = {
  name: '小王',
  age: '12',
}

```

### **Omit（没有内置）**
从对象T中排除key为K的属性。TS中没有内置，使用Pick和Exclude实现。
```typescript
type Omit<T, K> = Pick<T, Exclude<keyof T, K>>
```
#### 实际应用
排除 name 属性:
```typescript
interface Person {
  name: string,
  age: number,
  gender: string,
}

let const: Omit<Person, 'name'> = {
  age: 1,
  gender: '男'
}

```

比如想基于第三方组件库中的某个组件做二次封装，可以使用Omit排除掉其原有的属性，并自行定义：
```typescript
import React from 'react'
import {InputProps } from 'antd/lib/input'

// 继承antd的InputProps，但排除onChange
interface IProps extends Omit<InputProps, 'onChange'> {
  // 自定义onChange方法的类型
  onChange?(value: string): void
  
}

// 继承antd的InputProps，但排除onChange和value
interface IProps extends Omit<InputProps, 'onChange' | 'value'> {
    onChange?(value: string): void
}
```
继承原生DOM的属性，并做改造：
```typescript
// 继承了div的所有属性，但是又想自定义className和style
interface IProps extends Omit<React.HTMLAttributes<HTMLDivElement>, 'className' | 'style'> {
    style?: React.CSSProperties
    className?: string[] | string
    // 渐变的起始颜色，与文字颜色保持一致。
    shadowColor?: string
    // 渐变阴影的起始位置,值为相对于宽度的百分比。
    shadowPosition?: number
    // 溢出之后是否显示tooltip
    showTooltip?: boolean
    // 判断是发生了溢出
    checkOverflow?: (isOverFlow: boolean) => void
}

const FlexibleText: React.FC<IProps> = (props: IProps) => {
    const { text, ...reset } = props
    return <div {...reset}>{text}</div>
}

```

## 七、业务模型定义
在Node.js程序中，会划分出一个model模块，专门定义每个接口暴露的数据结构。
```javascript
const mongoose = require('mongoose');

const Schema = mongoose.Schema;
const userSchema = new Schema({
  username: {
    type: String,
    required: true
  },
  password: {
    type: String,
    required: true
  },
  name: {
    type: String,
    required: true
  },
  avatar: String,
  gender: {
    type: String,
    default: '0'
  },
  registerDate: {
    type: Date,
  }
})

module.exports = mongoose.model('User', userSchema);
```
而在JavaScript中，每个模块的数据构成是什么样的，却没有可以定义和查看的地方。在接手一个现有项目的时候，往往是通过控制台查看接口返回的数据，再结合具体的业务代码来理解数据的构成和演化过程。但是这样总有种盲人摸象的感觉，有没有一种更好的途径来帮助我们快速清晰的了解一个模块的根基？

通过TypeScript就可以很好的处理这个问题，但是初次接触TypeScript，又如何去定义每个模块的模型？
	先记住这句话：**_模型一定是前端的模型_**。不要完全照抄后端提供的接口文档来写一遍，一定是前端最终要用到什么样的数据，才把这些数据加入到模型中。

下面通过一个具体的例子来演示从后端接口拿到数据，通过前端业务模型的约束，业务处理，最终在页面上呈现的过程。

### 接口返回的数据
在前端页面中，经常会遇到列表页和详情页，通常列表的数据通过一个接口返回，详情的数据又是一个接口返回，这两个接口中返回的数据并不是完全一致。
服务发现列表接口：
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1660139825687-df62ce64-6996-4a58-99e2-0ca0435d2d0f.png#clientId=ub5f54ad4-894e-4&errorMessage=unknown%20error&from=paste&height=282&id=u5d17f4b4&originHeight=393&originWidth=540&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43724&status=error&style=none&taskId=u1a80b25a-aab4-4081-84d1-2e0aa4b553f&title=&width=387.727294921875)
服务发现详情接口：
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1660139845261-68ef1848-8a34-4ee4-a00f-8fcaaa0e06fc.png#clientId=ub5f54ad4-894e-4&errorMessage=unknown%20error&from=paste&height=203&id=u1aa2da4d&originHeight=266&originWidth=544&originalType=binary&ratio=1&rotation=0&showTitle=false&size=30749&status=error&style=none&taskId=u1e756502-4144-4fb3-8ace-7bcb3639a66&title=&width=414.6363830566406)
可以看到列表接口和详情接口返回的数据并不是完全一致，但是我们没有必要分别为列表和详情各自定义模型，只需要综合两个接口中的数据来定义即可。

### 前端业务模型
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1660229266577-caa0ed73-a266-4ba7-a594-823aa7a67831.png#clientId=uc071265a-4792-4&errorMessage=unknown%20error&from=paste&height=401&id=c2K6T&originHeight=551&originWidth=1160&originalType=binary&ratio=1&rotation=0&showTitle=false&size=36250&status=error&style=none&taskId=u124c42f6-4742-458a-9b9f-5c6aa636338&title=&width=843.6363636363636)
/model/discover
```typescript
//  服务发现端口映射
export interface IDiscoverPort {
  //  端口名称
  name: string
  // 服务端口
  port?: number
  // 协议
  protocol: 'TCP' | 'UDP'
  // 目标端口
  targetPort?: number
  // 主机端口
  nodePort?: number
}

 // 会话保持
export interface ISession {
  enabled: boolean
  timeoutSeconds: number
}

// 服务发现
export interface IDiscover {
  uid: string
  name: string
  namespace: string
  cluster: string
  project: string
  createdTime: string
  // 集群ip
  clusterIP: string
  // 目标域名
  externalName: string
  // 解析类型
  resolveTo: 'externalIP' | 'externalName' | 'serviceAlias' | 'workload' | 'pod'
  ports: IDiscoverPort[]
  // 类型。 默认为ClusterIP
  type: 'ClusterIP' | 'NodePort' | 'Headless'
  // 外部IP
  externalIPs: string[]
  // 外部流量策略
  externalTrafficPolicy: 'Local' | 'Cluster'
  // 对外发布的地址 true：所有接入点 false：仅就绪接入点
  publishNotReadyAddresses: boolean
  //  工作负载
  workload: string
  //  选择器。值为选择的工作负载的selector
  selector: {
    [key: string]: string
  }
  //  标签
  labels: {
    [key: string]: string
  }
  // 注释
  annotations: {
    [key: string]: string
  }
  sessionAffinity: ISession
}
```
我们后端的字段命名不管是从语义还是规范性上都还很好，所以绝大多数字段名还是直接保持其原始性。
每当开始一个新模块的时候，第一步就新建对应的模型，每个字段的名称参照接口文档做好注释，在开发过程中以及后期项目的维护，前端可以直接通过查看模型来了解每个字段的意思。
在IDiscover中，有端口和会话保持，这两个字段都是比较复杂的类型，而且在多个地方会单独的用到它们。所以我们也使用单独的接口去定义。
接口中没有返回workload这个字段，而在我们的模型中新增了这个字段，是因为在前端需要用到它。这里就体现出前端模型并不是和后端接口完全一致的特点。

前端在交互上经常会根据业务逻辑出现是否禁用，是否显示这样的情况，对于交互上的状态，我们通常在视图层组件中定义。
DiscoverPort.vue
```vue
<script lang="ts" setup>
import { IDiscoverPort } from '@/model/service'

interface IPort extends IDiscoverPort {
	//  是否显示目标端口的填写
	showTargetPort: boolean
	//  是否显示主机端口的填写
	showNodePort: boolean
}
</script>

```
在端口组件中，基于业务模型，新增了两个交互上的字段，而这也跟后端接口没有任何关系，都是前端要用的，才去定义。相反，接口有时候也会返回一些无用的数据，我们也就没必要在模型中定义它们。

## 八、TypeScript的好处
总之一句话，使用TypeScript让我很有安全感。
### 开发时的类型提示
```typescript
interface IPerson {
  name: string
  age: number
  gender: '0' | '1'
}

const person: IPerson = {
  name: '小王',
  age: 21,
  gender: '1',
}
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659540710460-3f3a5dd3-f07f-4518-9289-f1e6e1823c2a.png#clientId=uf1064ee2-06cf-4&errorMessage=unknown%20error&from=paste&height=75&id=uc0c8cdee&originHeight=103&originWidth=264&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3744&status=error&style=none&taskId=u8d2addcf-8176-4798-a2ac-f276d71afad&title=&width=192)![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1659540805342-830a1037-67e3-4bdc-a97f-03af16e9dc75.png#clientId=uf1064ee2-06cf-4&errorMessage=unknown%20error&from=paste&height=64&id=uab878799&originHeight=88&originWidth=379&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3719&status=error&style=none&taskId=ufa492984-b680-496c-9cac-e5dd25e9221&title=&width=275.6363636363636)
只需要在模型中定义好要用到的字段，通过点语法就可以快速的调取。再也不用复制粘贴了。


### 避免很多低级bug
```javascript
cosnt permissions = 'aaa'

if( permission === ''aaa ){}
```
像这样的低级错误出现的概率也不小，而且在众多代码中很难发现，明明值是'aaa'，为什么条件判断总是false？如果使用TypeScript会直接报错提示permission不存在。
```javascript
let result

result.value = 'aaa'
```
TypeScript提示result可能不存在，就可以有预计的进行空值处理，可以避免很多“cannot read properties of null”这种一出错就导致白屏的严重问题。

### 接口文档
后端编写的接口文档的一大作用是供前端调试的时候快速的查看每个字段的含义。但是它的作用也就仅仅发挥在项目初次开发的时候。
后期维护的时候还有人看接口文档吗？不管是维护自己开发的项目，还是接手其他项目，基本上都不会再去看。
维护项目的时候接口文档可以放心查阅吗？后端更改或增加了字段，一定会同步去更新接口文档吗？
使用TypeScript进行类型定义可以很大程度上完善接口文档的功能，前端在维护一个项目的时候，基本上通过查看一遍模型就可以清楚的知道哪些字段都是什么意思，状态都有哪些，类型都有哪些，业务模型是什么样的结构。
像这样的一个商户订单模型，根本就不需要再去找接口文档出来看，直接在前端项目中就可以弄清楚整个订单包含的东西。
```typescript
//  支付订单
export interface IPayOrder {
  id: string
  // 平台支付订单号
  platOrderNo: string
  // 渠道支付订单号
  canalsSn?: string
  // 商户支付订单号
  outOrderNo: string
  // 第三方应用ID
  thirdAppId?: string
  // 第三方商户号
  merId: string
  // 商户名称
  merName: string
  // 商户ID
  merchantId: string
  // 应用名称
  appName: string
  // 应用AppID
  appId: string
  // 支付金额
  totalFee: number
  // 手续费
  feeMoney: number
  // 退款金额
  refundFee: number
  // 支付渠道
  paywayCode?: string
  // 支付状态
  orderState: string
  // 创建时间
  createDate: string
  // 订单名称
  subject: string
  // 支付成功时间
  successDate?: string
  //  更新时间
  updateDate: string
}

// 退款订单
export interface IRefundOrder {
  id: string
  // 平台支付订单号
  platOrderNo: string
  // 平台退款订单号
  platRefundNo: string
  // 商户支付订单号
  outOrderNo: string
  // 商户退款订单号
  outRefundNo: string
  // 渠道支付订单号
  canalsSn: string
  // 渠道退款订单号
  canalsRefSn: string
  // 第三方应用ID
  thirdAppId: string
  // 第三方商户号
  merId: string
  // 商户名称
  merName: string
  // 商户ID
  merchantId: string
  // 应用名称
  appName: string
  // 应用AppID
  appId: string
  // 支付金额
  totalFee: number
  // 退款金额
  refundFee: number
  // 支付渠道
  paywayCode: string
  // 退款状态
  refState: string
  // 退款时间
  createDate: string
  // 退款原因
  refundReason?: string
  // 退款成功时间
  successDate?: string
  // 更新时间
  updateDate?: string
}

```
	
