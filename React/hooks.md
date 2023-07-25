汇总一些平时会经常用到的通用hook。
### useBoolean
方便管理boolean值类型的state。
```typescript
import { useMemo } from 'react'
import useToggle from './useToggle'

export interface Actions {
	setTrue: () => void
	setFalse: () => void
	toggle: (value?: boolean | undefined) => void
}

function useBoolean(defaultValue = false): [boolean, Actions] {
	const [state, { toggle }] = useToggle(defaultValue)

	const actions: Actions = useMemo(() => {
		const setTrue = () => toggle(true)
		const setFalse = () => toggle(false)
		return { setTrue, setFalse, toggle }
	}, [toggle])

	return [state, actions]
}

export default useBoolean
```

useToggle
```typescript
import { useState, useMemo } from 'react'

type IState = string | number | boolean | undefined

export interface Actions<T = IState> {
	setLeft: () => void
	setRight: () => void
	toggle: (value?: T) => void
}

function useToggle<T = boolean | undefined>(): [boolean, Actions<T>]

function useToggle<T = IState>(defaultValue: T): [T, Actions<T>]

function useToggle<T = IState, U = IState>(defaultValue: T, reverseValue: U): [T | U, Actions<T | U>]

function useToggle<D extends IState = IState, R extends IState = IState>(
	defaultValue: D = false as D,
	reverseValue?: R
) {
	const [state, setState] = useState<D | R>(defaultValue)
	const reverseValueOrigin = useMemo(() => (reverseValue === undefined ? !defaultValue : reverseValue) as D | R, [
		reverseValue,
		defaultValue
	])

	const actions = useMemo(() => {
		// 切换返回值
		const toggle = (value?: D | R) => {
			// 强制返回状态值，适用于点击操作
			if (value !== undefined) {
				setState(value)
				return
			}
			setState(s => (s === defaultValue ? reverseValueOrigin : defaultValue))
		}
		// 设置默认值
		const setLeft = () => setState(defaultValue)
		// 设置取反值
		const setRight = () => setState(reverseValueOrigin)
		return {
			toggle,
			setLeft,
			setRight
		}
	}, [setState, defaultValue, reverseValueOrigin])

	return [state, actions]
}

export default useToggle

```

使用
```typescript
import React, { useEffect } from 'react'
import { useBoolean } from '@/hooks'


const Demo: React.FC = () => {
	const [visible, { toggle: toggleVisible }] = useBoolean(false)

	useEffect(() => {
		getUserList()
			.then(() => {})
			.catch(() => {})
			.finally(() => {
				toggleVisible()
			})
	}, [])

	return <div></div>
}

export default Demo
```


### useCallbackState
      类组件中的setState接收第二个参数，是一个在state更新之后调用的方法。函数式组件中有时候也想在state更新后进行某些操作。
```typescript
import { useRef, useState, useEffect } from 'react'

function useCallbackState<T>(state: T): [T, (d: T, callback?: (data: T) => void) => void] {
	const callbackRef = useRef<any>()
	const [data, setData] = useState<T>(state)

	useEffect(() => {
		callbackRef.current && callbackRef.current(data)
	}, [data])

	return [
		data,
		(d: T, callback?: Function): T => {
			callbackRef.current = callback || null
			setData(d)
			return d
		},
	]
}

export default useCallbackState
```

使用
```typescript
import React, { useState } from 'react'
import { useCallbackState } from '@/hooks'


const Demo: React.FC = () => {
	const [username, setUsername] = useCallbackState('')

	const handleChangeUsername = () => {
		setUsername('user', () => {
			console.log('username已更新')
		})
	}

	return <div onClick={handleChangeUsername}></div>
}

export default Demo
```

### useCreation
useMemo 或者 useRef的替代品，可以用来创建一些常量。因为useMemo不能保证被Memo的值一定不会重新计算。useRef也会出现重新渲染的性能问题：const ref = useRef(new MyObject()) 每次组件更新的时候会重新渲染，都会实例化。

```typescript
import { useRef } from 'react'

export default function useCreation<T>(factory: () => T, deps: any[]) {
	const { current } = useRef({
		deps,
		obj: undefined as undefined | T,
		initialized: false
	})
	if (current.initialized === false || !depsAreSame(current.deps, deps)) {
		current.deps = deps
		current.obj = factory()
		current.initialized = true
	}
	return current.obj as T
}

function depsAreSame(oldDeps: any[], deps: any[]): boolean {
	if (oldDeps === deps) return true
	for (const i in oldDeps) {
		if (oldDeps[i] !== deps[i]) return false
	}
	return true
}
```
### <br />
使用
```typescript
import React, { useState } from 'react'
import { useCreation } from '@/hooks'


const Demo: React.FC = () => {
	const [username, setUsername] = useState('')
	const [nickname, setNickname] = useState('')

	const person = useCreation(() => {
		name: username + nickname
	}, [username, nickname])

	return <div></div>
}

export default Demo
```

### useClickOutside
点击目标元素之外进行的操作，比如关闭模态窗。
```typescript
import { RefObject, useEffect, useCallback } from 'react'

type EventType = MouseEvent | TouchEvent

const useClickOutside = (
	target: RefObject<HTMLElement | null>,
	onClickOutside: (event: EventType) => void,
	eventName: string = 'click'
) => {
	const handler = useCallback(
		event => {
			const el = target.current
			if (!el || el.contains(event.target)) {
				return
			}
			onClickOutside(event)
		},
		[onClickOutside, target]
	)

	useEffect(() => {
		document.addEventListener(eventName, handler)
		return () => {
			document.removeEventListener(eventName, handler)
		}
	})
}

export default useClickOutside
```

使用：
```typescript
import React, { useRef } from 'react'
import { useOnClickOutside } from 'hooks'

const Demo: React.FC = () => {
	const wrapRef = useRef<HTMLDivElement>(null)
	// 下拉列表是否显示
	const [listVisible, setListVisible] = useState(false)
	
  useOnClickOutside(wrapRef, () => setListVisible(false))
	
	return (
		<div ref={wrapRef}></div>
	)
}

export default Demo
```

### useComputed
借助useMemo实现计算属性。
```typescript
import { useMemo } from 'react'

function useComputed<T>(initValue: () => T, dep?: React.DependencyList): T {
	return useMemo(() => initValue(), dep)
}

export default useComputed
```

使用：
```typescript
import React, { useState } from 'react'
import { useComputed } from '@/hooks'


const Demo: React.FC = () => {
	const [username, setUsername] = useState('')
	const [nickname, setNickname] = useState('')

	const fullname = useComputed(() => username + nickname, [username, nickname])

	return <div>{fullname}</div>
}

export default Demo
```

### useCounter
计数器
```typescript
import { useMemo, useState } from 'react'
import useCreation from './useCreation'

export interface Options {
	min?: number
	max?: number
}

export interface Actions {
	inc: (delta?: number) => void
	dec: (delta?: number) => void
	set: (value: number | ((c: number) => number)) => void
	reset: () => void
}

function useCounter(initialValue: number = 0, options: Options = {}): [number, Actions] {
	const { min, max } = options

	const init = useCreation(() => {
		if (typeof max === 'number') {
			return Math.min(max, initialValue)
		}
		if (typeof min === 'number') {
			return Math.max(min, initialValue)
		}
		return initialValue
	}, [])

	const [current, setCurrent] = useState(init)

	const actions = useMemo(() => {
		const setValue = (value: number | ((c: number) => number)) => {
			setCurrent((c: number) => {
				let target = typeof value === 'number' ? value : value(c)
				if (typeof max === 'number') {
					target = Math.min(max, target)
				}
				if (typeof min === 'number') {
					target = Math.max(min, target)
				}
				return target
			})
		}
		const inc = (delta: number = 1) => {
			setValue(c => c + delta)
		}
		const dec = (delta: number = 1) => {
			setValue(c => c - delta)
		}
		const set = (value: number | ((c: number) => number)) => {
			setValue(value)
		}
		const reset = () => {
			setValue(init)
		}
		return { inc, dec, set, reset }
	}, [init, max, min])

	return [current, actions]
}

export default useCounter
```

使用：
```typescript
import React from 'react'
import { useCounter } from '@/hooks'

const Demo: React.FC = () => {
	const [count, { inc: addCount }] = useCounter()

	const handleAdd = () => {
		addCount()
	}

	return <div onClick={handleAdd}>{count}</div>
}

export default Demo

```

### useCountDown
倒计时
```typescript
import { useEffect, useMemo, useState } from 'react'
import dayjs from 'dayjs'
import usePersistFn from '../usePersistFn'

export type TDate = Date | number | string | undefined

export type Options = {
	targetDate?: TDate
	interval?: number
	onEnd?: () => void
}

export interface FormattedRes {
	days: number
	hours: number
	minutes: number
	seconds: number
	milliseconds: number
}

const calcLeft = (t?: TDate) => {
	if (!t) {
		return 0
	}
	const left = dayjs(t).valueOf() - new Date().getTime()
	if (left < 0) {
		return 0
	}
	return left
}

const parseMs = (milliseconds: number): FormattedRes => {
	return {
		days: Math.floor(milliseconds / 86400000),
		hours: Math.floor(milliseconds / 3600000) % 24,
		minutes: Math.floor(milliseconds / 60000) % 60,
		seconds: Math.floor(milliseconds / 1000) % 60,
		milliseconds: Math.floor(milliseconds) % 1000,
	}
}

const useCountdown = (options?: Options) => {
	const { targetDate, interval = 1000, onEnd } = options || {}

	const [target, setTargetDate] = useState<TDate>(targetDate)
	const [timeLeft, setTimeLeft] = useState(() => calcLeft(target))

	const onEndPersistFn = usePersistFn(() => {
		if (onEnd) {
			onEnd()
		}
	})

	useEffect(() => {
		if (!target) {
			setTimeLeft(0)
			return
		}

		// 立即执行一次
		setTimeLeft(calcLeft(target))

		const timer = setInterval(() => {
			const targetLeft = calcLeft(target)
			setTimeLeft(targetLeft)
			if (targetLeft === 0) {
				clearInterval(timer)
				onEndPersistFn()
			}
		}, interval)

		return () => clearInterval(timer)
	}, [target, interval, onEndPersistFn])

	const formattedRes = useMemo(() => {
		return parseMs(timeLeft)
	}, [timeLeft])

	return [timeLeft, setTargetDate, formattedRes] as const
}

export default useCountdown

```

### useDebounce
```javascript
const useDebounce = <T extends (...args: any[]) => any>(func: T, delay: number = 200, deps: any[] = []): [T, () => void] => {
    const timer = useRef<number>()
    const cancel = useCallback(() => {
        if (timer.current) {
            clearTimeout(timer.current)
        }
    }, [])

    const run = useCallback((...args) => {
        cancel()
        timer.current = window.setTimeout(() => {
            func(...args)
        }, delay)
    }, deps)
    return [run as T, cancel]
}

```

### usePersistFn
持久化 function 的hook。<br />[如何从useCallBack读取一个需要经常变化的值](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-to-read-an-often-changing-value-from-usecallback)
```typescript
import { useRef } from 'react'

export type noop = (...args: any[]) => any

function usePersistFn<T extends noop>(fn: T) {
	const fnRef = useRef<T>(fn)
	fnRef.current = fn

	const persistFn = useRef<T>()
	if (!persistFn.current) {
		persistFn.current = function (this: unknown, ...args) {
			return fnRef.current!.apply(this, args)
		} as T
	}

	return persistFn.current!
}

export default usePersistFn
```

### useRefresh
 手动刷新组件
```typescript
import { useState, useCallback } from 'react'

function useRefresh() {
	const [, setValue] = useState(0)

	return useCallback(() => {
		setValue(val => (val + 1) % (Number.MAX_SAFE_INTEGER - 1))
	}, [])
}

export default useRefresh
```

使用
```typescript
import React from 'react'
import { useRefresh } from '@/hooks'

const Demo: React.FC = () => {
const forceRefresh = useRefresh()

	const handleRefresh = () => {
		forceRefresh()
	}

	return <div onClick={handleRefresh}>清空</div>
}

export default Demo
```


### useList
 	方便地管理列表类的状态。
```typescript
import { useState } from 'react'

export interface Actions<T> {
	set: (list: T[]) => void
	clear: () => void
	updateAt: (index: number, item: T) => void
	remove: (index: number) => void
	push: (item: T) => void
	filter: (fn: (value: T) => boolean) => void
	sort: (fn?: (a: T, b: T) => number) => void
}

const useList = <T>(initialList: T[] = []): [T[], Actions<T>] => {
	const [list, set] = useState<T[]>(initialList)

	return [
		list,
		{
			set,
			clear: () => set([]),
			updateAt: (index, elem) =>
				set(currentList => [...currentList.slice(0, index), elem, ...list.slice(index + 1)]),
			remove: index => set(currentList => [...currentList.slice(0, index), ...list.slice(index + 1)]),
			push: elem => set(currentList => [...currentList, elem]),
			filter: fn => set(currentList => currentList.filter(fn)),
			sort: (fn?) => set(currentList => [...currentList].sort(fn))
		}
	]
}

export default useList
```

使用：
```typescript
import React from 'react'
import { useList } from '@/hooks'

const Demo: React.FC = () => {
	const [goodsList, { clear: clearGoodsList }] = useList([{ id: 1, name: '梨子', price: 10 }])

	const handleClear = () => {
		clearGoodsList()
	}

	return <div onClick={handleClear}>清空</div>
}

export default Demo
```
### 
### useSetState
方便地修改对象类型的状态。
```typescript
import { useCallback, useState } from 'react'

const useSetState = <T extends object>(
	initialState: T = {} as T
): [T, (patch: Partial<T> | ((prevState: T) => Partial<T>)) => void] => {
	const [state, setState] = useState<T>(initialState)
	const setMergeState = useCallback(
		patch => {
			setState(prevState => Object.assign({}, prevState, patch instanceof Function ? patch(prevState) : patch))
		},
		[setState]
	)

	return [state, setMergeState]
}

export default useSetState
```

使用：
```typescript
import React, { useEffect } from 'react'
import { useSetState } from '@/hooks'

const Demo: React.FC = () => {
	const [params, setParams] = useSetState({ page: 1, pageSize: 20 })

	useEffect(() => {
		getList(params).them(() => {})
	}, [params])

	const handlePagination = () => {
		setParams({
			pageSize: params.pageSize + 1
		})
	}

	return <div onClick={handlePagination}>翻页</div>
}

export default Demo

```

### useUnmount
组件销毁时需要执行的操作。
```typescript
import { useEffect } from 'react'
import usePersistFn from '../usePersistFn'
import _ from 'lodash'

const useUnmount = (fn: any) => {
	const fnPersist = usePersistFn(fn)

	useEffect(
		() => () => {
			if (_.isFunction(fnPersist)) {
				fnPersist()
			}
		},
		[fnPersist],
	)
}

export default useUnmount
```

### useVisibleChange
很多时候需要在模态窗打开或者关闭的时候执行一些操作，但又不是简单的监听visible的值是true还是false，必须是由true变为false，或者false变为true的时候才执行。
```typescript
import { useRef, useEffect, useCallback } from 'react'


export const useVisibleChange = ({ visible, open, close }: { visible: boolean; open?: () => void; close?: () => void }) => {
	// 记录上一次的visible
	const prevVisibleRef = useRef<boolean | undefined>()

	useEffect(() => {
		prevVisibleRef.current = visible
	}, [visible])

	const prevVisible = prevVisibleRef.current

	useEffect(
		useCallback(() => {
			if (!visible && prevVisible) {
				close?.()
			} else if (visible && !prevVisible) {
				open?.()
			}
		}, [visible, close, open, prevVisible]),
		[visible],
	)
}

export default useVisibleChange
```

使用：
```typescript
	useActionOnVisibleChange({
    visible,
		close: () => setUsername('')
	})
```
