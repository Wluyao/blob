# 溢出阴影
一般地，内容溢出之后采用省略号显示，但是省略号也会占用一定的位置，有没有什么办法能够最大化的使用页面空间。一个很好的效果：<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627800093463-d036094c-036c-48d0-b863-82a6fedb1f5e.png#averageHue=%23d6d6d6&clientId=u8478cd32-7e6a-4&from=paste&height=29&id=u61801a69&originHeight=58&originWidth=308&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5314&status=done&style=none&taskId=uef600f9e-bed3-4ad2-b8e8-d628bcb4427&title=&width=154)<br />在内容溢出时，采用末尾的阴影实现溢出效果。

[https://mp.weixin.qq.com/s/qADnUx3G2tKyMT7iv6qFwg](https://mp.weixin.qq.com/s/qADnUx3G2tKyMT7iv6qFwg)

## 实现
```jsx
import React, { useEffect, useState, useRef, useCallback } from 'react'
import cs from 'classnames'
import { Tooltip } from 'antd'
import { hexToRgb } from '@/utils/core'
import './style.less'

interface IProps extends Omit<React.HTMLAttributes<HTMLDivElement>, 'className'> {
	className?: string[] | string
	// tooltip标题
	title?: string
	// 文本内容的颜色
	textColor?: string
	// 渐变阴影的起始位置,值为相对于宽度的百分比。适用于widthLimit条件下的shadow模式
	shadowPosition?: number
	// 判断是发生了溢出
	checkOverflow?: (isOverFlow: boolean) => void
}

const rootClass = 'overflow'

/**
 * 内容溢出处理
 */
const Overflow: React.FC<IProps> = (props: IProps) => {
	const {
		style,
		className,
		title = '',
		children,
		textColor = '#333',
		shadowPosition = 80,
		checkOverflow,
		...restProps
	} = props

	const overflowRef = useRef(null)

	const [isOverFlow, setIsOverflow] = useState(false)

	useEffect(() => {
		let isOverFlow = false
		const dom: any = overflowRef.current
		isOverFlow = dom.scrollWidth > dom.clientWidth
		setIsOverflow(isOverFlow)
		checkOverflow && checkOverflow(isOverFlow)
	}, [title, children, checkOverflow])

	return (
		<div
			ref={overflowRef}
			className={cs(rootClass, className)}
			style={{
				...style,
				backgroundImage: `linear-gradient(to right, ${textColor} ${
					isOverFlow ? shadowPosition + '%' : '100%'
				}, ${hexToRgb(textColor, 0)})`,
			}}
			{...restProps}
		>
			{children ? children : <>{title}</>}
		</div>
	)
}

interface IWrapProps extends IProps {
	// 溢出之后是否显示tooltip
	showTooltip?: boolean
}

const Wrap: React.FC<IWrapProps> = (props: IWrapProps) => {
	const [isOverFlow, setIsOverflow] = useState(false)
	const { showTooltip = true, title, children, ...restProps } = props

	const checkOverflow = useCallback(
		(value: boolean) => {
			setIsOverflow(value)
			restProps.checkOverflow && restProps.checkOverflow(value)
		},
		[restProps]
	)

	if (isOverFlow && showTooltip) {
		const toolTipTitle = props.title || children
		return (
			<Tooltip title={toolTipTitle}>
				<Overflow {...props} checkOverflow={checkOverflow} />
			</Tooltip>
		)
	}
	return <Overflow {...props} checkOverflow={checkOverflow} />
}

export default React.memo(Wrap)
```

style
```css
.overflow {
	overflow: hidden;
	color: transparent !important;
	white-space: nowrap;
	-webkit-background-clip: text;
}
```

utils
```typescript
export const hexToRGB = (hex: string, alpha: number = 1) => {
	let h: any = hex.slice(hex.startsWith('#') ? 1 : 0)
	if (h.length === 3) h = [...h].map(x => x + x).join('')
	h = parseInt(h, 16)
	return 'rgba' + '(' + (h >>> 16) + ', ' + ((h & 0x00ff00) >>> 8) + ', ' + ((h & 0x0000ff) >>> 0) + ', ' + alpha + ')'
}
```



## 使用
```html
<Overflow>这是动态的文本内容这是动态的文本内容</Overflow>

<Overflow title="第二行内容第二行内容第二行内容第二行内容">
  <div>第一行内容</div>
  <div>第二行内容第二行内容第二行内容第二行内容</div>
</Overflow>
```


## 溢出tooltip
   当内容溢出时，tooltip显示完整内容。

### 实现
```typescript
<script lang="ts" setup>
import { ref } from 'vue'

const emits = defineEmits(['checkOverflow'])

interface IProps {
  title?: string
  // 是否需要显示tootip
  showTooltip?: boolean
  // 是否需要溢出省略
  ellipsis?: boolean
  // 是否立即检测溢出
  immidiate?: boolean
  // 超过多少行之后省略
  rows?: number
  // tooltip内容的最大宽度
  contentWidth?: number
}

const props = withDefaults(defineProps<IProps>(), {
  showTooltip: true,
  ellipsis: true,
  immidiate: false,
  rows: 1,
  contentWidth: 300,
})

const isOverFlow = ref(false)
const wrapRef = ref()
const measureRef = ref()

const styleClamp = computed(() => ({
  '-webkit-line-clamp': props.rows,
}))

watch(
  () => props.rows,
  () => {
    checkOverflow()
  }
)

onMounted(async () => {
  if (props.immidiate) {
    checkOverflow()
  }
})

const checkOverflow = () => {
  if (!wrapRef.value) return
  // 允许1px的偏差
  isOverFlow.value = measureRef.value.clientWidth - wrapRef.value.clientWidth * props.rows > 1
  emits('checkOverflow', isOverFlow.value)
}
  </script>

  <template>
  <div ref="wrapRef" class="relative overflow-hidden break-all" @mouseenter="checkOverflow">
  <div ref="measureRef" class="measure"><slot></slot></div>

  <el-tooltip v-if="showTooltip && isOverFlow" :hide-after="0">
  <template #content>
  <div :style="{ 'max-width': `${contentWidth}px` }">
    <template v-if="title">{{ title }}</template>
  <slot v-else></slot>
  </div>
  </template>

  <div :class="{ ellipsis: ellipsis }" :style="styleClamp"><slot></slot></div>
  </el-tooltip>
  <div v-else :class="{ ellipsis: ellipsis }" :style="styleClamp"><slot></slot></div>
  </div>
  </template>

<style scoped>
.measure {
  position: fixed;
  top: -9999px;
  left: -9999px;
  white-space: nowrap;
}

.ellipsis {
  overflow: hidden;
  word-break: break-all;
  display: -webkit-box;
  -webkit-box-orient: vertical;
}
</style>
```
