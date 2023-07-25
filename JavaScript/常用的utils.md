### 数值
#### 是否可以转换为数值
```javascript
const isNumeric = (value: any) =>
	!isNaN(parseFloat(value)) && isFinite(value)
```



#### 数值使用逗号隔开
```javascript
const getCommaNumber = (value: number) => {
	const list = value.toString().split('.')
	const prefix = list[0].charAt(0) === '-' ? '-' : ''
	let num = prefix ? list[0].slice(1) : list[0]
	let result = ''
	while (num.length > 3) {
		result = `,${num.slice(-3)}${result}`
		num = num.slice(0, num.length - 3)
	}
	if (num) {
		result = num + result
	}
	return `${prefix}${result}${list[1] ? `.${list[1]}` : ''}`
}

console.log(getCommaNumber(123456789)) // 123,456,789
```
### 
#### 数值转换为大写金额
```javascript
const moneyToCN = (data: number) => {
	let value = data
	const fraction = ['角', '分']
	const digit = ['零', '壹', '贰', '叁', '肆', '伍', '陆', '柒', '捌', '玖']
	const unit = [
		['元', '万', '亿'],
		['', '拾', '佰', '仟'],
	]
	const head = value < 0 ? '负' : ''
	value = Math.abs(value)
	let s = ''
	// 循环角、分，按照乘10%10的步骤对数值进行判断；
	for (let i = 0; i < fraction.length; i++) {
		s += (digit[Math.floor(value * 10 * Math.pow(10, i)) % 10] + fraction[i]).replace(/零./, '')
	}
	// 整数部分则角和分位无值，替换为整
	s = s || '整'
	value = Math.floor(value)
	// 整数部分%10余n,便在digit里循环n次，得到对应数字n的大写汉字，如果轮询到零就replace将零剪切，最后将得到的小数部位和整数部位进行拼接.
	for (let i = 0; i < unit[0].length && n > 0; i++) {
		let p = ''
		for (let j = 0; j < unit[1].length && n > 0; j++) {
			p = digit[value % 10] + unit[1][j] + p
			value = Math.floor(value / 10)
		}
		s = p.replace(/(零.)*零$/, '').replace(/^$/, '零') + unit[0][i] + s
	}
	return (
		head +
		s
			.replace(/(零.)*零元/, '元')
			.replace(/(零.)+/g, '零')
			.replace(/^整$/, '零元整')
	)
}

console.log(moneyToCN(-128.97))
```

```javascript
 const moneyToCN = n => {
	if (n == '') return ''
	if (!/^(0|[1-9]\d*)(\.\d+)?$/.test(n)) return '数据非法'
	let unit = '京亿万仟佰拾兆万仟佰拾亿仟佰拾万仟佰拾元角分',
		str = ''
	n += '00'
	let p = n.indexOf('.')
	if (p >= 0) n = n.substring(0, p) + n.substr(p + 1, 2)
	unit = unit.substr(unit.length - n.length)
	for (let i = 0; i < n.length; i++)
		str += '零壹贰叁肆伍陆柒捌玖'.charAt(n.charAt(i)) + unit.charAt(i)
	return str
		.replace(/零(仟|佰|拾|角)/g, '零')
		.replace(/(零)+/g, '零')
		.replace(/零(兆|万|亿|元)/g, '$1')
		.replace(/(兆|亿)万/g, '$1')
		.replace(/(京|兆)亿/g, '$1')
		.replace(/(京)兆/g, '$1')
		.replace(/(京|兆|亿|仟|佰|拾)(万?)(.)仟/g, '$1$2零$3仟')
		.replace(/^元零?|零分/g, '')
		.replace(/(元|角)$/g, '$1整')
}
```
```javascript
class RMB {
	constructor() {
		this.numMap = new Map([
			[0, '零'],
			[1, '壹'],
			[2, '贰'],
			[3, '叁'],
			[4, '肆'],
			[5, '伍'],
			[6, '陆'],
			[7, '柒'],
			[8, '捌'],
			[9, '玖'],
		])
		this.integerMap = new Map([
			[0, '元'],
			[1, '拾'],
			[2, '佰'],
			[3, '仟'],
			[4, '万'],
			[5, '拾'],
			[6, '佰'],
			[7, '仟'],
			[8, '亿'],
		])
		this.decimalMap = new Map([
			[0, '分'],
			[1, '角'],
		])
		this.resultSet = new Set()
	}
	/**
	 * 转换
	 *
	 * @param {number} num 待转换的整数
	 * @returns
	 * @memberof RMB
	 */
	transform(num) {
		this.resultSet.clear()
		num = ('' + num).split('.')
		let integer = num[0],
			decimal = num[1] ? num[1].substr(0, 2) : []
		this.add(integer, this.integerMap).add(decimal, this.decimalMap)
		return [...this.resultSet].join('')
	}
	/**
	 * 转换
	 *
	 * @param {array} numType
	 * @param {map} mapType
	 * @returns
	 * @memberof RMB
	 */
	add(numType, mapType) {
		let len = numType.length
		for (let i = 0; i < len; i++) {
			this.resultSet.add(this.numMap.get(+numType[i]) + (+numType[i] ? mapType.get(len - i - 1) : ''))
		}
		return this
	}
}

const rmb = new RMB()
 console.log(rmb.transform(2114523.234))  // 贰佰壹拾壹万肆仟伍佰贰拾叁元贰角叁分
```

#### 四舍五入到指定的位数
```javascript
const round = (value, decimals = 2) => {
	if (isNaN(Number(value))) return value
	const multiple = Number('1' + '0'.repeat(decimals))
	return Math.round(Number(value) * multiple) / multiple
}
console.log(round('-3.559'))       // -3.56
console.log(round('-3.5593', 3))   // -3.559
console.log(round('-3.5593', 0))   // -4
```

#### 数值录入
对input中输入的值进行处理。
```javascript
/**
 * 数值录入
 * @param {String} source       输入的值
 * @param {Number}  decimals   保留几位小数，0表示整数，默认两位小数。
 * @param {Nubmer} min      最小值
 * @param {Nubmer} max      最大值
 * 如果最小值不是0，需要在blur的时候处理
 */
export const inputNumber = (
	source: string | number,
	decimals: number = 2,
	min: number = Number.NEGATIVE_INFINITY,
	max: number = Number.POSITIVE_INFINITY
): string => {
	if (source === 0) return '0'
	if (!source) return ''
	let value = source.toString()
	value = value.replace(/^(\-)*\D*(\d*(?:\.\d*)?).*$/g, '$1$2')
	//  只能输入正数
	if (min >= 0) {
		value = value.replace('-', '')
	} else if (max < 0) {
		// 只能输入负数
		value = '-' + value
	}
	// 小数处理
	const decimalIndex = value.indexOf('.')
	if (decimals !== undefined && decimalIndex > -1) {
		if (decimals === 0) {
			value = value.slice(0, decimalIndex + decimals)
		} else if (decimals > 0) {
			value = value.slice(0, decimalIndex + decimals + 1)
		}
	}
  // 最大/最小值处理
	const numberValue = Number(value)
	if (typeof numberValue === 'number') {
		if (numberValue < min) {
			value = min.toString()
		} else if (numberValue > max) {
			value = max.toString()
		}
	}
	return value
}
```
#### <br />
#### 获取小数位数
```javascript
 const getDecimal = (value: number | string): number => {
	const valueStr = _.toString(value)
	if (valueStr.includes('.')) {
		return valueStr.slice(valueStr.indexOf('.') + 1).length
	}
	return 0
}
```
#### 
#### 获取指定范围内的随机数
```javascript
 const random = (min=0, max=10000) => {
	const choice = max - min + 1
	return Math.floor(min + Math.random() * choice)
}
```

### 字符串
#### 统计每个字符出现的次数
```javascript
const countChar = str => {
	const result = {}
	for (let char of str) {
		result[char] = result[char] ? result[char] + 1 : 1
	}
	return result
}
console.log(countChar('aaaabbbccccddfgh'))
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628856842489-7fd84120-41b9-4f86-b0df-609ac7090869.png#averageHue=%23fdfdfd&clientId=uf1a6c9d1-d6b5-4&errorMessage=unknown%20error&from=paste&height=100&id=uce749cf1&originHeight=200&originWidth=352&originalType=binary&ratio=1&rotation=0&showTitle=false&size=10189&status=error&style=none&taskId=ua3da142c-e82e-44ea-a39e-752362c4873&title=&width=176)

#### 统计出现次数最多的字符
```javascript
const maxCountChar = (str) => {
  const result = {
    // 可能有出现次数相同的字符
    char: [],
    count: 0,
  }
  for (let char of str) {
    // 如果char已经存在于结果中，不必再找一遍
    if (result.char.includes(char)) {
      continue
    }
    const pattern = new RegExp(char, 'g')
    // 全局匹配str中满足条件的子串
    const matches = str.match(pattern)
    if (matches) {
      const count = matches.length;
      if (count > result.count) {
        result.char = [char]
        result.count = count
      } else if (count === result.count) {
        result.char.push(char)
      }
    }
  }
  return result
}

const str = maxCountChar("hello worldoaaaaa");
console.log(str)
```

#### 字符串反转
```javascript
const reverseString = str => [...str].reverse().join('')


const reverseString = str => str.split('').reverse().join('')

console.log(reverseString('foobar'))   //raboof
```

#### 字符串排序
```javascript
const sortCharactersInString = str =>
    str.split('').sort((a, b) => a.localeCompare(b)).join('')

console.log(sortCharactersInString('cabbage')) //aabbceg
```

#### 获取字符串长度
```javascript
const getStringLength = value => {
	var code = value.split('')
	var len = 0
	var f
	for (var i = 0; i < code.length; i++) {
		f = Number(code[i].charCodeAt(0))
		if (f > 127) {
			len += 2
		} else {
			len++
		}
	}
	return len
}
```


#### 截取字符串为相同长度的子串
```typescript
const str = '123456789'
console.log(str.match(/.{1,3}/g));  //['123','456','789']
```


### 数组
#### 获取平均值
```javascript
const arrayAverage = arr => {
  const sum = arr.reduce((prev, item) => prev + item, 0)
  return sum / arr.length
}

arrayAverage([1,2,3]) // 2
```

#### 获取最大值
```javascript
const arrayMax = arr => Math.max(...arr)
```
#### 
#### 获取最小值
```javascript
const arrayMin = arr => Math.min(...arr)
```
#### 
#### 判断数组相等
```javascript
const arrayEqual = (arr1, arr2) => {
  if (arr1 === arr2) {
    return true
  }
  if (arr1.length != arr2.length) {
    return false
  }
  for (let i = arr1.length; i--;) {
    if (arr1[i] !== arr2[i]) {
      return false
    }
  }
  return true
}
```

#### 划分为指定大小的二维数组
将数组划分为指定大小的二维数组。<br />使用Array.from()创建新的数组，使用Array.slice()将新数组的每个元素映射到size长度的区块。如果原始数组不能均匀拆分, 则最终的块将包含剩余的元素。
```javascript
const chunk = (arr, size) =>
	Array.from({ length: Math.ceil(arr.length / size) }, (value, index) =>
		arr.slice(index * size, (index + 1) * size),
	)
	
console.log(chunk([1, 2, 3, 4, 5], 2))  // [[1,2],[3,4],[5]]
```

#### 根据指定字段合并数组
```javascript
/**
* 根据指定字段，合并多个数组
* @param {String} filed  需要进行识别的字段
* @param {Array} rest    需要进行合并的数组
* @example
```
    const arr1 = [{ id: 1, name: 'aaa', list: [{ age: 10 }] }, { id: 2, name: 'bbb' }]
    const arr2 = [{ id: 1, name: 'wly', list: [{ age: 20 }], }, { id: 3, name: 'ccc' }]
    const arr3 = [{ id: 2, name: 'abc' }]
    console.log((concatBy('id', arr1, arr2, arr3)))
```
*/

const concatBy = (filed: string, ...rest: any[]) => {
    const combination = rest.reduce((acc, current) => acc.concat(current), [])
    const group = new Map()
    combination.forEach((item: any) => {
        if (group.has(item.id)) {
            const target = group.get(item.id)
            target.push(item)
            group.set(item.id, target)
        } else {
            group.set(item.id, [item])
        }
    })
  
    const result: any[] = []
    group.forEach(same => result.push(same.reduce((acc: any, current: any) => Object.assign({}, acc, current), {})))
    return result
}
```
 	如果只是希望将一个数组合并到另一个数组：
```javascript
const concatBy = (source: any[], data: any[], filed: string) =>
    data.reduce((acc, current) => {
        let target = acc.find((item: any) => item[filed] === current[filed])
        if (target) {
            Object.assign(target, current)
        } else {
            acc.push(current)
        }
        return acc
    }, source)

const arr1 = [{ id: 1, name: 'aaa', list: [{ age: 10 }] }, { id: 2, name: 'bbb' }]
const arr2 = [{ id: 1, name: 'wly', list: [{ age: 20 }], position: 'ceo' }, { id: 3, position: 'sales' }]

 console.log(concatBy(arr1,arr2,'id'))
```
	这种方式会改变源数据，source和最终的结果是一样的。实际使用中如果源数据还有用，可以先进行一次深克隆。

#### 移除falsey值
使用Array.filter()筛选出 falsey 值 (false、null、0、""、undefined和NaN).
```javascript

const compact = (arr) => arr.filter(Boolean)

compact([0, 1, false, 2, '', 3, 'a', 'e' * 23, NaN, 's', 34])   
// [ 1, 2, 3, 'a', 's', 34 ]
```


#### 获取某个值出现的次数
```javascript
const countOccurrences = (arr, value) =>
	arr.reduce(
		(accumulate, current) =>
			current === value ? accumulate + 1 : accumulate,
		0,
	)

console.log(countOccurrences([1, 1, 2, 1, 2, 3], 1)) //3
```

#### 获取间隔n的元素
```javascript
const everyNth = (arr, nth) => arr.filter((e, i) => i % nth === 0)

console.log(everyNth([1,2,3,4,5,6], 2))  // [ 1, 3, 5 ]
console.log(everyNth([1,2,3,4,5,6], 3))  // [ 1,4 ]
```

#### 获取数组中没有重复出现的元素
```javascript
const filterNonUnique = arr =>
    arr.filter(item => arr.indexOf(item) === arr.lastIndexOf(item))

filterNonUnique([1,2,2,3,4,4,5]) // [1,3,5]
```

#### 获取数组中重复出现的元素
```typescript
const arr = [
  {
    id: 1,
    name: 'aaa',
  },
  {
    id: 2,
    name: 'bbb',
  },
  {
    id: 3,
    name: 'ccc',
  },
  {
    id: 4,
    name: 'aaa',
  },
  {
    id: 5,
    name: 'ccc',
  },
  {
    id: 6,
    name: 'fff',
  },
  {
    id: 7,
    name: 'ccc',
  },
]

const result = {}
// 存放已经遍历过的元素
const oldArr = []

arr.forEach((newItem) => {
  if (result[newItem.name]) {
    result[newItem.name].push(newItem)
    return
  }
  const oldIndex = oldArr.findIndex((oldItem) => oldItem.name === newItem.name)
  if (oldIndex > -1) {
    if (result[newItem.name]) {
      result[newItem.name].push(oldArr[oldIndex], newItem)
    } else {
      result[newItem.name] = [oldArr[oldIndex], newItem]
    }
    oldArr.splice(oldIndex, 1)
  } else {
    oldArr.push(newItem)
  }
})

console.log(oldArr)
console.log(Object.values(result))
```


#### 对数组进行分组
  根据给定方法对数组元素进行分组。<br />	使用Array.map()将数组的值映射到函数或属性名。使用Array.reduce()创建一个对象, 其中的键是从映射的结果生成的。
```javascript
const groupBy = (arr, func) =>
	arr
		.map(typeof func === 'function' ? func : val => val[func])
		.reduce((acc, val, i) => {
			acc[val] = (acc[val] || []).concat(arr[i])
			return acc
		}, {})

groupBy([6.1, 4.2, 6.3], Math.floor)         //  {4: [4.2], 6: [6.1, 6.3]}
groupBy(['one', 'two', 'three'], 'length')   // {3: ['one', 'two'], 5: ['three']}
```

#### 按规则移除元素
从数组中移除给定函数返回true的元素。 
```javascript
const remove = (arr, func) =>
  Array.isArray(arr)
    ? arr.filter(func).reduce((acc, val) => {
      arr.splice(arr.indexOf(val), 1)
      return acc.concat(val)
    }, [])
: []

const arr = [1, 2, 3, 4]
const result = remove(arr, n => n % 2 == 0)
console.log(result)  // [2, 4]
console.log(arr)     // [1,3]
```
#### 
#### 根据指定字段去重
```javascript
  const uniqueElement = (arr, keys) => {
    const hash = {}
    return arr.reduce((result, item) => {
      const hashKey = keys.reduce((acc, current) => `${acc}-${item[current]}`, 'prefix')
      if (!hash[hashKey]) {
        hash[hashKey] = true
        result.push(item)
      }
      return result
    }, [])
  }


  const arr = [
    { id: 111, name: 'www', age: 25 },
    { id: 111, name: 'www', age: 25555 },
    { id: 222, name: 'Alex', age: 27 },
    { id: 222, name: 'Alex', age: 27 },
    { id: 222, name: 'Tom', age: 27 },
    { id: 3333, name: 'Chem', age: 28 }
  ]
//去除id和name都有一样的值
  console.log(uniqueElement(arr, ['id', 'name']))
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1661484497660-bc8fec07-e987-47d6-9655-74a26971aab4.png#averageHue=%23252a33&clientId=u470dcad4-1327-4&errorMessage=unknown%20error&from=paste&height=67&id=ue190d7e2&originHeight=67&originWidth=276&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5815&status=error&style=none&taskId=uea49a680-accc-4402-9000-22de1e06f7c&title=&width=276)


#### 按规则去重
lodash的uniqWith
```javascript
/**
 * 数组去重
 * @param {Arrray} arr
 * @param {Function} fn
 */
export const uniqueWith = (arr: any[], fn: (a: any, b: any) => boolean) =>
    arr.reduce((acc, a) => {
        if (!acc.some((b: any) => fn(a, b))) acc.push(a)
        return acc
    }, [])

uniqWith(arr, (a, b) => a.id === b.id)
```

#### 随机获取一个元素
```javascript
const sample = arr => arr[Math.floor(Math.random() * arr.length)]

console.log(sample([3, 7, 9, 11]))  // 9
console.log(sample("wly"))   // l
```
该方法也适用于字符串。

#### 打乱数组
```javascript
const shuffle = source => source.sort(() => Math.random() - 0.5)

var arr = [4, 1, 67, 12, 45, 121, 3]
console.log(shuffle(arr))
console.log(arr)
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628843313219-90997c1b-3c3b-4f92-90eb-2533fa788a60.png#averageHue=%23f8f8f8&clientId=u60ca8e58-6ce5-4&errorMessage=unknown%20error&from=paste&height=28&id=u12f741fc&originHeight=56&originWidth=326&originalType=binary&ratio=1&rotation=0&showTitle=false&size=11595&status=error&style=none&taskId=u5240c759-a421-4c89-8d03-ec17aacc614&title=&width=163)
```javascript

function random(min, max) {
  const choice = max - min + 1
  return Math.floor(min + Math.random() * choice)
}

function shuffle(source) {
  const newArr = []
  const len = source.length
  for (let i = len; i--;) {
    // 获取一个随机的索引
    const randomIndex = random(0, source.length - 1)
    // 从源数组中随机删除一个元素，并存到新数组中
    newArr.push(arr.splice(randomIndex, 1)[0])
  }
  return newArr
}

const arr = ['A', 'B', 'C', 'D', 'E']
shuffle(arr)
```

#### 旋转数组
```typescript
// 旋转数组。将数组中的元素向右移动n个位置：
const rotate = (arr, n) => {
  const temp = [...arr];
  return temp.splice(temp.length - n).concat(temp);
};

const arr = [1, 2, 3, 4, 5];
console.log(rotate(arr, 1));  // [5, 1, 2, 3, 4]
console.log(rotate(arr, 2));  // [4, 5, 1, 2, 3]
```


### 对象
#### 选取指定值
使用Array.reduce()将筛选/选取的密钥转换回具有相应键值对的对象 (如果在 obj 中存在该键)。
```javascript
const pick = (source, ...fields) =>
	fields.reduce(
		(acc, current) => (
			current in source && (acc[current] = source[current]), acc
		),
		{},
	)

const source = {
	name: 'wly',
	age: 25,
	gender: '1',
}

const fields = ['name', 'age', 'vvv']
const result = pick(source, ...fields)
console.log(result)     // {name: "wly", age: 25}
```

```javascript
const pick = (source, ...fields) => {
	const keys = Object.keys(source)
	const result = {}
	keys.forEach(item => {
		if (fields.includes(item)) {
			result[item] = source[item]
		}
	})
	return result
}
```

#### 对象深合并
```typescript
_.merge()
```



### 树
#### 修改树中的节点
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628849986649-88a5b1e9-c026-42af-bb7e-2745b7db746f.png#averageHue=%23fcfcfb&clientId=uf1a6c9d1-d6b5-4&errorMessage=unknown%20error&from=paste&height=243&id=uf8d14429&originHeight=486&originWidth=202&originalType=binary&ratio=1&rotation=0&showTitle=false&size=14805&status=error&style=none&taskId=u8c57da3d-286c-4dc3-bd29-18521a50a33&title=&width=101)
```javascript
const treeData = [
  {
    id: '1-1',
    name: '1-1',
    children: [
      {
        id: '1-1-1',
        name: '1-1-1',
        children: [
          { id: '1-1-1-1', name: '1-1-1-1' },
          { id: '1-1-1-2', name: '1-1-1-2' }
        ]
      },
      {
        id: '1-1-2',
        name: '1-1-2',
        children: [
          { id: '1-1-2-1', name: '1-1-2-1' },
          { id: '1-1-2-2', name: '1-1-2-2' }
        ]
      },
      {
        id: '1-1-3',
        name: '1-1-3'
      }
    ]
  },
  {
    id: '1-2',
    name: '1-2',
    children: [
      { id: '1-2-1', name: '1-2-1' },
      { id: '1-2-2', name: '1-2-2' }
    ]
  },
  {
    id: '1-3',
    name: '1-3'
  }
]
```

遍历这个树，增加或修改一些属性，直接修改原来的树：
```javascript
// 为每个节点增加父节点id,为每个节点增加一个editable属性,将属性id改为key
const formatNode = (node, id) => {
	node.parentId = id
	node.editable = false
	node.key = node.id
	delete node.id
}

const formatTree = (nodes, id = 0) => {
	nodes.forEach(node => {
		if (node.children) {
			formatNode(node, id)
			formatTree(node.children, node.id)
		} else {
			formatNode(node, id)
		}
	})
	return nodes
}
console.log(formatTree(deepClone(treeData)))
```

```javascript
// 只是简单的将每个节点的name改为title
const result = JSON.parse(JSON.stringify(treeData).replace(/name/g, 'title'))
console.log(result)

//  name改为title,id改为key
const result = JSON.parse(JSON.stringify(treeData).replace(/name/g, 'title').replace(/id/g, 'key'))
console.log(result)

```



修改树节点的属性，建立一个新的树：
```javascript
const formatTree = source => {
	const tree = []
	const createNode = (oldTree, newTree) => {
		oldTree.forEach(node => {
			const newNode = {
				key: node.id,
				title: node.name,
			}
			if (node.children) {
				const newChildren = []
				newNode.children = newChildren
				newTree.push(newNode)
				createNode(node.children, newChildren)
			} else {
				newTree.push(newNode)
			}
		})
	}
	createNode(source, tree)
	return tree
}

// 递归
const formatTree = source => {
	const newTree = []
	source.forEach(node => {
		const newNode = {
			key: node.id,
			title: node.name,
			children: formatTree(node.children),
		}
		newTree.push(newNode)
	})
	return newTree
}
```

#### 根据一维数组生成树
```javascript
const source = [{
  id: '1',
  name: '湖北',
  parentId: 0
}, {
  id: '2',
  name: '武汉',
  parentId: '1'
}, {
  id: '3',
  name: '襄阳',
  parentId: '1'
}, {
  id: '4',
  name: '南漳',
  parentId: '3'
}, {
  id: '5',
  name: '谷城',
  parentId: '3'
}, {
  id: '6',
  name: '宜昌',
  parentId: '1'
}, {
  id: '7',
  name: '湖南',
  parentId: null
}, {
  id: '8',
  name: '长沙',
  parentId: '7'
}, {
  id: '9',
  name: '株洲',
  parentId: '7'
}]

```

每次递归都要完全遍历一遍源数据。时间复杂度O( N^2):
```javascript
const createTree = (source, parentId) => {
	const tree = []
	source.forEach(item => {
		if (item.parentId === parentId) {
			item.children = createTree(source, item.id)
			tree.push(item)
		}
	})
	return tree
}

console.log(createTree(source, 0))
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628853638113-5062faaa-13f0-4956-90e6-7669bbbf0082.png#averageHue=%23faf9f9&clientId=uf1a6c9d1-d6b5-4&errorMessage=unknown%20error&from=paste&height=160&id=u234689bc&originHeight=320&originWidth=728&originalType=binary&ratio=1&rotation=0&showTitle=false&size=56002&status=error&style=none&taskId=udada36da-208e-4bd9-a087-5e992c6b823&title=&width=364)


**建立分类索引**<br />	第一次遍历将parentId相同的项归为一类，对象的key为parentId，值就是parentId相同的项组成的数组。第二次遍历为具有子节点的项添加children(某个项的id是group的key，那么这一项就有子节点)。最后返回顶级节点。时间复杂度为O(N*2)。
```javascript
const createTree = (source, topId) => {
	const group = {}
	// 先将具有相同parentId的项分为一类
	source.forEach(item => {
		const parentId = item.parentId
		if (!group[parentId]) {
			group[parentId] = []
		}
		group[parentId].push(item)
	})
  
	// 如果某一个item的id在group中，说明这一个item存在子树，子树就是group中根据id的分类。
	source.forEach(item => {
		const id = item.id
		if (group[id]) {
			item.children = group[id]
		}
	})
	return group[topId]
}

console.log(createTree(list, 0))
```

#### 扁平化树
```javascript
const flatTree = tree => {
  return tree
    .map(node => {
      const result = [];
      result.push(node);
      if (node.children) {
        result.push(...flatTree(node.children));
      }
      return result;
    })
    .flat();
};
```

#### 获取树的所有节点的某个属性值
```javascript
/**
* 获取树的所有节点的某个属性值
*/
export const getTreeNodeValue = (tree, filed) => {
  return tree
    .map((node) => {
    const result = []
    node[filed] && result.push(node[filed])
    if (node.children) {
      result.push(...getTreeNodeValue(node.children, filed))
    }
    return result
  })
    .flat()
}
console.log(getTreeNodeValue(treeData,'id'));
```
#### 查找某个节点的根节点
```javascript
const  getRootNode  =(nodes, id)=> {
  const result = []
  for (let i = 0; i < nodes.length; i++) {
    const node = nodes[i]
    if (node.id === id) {
      result.push(node.id)
      if (result.length === 1) {
        return result[0]
      }
      return true
    }
    if (node.children) {
      // 假设当前节点是想要的结果
      result.push(node.id)
      const find = getRootNode(node.children, id)
      if (find) {
        return result[0]
      }
      result.pop()
    }
  }
  return false
}
console.log(getRootNode(dataSource, 7))
```

####  查找某个节点的所有父节点
```javascript
const  getParentNodes = (nodes, id, result = [])  =>{
  for (let i = 0; i < nodes.length; i++) {
    const node = nodes[i]
    if (node.id === id) {
      result.push(node.id)
      //  如果在第一层就找到了，直接返回
      if (result.length === 1) {
        return result
      }
      return true
    }
    if (node.children) {
      // 假设当前节点是想要的结果
      result.push(node.id)
      const find = getParentNodes(node.children, id, result)
      if (find) {
        return result
      }
      result.pop()
    }
  }
  return false
}
console.log(getParentNodes(dataSource, 9))
```

#### 树的过滤
```typescript
const filterTree = (tree) => {
	return tree
		.filter((node) => node.flag === 1)
		.map((node) => {
			node = _.cloneDeep(node)
			if (node.children) {
				node.children = filterTree(node.children)
			}
			return node
		})
}
```

### dom
#### 判断一个元素是否聚焦
```javascript
const isElemFocus = elem => elem === document.activeElement
```

#### 获取两个元素之间的距离
```javascript
const getELemPosition = (elem, target) => {
	const eleRect = elem.getBoundingClientRect()
	const targetRect = target.getBoundingClientRect()

	return {
		vertical: eleRect.top - targetRect.top,
		horizon: eleRect.left - targetRect.left,
	}
}
```
#### <br />
#### 获取元素相对于浏览器窗口的位置
```javascript
const getOffset = elem => {
	function getLeft(o) {
		if (o == null) {
			return 0
		} else {
			return o.offsetLeft + getLeft(o.offsetParent) + (o.offsetParent ? o.offsetParent.clientLeft : 0)
		}
	}

	function getTop(o) {
		if (o == null) {
			return 0
		} else {
			return o.offsetTop + getTop(o.offsetParent) + (o.offsetParent ? o.offsetParent.clientTop : 0)
		}
	}
	return { left: getLeft(elem), top: getTop(elem) }
}
```


#### 计算文本宽度
```javascript
const measureWidth = function (text, font) {
    const canvas = document.createElement('canvas');
    const context = canvas.getContext('2d');
    // 必须指定字体
    context.font = font;
    const metrics = context.measureText(text);
    return metrics.width;
}

console.log(measureWidth('这是一段文本内容', '14px sans-serif'))
```
```javascript
const textRangeWrap = document.createElement('p')
textRangeWrap.style.position = 'absolute'
textRangeWrap.style.textIndent = '-9999px'

 const measureWidth  = (
	text: string | number,
	fontSize?: string,
	bold?: string | number,
	fontFamily?: string,
) => {
	if (!text) return 0
	if (!document.body.contains(textRangeWrap)) {
		document.body.appendChild(textRangeWrap)
	}
	if (fontSize) {
		textRangeWrap.style.fontSize = fontSize
	}
	if (bold) {
		textRangeWrap.style.fontWeight = 'bold'
	}
	if (fontFamily) {
		textRangeWrap.style.fontFamily = fontFamily
	}
	textRangeWrap.innerText = text.toString()
	const range = document.createRange()
	range.setStart(textRangeWrap, 0)
	range.setEnd(textRangeWrap, textRangeWrap.childNodes.length)
	return range.getBoundingClientRect().width
}

```
#### 滚动
```javascript
/**
 * 滚动,先慢后快，缓动的效果比easeIn动画明显
 * @param {HTMLDOM} element  要滚动的元素
 * @param {Number} target    目标位置
 * @param {Number} duration  滚动所用的总时间
 * @param {Function} callback  滚动完成之后的回调
 */
const scroll = (element, target, duration = 500, callback = () => {}) => {
	const startTime = Date.now()
	const move = () => {
		const passTime = Date.now() - startTime
		const currentPosition = element.scrollTop
		const residueDistance = target - currentPosition
		const step = residueDistance / 10
		element.scrollTop = currentPosition + step
		if (passTime < duration) {
			window.requestAnimationFrame(move)
		} else {
			element.scrollTop = target
			callback()
		}
	}
	move()
}
```

#### 动画
```javascript
/**
 * 动画，可改变多个属性
 * @param {HTMLDOM} element  要发生动画的元素
 * @param {Object} properties    要改变的元素属性
 * @param {Number} interval  每次运动的时间间隔
 * @param {Function} callback  动画完成之后的回调
 */
const animate = (element, properties, interval = 20, callback = () => {}) => {
	clearInterval(element.timer)
	element.timer = setInterval(() => {
		let flag = true
		for (const property in properties) {
			const current = parseInt(window.getComputedStyle(element)[property])
			const target = properties[property]
			let step = (target - current) / 10
			step = step > 0 ? Math.ceil(step) : Math.floor(step)
			element.style[property] = current + step + 'px'
			if (current != target) {
				flag = false
			
		}
		if (flag) {
			clearInterval(element.timer)
			callback()
		}
	}, interval)
}
```



### 日期
#### 时间格式
```javascript
function formatDate(date, format) {
    if (date) {
        date = new Date(date);
    } else {
        return "";
    }
    var o = {
        'M+': date.getMonth() + 1, //month
        'd+': date.getDate(), //day
        'h+': date.getHours(), //hour
        'm+': date.getMinutes(), //minute
        's+': date.getSeconds(), //second
        'q+': Math.floor((date.getMonth() + 3) / 3), //quarter
        'S': date.getMilliseconds() //millisecond
    }
    if (/(y+)/.test(format)) {
        format = format.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length));
    }
    for (var k in o) {
        if (new RegExp('(' + k + ')').test(format)) {
            format = format.replace(RegExp.$1, RegExp.$1.length == 1 ? o[k] : ('00' + o[k]).substr(('' + o[k])
                .length));
        }
    }
    //增加星期功能
    // var weekArr = ['星期天', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];
    // var week = weekArr[date.getDay()]; 
    // format = format + ' ' + week; 
    return format;
}

var date = new Date(); // 只能传入标准时间对象，时间戳也不行，getMonth()这些方法不执行。
console.log(formatDate(date, 'hh:mm')); //12:57
console.log(formatDate(date, 'hh:mm:ss')); //12:57:29
console.log(formatDate(date, 'yyyy-MM-dd')); //2017-12-10
console.log(formatDate(date, 'yyyy-MM-dd hh:mm:ss')); //2017-12-10 12:57:29
console.log(formatDate(date, 'yyyy年MM月dd日 hh:mm:ss')); //2017年12月10日 13:27:21
```

#### 模糊时间
用于简化经历了多长时间：
```typescript
const timeFormat = (value: number) => {
  const timeMap = {
    year: 1000 * 60 * 60 * 24 * 365,
    month: 1000 * 60 * 60 * 24 * 30,
    day: 1000 * 60 * 60 * 24,
    hour: 1000 * 60 * 60,
    min: 1000 * 60,
    s: 1000,
    ms: 1,
  }

  for (const time in timeMap) {
    if (Number(value) >= timeMap[time]) {
      return (Number(value) / timeMap[time]).toFixed(2) + ` ${time}`
    }
  }
  return '0 ms'
}

console.log(timeFormat(100000))  //1.67 min
```

```typescript
const parseTime = (time) => {
	if (!time) return
	const SECOND = 1000
	const MINUTE = 60 * SECOND
	const HOUR = 60 * MINUTE
	const DAY = 24 * HOUR

	const days = Math.floor(time / DAY)
	const hours = Math.floor((time % DAY) / HOUR)
	const minutes = Math.floor((time % HOUR) / MINUTE)
	const seconds = Math.floor((time % MINUTE) / SECOND)
	const milliseconds = Math.floor(time % SECOND)

	if (days) {
		return `${days}d${hours}h${minutes}min`
	}
	if (hours) {
		return `${hours}h${minutes}min`
	}
	if (minutes) {
		return `${minutes}min${seconds}s`
	}
	if (seconds) {
		return `${seconds}s{milliseconds}ms`
	}
	return `${milliseconds}ms`
}


console.log(parseTime(10000000)) // 2h46min
```

```typescript
const fromNowOn = (value: string) => {
	if (!value) return ''
	let now = +new Date()
	let to = +new Date(value)
	let d = (now - to) / 1000
	if (d < 0) return '时间未到'
	if (d < 60) return `${Math.floor(d)} 秒前`
	if (d < 60 * 60) return `${Math.floor(d / 60)} 分钟前`
	if (d < 60 * 60 * 24) return `${Math.floor(d / (60 * 60))} 小时前`
	if (d < 60 * 60 * 24 * 30) return `${Math.floor(d / (60 * 60 * 24))} 天前`
	if (d < 60 * 60 * 24 * 30 * 12) return `${Math.floor(d / (60 * 60 * 24 * 30))}  个月前`
	return `${Math.floor(d / (60 * 60 * 24 * 30 * 12))} 年前`
}
```

### type
#### 生成字面量类型
```javascript
type Status  = 0 | 1 | 2 | 3
```


```javascript
 const tupleStr = <T extends string[]>(...args: T) => args

 const tupleNum = <T extends number[]>(...args: T) => args

const directions = tupleStr('North', 'South', 'East', 'West')

type Direction = typeof status[number]

interface IFile {
    direction: Direction
}
```


```javascript
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



const directions = enumStr(['North', 'South', 'East', 'West'])
console.log(directions) // {North: "North", South: "South", East: "East", West: "West"}

// 创建一个类型
type Direction = keyof typeof directions

// 简单的使用
let sample: Direction

sample = directions.North // ok
sample = 'North' // ok
```

需要直接将参数数组传入，不能从外边先定义好数组，然后传入：
```javascript

// 无效, 因为arr会被推断为string[]
const arr = ['North', 'South', 'East', 'West']
const directions = enumStr(arr)

```

### 文件
#### 将base64数据转换为Bolb
```typescript
const base64toBlob = (dataURL: string, filename: string = 'file') => {
  const arr = dataURL.split(',')
  const mime = arr[0]!.match(/:(.*?);/)![1]
  const suffix = mime.split('/')[1]
  const bstr = window.atob(arr[1])
  let n = bstr.length
  const u8arr = new Uint8Array(n)
  while (n--) {
    u8arr[n] = bstr.charCodeAt(n)
  }
  return new File([u8arr], `${filename}.${suffix}`, {
    type: mime,
  })
}
```

#### 将Blob数据转换为base64
```typescript
const blobToBase64 = (blob: Blob) => {
  return new Promise<string>((resolve, reject) => {
    const reader = new FileReader()
    reader.onload = (e) => {
      resolve(e.target?.result as string)
    }
    reader.onerror = reject
    reader.readAsDataURL(blob)
  })
}
```

#### 下载文件,数据源是Blob对象
```typescript
const downloadBlobFile = (binaryString: string, fileName: string) => {
  const blob = new Blob([binaryString], { type: 'application/octet-stream' })
  const link = document.createElement('a')
  link.href = window.URL.createObjectURL(blob)
  link.download = fileName
  link.click()
  //延时释放
  setTimeout(function () {
    window.URL.revokeObjectURL(link.href)
  }, 100)
}
```
#### 下载文件。数据源是base64
```typescript
const headerMap = {
  txt: 'data:text/plain',
  png: 'data:image/png',
  jpg: 'data:image/jpeg',
  xlsx: 'data:application/vnd.ms-excel',
}

const downloadBase64File = (
  data: string,
  fileName: string,
  headerType?: 'txt' | 'png' | 'jpg' | 'xlsx' | 'lic'
) => {
  const link = document.createElement('a')
  const header = headerMap[headerType || ''] || ''
  link.href = header + ';base64,' + data
  link.download = fileName
  link.click()
}
```

#### 通过文件地址下载文件
```typescript
const downloadURLFile = (url: string, fileName: string) => {
  const xhr = new XMLHttpRequest()
  xhr.open('GET', url.replace(/\\/g, '/'), true)
  xhr.responseType = 'blob'
  xhr.onload = () => {
    if (xhr.status === 200) {
      downloadBlobFile(xhr.response, fileName)
    }
  }
  xhr.send()
}
```

#### 计算文件大小
```typescript
const getFileSize = (value: string | number) => {
	const sizeMap = {
		PiB: 1024 * 1024 * 1024 * 1024 * 1024,
		TiB: 1024 * 1024 * 1024 * 1024,
		GiB: 1024 * 1024 * 1024,
		MiB: 1024 * 1024,
		KiB: 1024,
		Byte: 1,
	}
	for (let i in sizeMap) {
		if (Number(value) >= sizeMap[i]) {
			return (Number(value) / sizeMap[i]).toFixed(2) + ` ${i}`
		}
	}
	return '0 Byte'
}
```

#### 获取不带后缀的文件名
```typescript
export const getFileName = (fileName: string) => {
	return fileName.replace(/(.*\/)*([^.]+).*/gi, '$2')
}


// 需要注意有文件名中有点的情况
const file = 'abcdefg.ab.xlsx'
console.log(getFileName(file)); // abcdefg

```

#### 获取文件后缀
```typescript
const getFileSuffix = (fileName: string) => {
	return fileName.replace(/.+\./, '')
  //fileName.substring(file_path.lastIndexOf('.') + 1)
}
// 需要注意有文件名中有点的情况


const getFileSuffix = (fileName) => {
  const m = fileName.slice(0, fileName.lastIndexOf('.'))
  const n = fileName.slice(m.lastIndexOf('.') + 1)
  return n
}
const file = 'abcdfg.trg.xlsx'
console.log(getFileSuffix(file)) //trg.xlsx
```






### 其他
#### 防抖
触发事件后在指定时间内函数只执行一次，如果在指定时间内又触发了事件，则会重新计算时间。<br />效果：在指定时间内连续触发事件，函数不会执行，当到达指定时间后，函数执行一次。<br />比如在input中连续输入的时候，input检测到有输入，就会去执行相应的时间处理程序。加入防抖之后，如果连续输入的时间间隔小于interval，则不执行，当用户停止输入，经过interval之后，去执行一次。
```javascript
	function debounce(fn, interval = 200) {
		let timer;
		return function () {
			const context = this
			const args = arguments
			// 每次触发的时候取消定时器，都并重新创建一个定时器，也就是重新计算时间
			if (timer) {
				clearTimeout(timer)
			}
			timer = setTimeout(() => {
				fn.apply(this, arguments)
			}, interval)
		}
	}
```

连续拖动的时候，不会执行，当拖动停止，并经过3s后执行一次：
```javascript
 window.onresize = debounce(resize,3000)
```

获取content和arg是为了让防抖函数最终返回的函数内部的this指向不变（this指向触发事件的DOM元素），以及依旧能接受到event对象：
```javascript
function input(e) {
  console.log(e);
  console.log(this)
}
document.getElementById('input').addEventListener('change', debounce(input, 3000))
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628859357305-d65a655b-6577-44bd-88cc-1d81fb4e8a16.png#averageHue=%23f5f4f4&clientId=uf1a6c9d1-d6b5-4&errorMessage=unknown%20error&from=paste&height=43&id=u0cad6acc&originHeight=86&originWidth=544&originalType=binary&ratio=1&rotation=0&showTitle=false&size=22875&status=error&style=none&taskId=uc2a71360-b274-4f62-8a8d-012f0677af1&title=&width=272)

react中的检简易防抖
```javascript
const search = useCallback(() => {
    searchSaleOrder({ page: 1, keyword })
        .then(res => {})
}, [keyword])

useEffect(() => {
    const timer = setTimeout(search, 300)
    return () => {
        clearTimeout(timer)
    }
}, [search])
```


#### 节流
连续触发事件但是在指定时间内只执行一次。如果在指定时间内又触发了事件，则忽略执行。节流会稀释函数的执行频率。

立即执行版：
```javascript
function throttle(fn, interval = 200) {
	let prevTime = 0
	return function () {
		const context = this
		const args = arguments
		const nowTime = Date.now()
		// 如果两次触发事件的时间间隔大于规定时间间隔，才执行函数
		if (nowTime - prevTime > interval) {
			prevTime = nowTime
			fn.apply(context, args)
		}
	}
}
```
一触发操作就立即执行一次，此后每当到了指定时间再执行。跟lodash中的表现一致。

非立即执行版：
```javascript
function throttle(fn, interval = 200) {
	let timer
	return function () {
		const context = this
		const args = arguments
		// 定时器不在的时候才执行。如果定时器还在，说明前一次延迟执行还没有完成(还没到指定的interval)
		if (!timer) {
			timer = setTimeout(() => {
				timer = null
				fn.apply(context, args)
			}, interval)
		}
	}
}
```
触发操作后到了指定时间才执行第一次，此后每当到了指定时间再执行。


#### 异步函数锁
```typescript
/**
 * 异步函数加锁，用于连续发出请求时的时序控制。
 * 大概原理就是当上一个请求还未完成的时候，下次请求就不会发出。
 * 问题：当翻到第11页的时候停止，但是此时第10页的请求还未完成，那么第11页的请求不会发出。最终呈现的是第10页的数据。
 * */

const lockFn = (fn: Function) => {
	let locked = false
	return async function (...args) {
		if (locked) return
		locked = true
		const result = await fn.apply(null, args)
		locked = false
		return result
	}
}

const getTable = lockFn(async () => {
	const data = await apis.cluster.getUserList()
})
```


#### 深克隆
```javascript
function deepClone(source) {
	if (typeof source !== 'object' || source === null) {
		return source
	}
	const target = Array.isArray(source) ? [] : {}
	for (const [key, value] of Object.entries(source)) {
		target[key] = deepClone(value)
	}
	return target
}
```
for of 和for in不可获取到Symbol类型的key。

#### 序列化请求参数
```javascript
const dataSerializer = (params = {}) => {
	const paramArr = []
	for (const [key, value] of Object.entries(params)) {
		if (Array.isArray(value)) {
			value.forEach(item => paramArr.push(`${encodeURIComponent(key)}=${encodeURIComponent(item)}`))
		} else {
			paramArr.push(`${encodeURIComponent(key)}=${encodeURIComponent(value)}`)
		}
	}
	return paramArr.join('&')
}
```

#### 加载第三方脚本
```javascript
const loadScript = (src, callback = (err, res) => {}) => {
	const existScript = document.getElementById(src)
	if (existScript) {
		callback(null, existScript)
	} else {
		const script = document.createElement('script')
		script.src = src
		script.id = src
		document.body.appendChild(script)
		script.onload = function () {
			callback(null, script)
		}
		script.onerror = function () {
			callback(new Error(`“${src}”加载失败`), script)
		}
	}
}
```


#### 延时执行
```javascript
/**
 * 延迟执行async函数的一部分, 将其放入休眠状态, 返回Promise。
 * @param {Number} interval  阻塞的时间 ms
 * @example
```
async function test() {
  console.log('Hello')
  await sleep(1000)
  console.log('world!')
}
```
 */
const sleep = (interval)=> new Promise(resolve => setTimeout(resolve, interval))

```

#### 判断数据类型
```javascript
const getType = value => (value ? value.constructor.name.toLowerCase() : value)
// Object.prototype.toString.call(value).match(/\[object (\w+)\]/)[1].toLowerCase()

console.log(getType('www'))     //"string"
console.log(getType(1))          //"number"
console.log(getType(function () {})) //"function"
console.log(getType([]))       //"array"
console.log(getType({}))      //"object"
console.log(getType(new Set([1, 2, 2]))) //"set"
```

#### 获取url参数
```javascript
const getURLParameters = (url: string) => {
  const matchArray = url.match(/([^?=&]+)(=([^&]*))/g) || []
  return matchArray.reduce(
    (a, v) => (
      (a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a
    ),
    {}
  )
}

console.log(getURLParameters('http://url.com/page?name=Adam&surname=Smith'));
// { name: 'Adam', surname: 'Smith' }
```
```javascript
function getURLParameters(url) {
  const search = url.split('?')[1];
  if (!search) {
    return {}
  }
  const parameters =
    '{"'
    +
    decodeURIComponent(search)
      .replace(/"/g, '\\"')
      .replace(/&/g, '","')
      .replace(/=/g, '":"')
    +
    '"}';
  return JSON.parse(parameters)
}
```
```javascript
function getURLParameters(url) {
	const search = url.split('?')[1]
	const args = {} // 保存数据的对象
	let item = []
	let key = ''
	let value = ''
	// 将查询字符串转换为数组
	const strs = search.split('&')
	for (let i = 0, len = strs.length; i < len; i++) {
		item = strs[i].split('=')
		key = decodeURIComponent(item[0])
		value = decodeURIComponent(item[1])
		args[key] = value
	}
	return args
}

```
#### 生成guid
全局唯一标识符（GUID，Globally Unique Identifier）也称作 UUID(Universally Unique IDentifier) 。GUID是一种由算法生成的二进制长度为128位的数字标识符。GUID 的格式为"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"，其中的 x 是 0-9 或 a-f 范围内的一个32位十六进制数。在理想情况下，任何计算机和计算机集群都不会生成两个相同的GUID。
```javascript
const guid = () => {
    const S4 = () => (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
    return (S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4() + "-" + S4() + S4() + S4());
};
console.log(guid())
console.log(guid())
console.log(guid())
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628860107855-26de8387-080f-4aea-8e1a-6a12db43087a.png#averageHue=%23e9e9e9&clientId=uf1a6c9d1-d6b5-4&errorMessage=unknown%20error&from=paste&height=36&id=ufe3943b8&originHeight=72&originWidth=320&originalType=binary&ratio=1&rotation=0&showTitle=false&size=12012&status=error&style=none&taskId=uc3bc73bc-ca39-45e4-a7a0-2133e4bb7b4&title=&width=160)‘

使用cryptoAPI 生成 UUID, 符合RFC4122版本。
```javascript
const guid = () =>
    ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, c =>
        (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16)
    );
console.log(guid())
```
#### 
#### 将px转换为rem
```javascript
/**
 * 将px转换为rem
 * @param {number} size 大小
 */
export function pxtorem(size) {
    const rootValue = document.documentElement.style.fontSize
    return size / parseFloat(rootValue) + 'rem'
}
```

#### 十六进制颜色值转rgb
使用按位右运算符和掩码位与&(and) 运算符将十六进制颜色代码 (前缀为#) 转换为具有 RGB 值的字符串。如果它是一个3位数的 colorcode, 那么用 extendHex () 函数 (ref.extendHex代码段) 扩展的6位 colorcode 进行相同的处理。
```javascript
const hexToRgb = (hex: string, opacity: number = 1) => {
	const extendHex = (shortHex: string) =>
		'#' +
		shortHex
			.slice(shortHex.startsWith('#') ? 1 : 0)
			.split('')
			.map(x => x + x)
			.join('')
	const extendedHex = hex.slice(hex.startsWith('#') ? 1 : 0).length === 3 ? extendHex(hex) : hex
	return `rgba(${parseInt(extendedHex.slice(1), 16) >> 16}, ${
		(parseInt(extendedHex.slice(1), 16) & 0x00ff00) >> 8
	}, ${parseInt(extendedHex.slice(1), 16) & 0x0000ff}, ${opacity})`
}

hexToRgb('#27ae60') //'rgb(39, 174, 96)'
hexToRgb('#acd', 0.5) // 'rgb(170, 204, 221, 0.5)'

extendHex('#03f') //'#0033ff'
extendHex('05a') // '#0055aa'
```
```javascript
const hexToRGB = (hex: string, alpha: number = 1) => {
	let h: any = hex.slice(hex.startsWith('#') ? 1 : 0)
	if (h.length === 3) h = [...h].map(x => x + x).join('')
	h = parseInt(h, 16)
	return (
		'rgba' + '(' + (h >>> 16) + ', ' + ((h & 0x00ff00) >>> 8) + ', ' + ((h & 0x0000ff) >>> 0) + ', ' + alpha + ')'
	)
}
```

```javascript
/**
 * 设置十六进制颜色的透明度。就是和白色mix
 */
export const alphaColor = (color, alpha) => {
  const colorValue = color.slice(1);
  let red = parseInt(colorValue.slice(0, 2), 16);
  let green = parseInt(colorValue.slice(2, 4), 16);
  let blue = parseInt(colorValue.slice(4, 6), 16);
  red = Math.round(red * (1 - tint) + 255 * tint);
  green = Math.round(green * (1 - tint) + 255 * tint);
  blue = Math.round(blue * (1 - tint) + 255 * tint);
  red = red.toString(16);
  green = green.toString(16);
  blue = blue.toString(16);
  return `#${red}${green}${blue}`;
};
console.log(alphaColor("#aaaaaa", 0.3)); // #c4c4c4
```

#### 随机生成十六进制颜色
```javascript
 const randomHexColor = () => {
    return '#' + ('00000' + (Math.random() * 0x1000000 << 0).toString(16)).slice(-6);
}

 console.log(randomHexColor())  // #d7a789
```

#### 随机生成RGBA颜色
```javascript
function randomRgbaColor() { //随机生成RGBA颜色
    var r = Math.floor(Math.random() * 256); //随机生成256以内r值
    var g = Math.floor(Math.random() * 256); //随机生成256以内g值
    var b = Math.floor(Math.random() * 256); //随机生成256以内b值
    var alpha = Math.random(); //随机生成1以内a值
    return `rgba(${r},${g},${b},${alpha})`; //返回rgba(r,g,b,a)格式颜色
}
  
```

#### 设置十六进制颜色的透明度
```javascript

export const alphaColor = (color, tint) => {
  const colorValue = color.slice(1);
  let red = parseInt(colorValue.slice(0, 2), 16);
  let green = parseInt(colorValue.slice(2, 4), 16);
  let blue = parseInt(colorValue.slice(4, 6), 16);
  red = Math.round(red * (1 - tint) + 255 * tint);
  green = Math.round(green * (1 - tint) + 255 * tint);
  blue = Math.round(blue * (1 - tint) + 255 * tint);
  red = red.toString(16);
  green = green.toString(16);
  blue = blue.toString(16);
  return `#${red}${green}${blue}`;
};
```

#### 图片转base64
```javascript
/**
 * 图片转base64
 * @param url
 * @param callback
 * @param errorCallback
 */
export function getUrlBase64(
	url: string,
	callback: (url: string) => void,
	width: number = 60,
	height: number = 60,
	errorCallback?: (err: any) => void,
) {
	let canvas: any = document.createElement('canvas') // 创建canvas DOM元素
	const ctx: any = canvas.getContext('2d')
	const img = new Image()
	img.crossOrigin = 'Anonymous'
	img.src = url
	img.onload = function () {
		canvas.height = height // 指定画板的高度,自定义
		canvas.width = width // 指定画板的宽度，自定义
		ctx.drawImage(img, 0, 0, width, height) // 参数可自定义
		const dataURL = canvas.toDataURL('image/')
		callback && callback.call(this, dataURL) // 回掉函数获取Base64编码
		canvas = null
	}
	img.onerror = function (err: any) {
		errorCallback && errorCallback(err)
	}
}
```
####  校验身份证
```javascript
const WEIGHT = [7,9,10,5,8,4,2,1,6,3,7,9,10,5,8,4,2]
const MO = [1,0,'X',9,8,7,6,5,4,3,2]

const isRightId = (id) => {
  const arr = id.split('')
  const checkNumber = arr.pop() // 去除校验码，将 pop 的返回值赋值给 checkNumber
  let sum = 0
  arr.forEach((ele, index) => {
   sum += ele * WEIGHT[index]
  })
  const m = sum % 11
  const result = MO[m]
  return result+'' === checkNumber
}

console.log(isRightId('11010519491231002X')) // true
console.log(isRightId('110105194912310029')) // false
```
