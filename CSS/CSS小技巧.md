# img底部3px
该问题为极限对齐原因造成的。img的diaplay属性默认是inline-block。 带有行内特性的元素一般都是文字, 而英文的小写字母y和g底部比别人多出来一段, 因此不管inline元素中有什么, 都会默认为y和g留出底部一段距离。有以下几种方式来取出底部多数来的3px空白。

**方式一**
```css
img {
  display: block;
  border: none;
}
```
    因为只有inline元素才会有极限对齐，所以将img设置成块级元素就可以了。

**方式二**
```css
img {
  vertical-align: top
}
```
   以top方式对齐，而不是默认地baseline。

**方式三**
```css
.parent {
  font-size: 0;
  /* 或者 */
  /* line-height:0; */
}
```
    parent为img的父元素。

# 正方形
**方式一**<br />宽高都以vw为单位。
```css
.box {
    width: 30vw;
    height: 30vw;
    background-color: skyblue;
}
```

**方式二**<br />      使用grid布局。<br />      当padding以百分比为单位时，以父元素的宽度为参考。子元素的宽默认为父元素的100%，而高使用padding-top或paddiing-bottom撑起来。<br />实际应用中，都不会是简单的画个正方形，还需要放内容，内容区采用定位的方式铺满即可。
```html
<div class="wrap">
    <div class="box">
        <div class="content">1</div>
    </div>
    <div class="box">
        <div class="content">2</div>
    </div>
    <div class="box">
        <div class="content">3</div>
    </div>
</div>
```
```css
.wrap {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
}
.box {
    position: relative;
    padding-bottom: 100%;
}
.content {
    position: absolute;
    width: 100%;
    height: 100%;
}
```
**方式三**<br />使用flex布局。<br />还是采用“ 当padding以百分比为单位时，以父元素的宽度为参考”的规则。<br />浮动产生BFC，并撑起高度，可以直接再放内容进去。最为推荐。
```html
<div class="wrap">
    <div class="box">内容</div>
    <div class="box">内容</div>
    <div class="box">内容</div>
</div>

```
```css
.wrap {
    display: flex;
    width: 200px;
}
.box {
    flex: 1;
    background-color: antiquewhite;
    position: relative;
}
.box + .box {
    margin-left: 10px;
}
.box::after {
    content: '';
    /* 浮动撑起box，且不会遮住里边的内容 */
    float: left;
    /* 伪元素padding相对于父元素box的宽度 */
    padding-top: 100%;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628173202687-f08b882b-2695-4583-9483-c2862be153ad.png#averageHue=%23f0e4d6&clientId=u1403c844-a0bb-4&from=paste&height=49&id=uc922f987&originHeight=98&originWidth=304&originalType=binary&ratio=1&rotation=0&showTitle=false&size=7419&status=done&style=none&taskId=u2439bed8-6a93-40eb-8a63-2fc9ba12065&title=&width=152)
# 三角形
**1.基础实现**
```css
.triangle {
    width: 0;
    height: 0;
    /* 边框颜色与背景色相同 */
    border: 30px solid white; 
    /* 顶部边框颜色突出显示 */
    border-top-color: blue;
    border-bottom: none;
}
```
```css
.triangle {
    width: 0;
    height: 0;
    border: 30px solid blue;
    border-bottom-color: transparent;
    border-left-color: transparent;
    border-right-color: transparent;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171242514-28d4e95a-968d-4054-8f76-a909138e83fc.png#averageHue=%23dee0df&clientId=uf1a197a2-8c87-4&from=paste&height=38&id=uc82a9c55&originHeight=76&originWidth=106&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2002&status=done&style=none&taskId=u79bde521-6425-4339-a9be-2f121c17180&title=&width=53)

**2.任意方向、任意角度**<br />**(1)**
```css
.triangle {
  width: 0;
  height: 0;
  border-left: 30px solid transparent;
  border-right: 30px solid transparent;
  border-bottom: 30px solid red;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171300594-dd942b03-ac68-43eb-819c-c81660aff35b.png#averageHue=%23faaeaa&clientId=uf1a197a2-8c87-4&from=paste&height=17&id=u271a281e&originHeight=34&originWidth=70&originalType=binary&ratio=1&rotation=0&showTitle=false&size=780&status=done&style=none&taskId=u586d6b83-9801-430c-bbc8-be9abad40a6&title=&width=35)<br />**(2)**
```css
.triangle {
  width: 0;
  height: 0;
  border-left: 30px solid transparent;
  border-right: 30px solid transparent;
  border-top: 30px solid red;

}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171336345-36572924-4e14-4244-ada2-756b4b020afb.png#averageHue=%23fab6b3&clientId=uf1a197a2-8c87-4&from=paste&height=20&id=u01c2ad74&originHeight=40&originWidth=66&originalType=binary&ratio=1&rotation=0&showTitle=false&size=890&status=done&style=none&taskId=ud5ba48ad-93ed-436a-bdd9-b2e52f6ef2c&title=&width=33)<br />**(3)**
```css
.triangle {
  width: 0;
  height: 0;
  border-top: 30px solid transparent;
	border-right: 30px solid red;
	border-bottom: 30px solid transparent;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171424221-2d167c7e-1374-4f78-acad-a949ba428b48.png#averageHue=%23faa7a3&clientId=uf1a197a2-8c87-4&from=paste&height=31&id=uf06cae94&originHeight=62&originWidth=36&originalType=binary&ratio=1&rotation=0&showTitle=false&size=692&status=done&style=none&taskId=ud5c83297-4952-4d67-8c77-023aed4a024&title=&width=18)<br />**(4)**
```css
.triangle {
  width: 0;
  height: 0;
  border-top: 30px solid transparent;
	border-left: 30px solid red;
	border-bottom: 30px solid transparent;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171482136-ec97cbc1-2a9d-4ec7-a98c-fc7ca9cc5b22.png#averageHue=%23f9a3a0&clientId=uf1a197a2-8c87-4&from=paste&height=33&id=u6f2000a6&originHeight=66&originWidth=32&originalType=binary&ratio=1&rotation=0&showTitle=false&size=917&status=done&style=none&taskId=u3a66bc37-6116-42ae-882d-3fe8e5d4c89&title=&width=16)<br />**(5)**
```css
.triangle {
  width: 0;
  height: 0;
  border-top: 30px solid red;
	border-right: 30px solid transparent;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171512890-12fabf96-4761-438b-b48e-627ce35f198a.png#averageHue=%23faaba8&clientId=uf1a197a2-8c87-4&from=paste&height=18&id=u16737ecc&originHeight=36&originWidth=32&originalType=binary&ratio=1&rotation=0&showTitle=false&size=513&status=done&style=none&taskId=ub546ae69-19f0-473a-8862-6c03b0e6a14&title=&width=16)<br />**(6)**
```css
.triangle {
  width: 0;
  height: 0;
  border-top: 30px solid red;
	border-left: 30px solid transparent;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171533791-1a792073-308c-44c1-87ed-8ac4613a8a60.png#averageHue=%23fab0ad&clientId=uf1a197a2-8c87-4&from=paste&height=18&id=u35cca1c9&originHeight=36&originWidth=34&originalType=binary&ratio=1&rotation=0&showTitle=false&size=516&status=done&style=none&taskId=u85156905-35d5-409b-b4bf-7cbfbbf54f8&title=&width=17)<br />**(7)**
```css
.triangle {
  width: 0;
  height: 0;
  border-bottom: 30px solid red;
  border-right: 30px solid transparent;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171578753-95f721a7-ff30-4584-b126-73e14c13db74.png#averageHue=%23fab3b0&clientId=uf1a197a2-8c87-4&from=paste&height=17&id=ube8cb90d&originHeight=34&originWidth=38&originalType=binary&ratio=1&rotation=0&showTitle=false&size=222&status=done&style=none&taskId=u9202c6ed-d7be-4060-880a-8b7818f04f2&title=&width=19)<br />**(8)**
```css
.triangle {
  width: 0;
  height: 0;
  border-bottom: 30px solid red;
  border-left: 30px solid transparent;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628171601074-187cb895-580a-46f8-8911-4d36fb387c3b.png#averageHue=%23faaba8&clientId=uf1a197a2-8c87-4&from=paste&height=16&id=uedfc8127&originHeight=32&originWidth=36&originalType=binary&ratio=1&rotation=0&showTitle=false&size=492&status=done&style=none&taskId=udb9e72f8-4175-4fab-800f-7125054ba6b&title=&width=18)

# 水平垂直居中
## 定位
**方式一**<br />左和上定位到50%，然后通过负的外边距向左和上方移动宽度的一半。<br />margin的百分值是相对于父元素的，所有这里不能使用-50%，所以只适用于宽高固定的盒子。
```css
.box {
    position: absolute;
    left: 50%;
		top: 50%;
		width: 400px;
    height: 200px;
    margin: -100px 0 0 -200px;
    background-color: #ccc;
}
```

**方式二**<br />左和上定位的通过calc计算50%减去盒子的宽和高的一半得到。<br />也是只适应于宽高固定的盒子。
```css
.box {
    position: absolute;
    left: calc(50% - 200px);
    top: calc(50% - 100px);
    width: 400px;
    height: 200px;
    background-color: #ccc;
}
```

**方式三**<br />左和上定位到50%，然后通过transform向左和上方宽度的一半。	<br />translate(left,top),位移的百分值是相对于元素自身的宽高，适用于宽高不固定的盒子。
```css
.box{
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    /*transform: translate(-200px, -100px);*/
    width: 400px;
    height: 200px;
    background-color: #ccc;
}
```


**方式四**<br />盒子上下左右定位都为0，上下左右的margin都为auto。
```css
.box{
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
		top: 0;
		margin: auto;
    width: 400px;
    height: 200px;
    background-color: #ccc;
}
```
首先上下左右的定位都为0，此时盒子会铺满整个页面；然后设置宽高，盒子会挨着左上角定位。最后设置上下所有的margin为auto，就显示在页面中间。<br />不设宽高会铺满整个页面，不设margin：auto会定位在左上角。盒子可以为百分比的宽度。

**方式五**<br />四个方向都为0，则铺满整个页面，将四个方向的值都设为相同，则实现垂直水平居中。
```css
.layer {
  position: fixed;  
  left: 20px;
  right: 20px;
  top: 20px;
  bottom: 20px;
  background-color: rgba(40, 40, 40, 0.5);
}
```

## flex布局
```html
<div class="box-wrap">
  <div class="box">内容内容内容内容</div>
</div>
```

```css
.box-wrap {
  width: 200px;
  height: 200px;
  border: 1px solid #000;
}
.box {
  width: 100px;
  height: 100px;
  background-color: green;
}
```

**方式一**
```css
.box-wrap {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

**方式二**
```css
.box-wrap {
  display: flex;
}
.box {
  margin: auto;
}
```

## grid布局
```css
.box-wrap {
  display: grid;
  width: 200px;
  height: 200px;
  border: 1px solid #000;
}
.box {
  align-self: center;
  justify-self: center;
  width: 100px;
  height: 100px;
  background-color: green;
}
```
## table-cell
```css
.box-wrap {
  display: table-cell;
  text-align: center;
  vertical-align: middle;
  width: 200px;
  height: 200px;
  border: 1px solid #000;
}
.box {
  display: inline-block;
  text-align: left;
  width: 100px;
  height: 100px;
  background-color: green;
}
```
## line-height
```css
.box-wrap {
  /* wrap的行高应该和它自身的高度保持一致 */
  line-height: 200px;
  text-align: center;
  width: 200px;
  height: 200px;
  border: 1px solid #000;
}
.box {
  display: inline-block;
  vertical-align: middle;
  /* 修正行高 */
  line-height: initial;
  /* 修正文字方向 */
  text-align: left;
  width: 100px;
  height: 100px;
  background-color: green;
}
```

# 溢出内容出现在padding中
如果一个box带有padding，那么溢出的内容会占用padding的空间。
```html
<div class="box">这是以一条很长的内容</div>
```
```css
.box {
	width: 100px;
	box-sizing: border-box;
	white-space: nowrap;
	overflow: hidden;
	background-color: #ccc;
  padding-right: 10px;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628174968915-724ad839-e171-461d-a58b-f7a173add1b8.png#averageHue=%2382a6c7&clientId=u1403c844-a0bb-4&from=paste&height=26&id=u638a86ec&originHeight=52&originWidth=202&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3871&status=done&style=none&taskId=u7c31053d-bf12-4e71-b496-ef2d66d437b&title=&width=101)<br />可以使用透明的边框来代替padding。
```css
.box {
	width: 100px;
	box-sizing: border-box;
	white-space: nowrap;
	overflow: hidden;
	background-color: #ccc;
	border-right: 10px solid transparent;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628175023480-66ba6a00-3154-46d2-8b66-f493d1ac7598.png#averageHue=%2382a6c7&clientId=u1403c844-a0bb-4&from=paste&height=26&id=uf3f62231&originHeight=52&originWidth=210&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3546&status=done&style=none&taskId=uc18c6c2b-aa50-4e36-b1c4-3ac956e92f4&title=&width=105)

# 渐变色边框
**方式一**<br />这种方式不支持圆角。
```css
.box {
	  width: 200px;
		height: 40px;
		border: 2px solid;
		border-image: linear-gradient(to right, green, pink) 2 2;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628176200968-a414a804-be5a-4e04-bcb6-4a4cb41f4c1c.png#averageHue=%23fdfdfd&clientId=u7e25f617-643c-4&from=paste&height=53&id=u71a8f61f&originHeight=106&originWidth=426&originalType=binary&ratio=1&rotation=0&showTitle=false&size=7757&status=done&style=none&taskId=uec827604-3353-480a-883a-4f6e90f1de7&title=&width=213)

**方式二**<br />**     **外层元素设置渐变的背景色。
```html
<div class="wrap">
	 <div class="box"></div>
</div>
```
```css
.wrap {
		width: 200px;
		height: 40px;
		padding: 2px;
		border-radius: 20px;
		background-image: linear-gradient(to right, green, pink);
  	box-sizing: border-box;
}
.box {
		height: 36px;
		border-radius: 18px;
		background-color: #fff;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628176709883-30933821-0d53-48a9-9e35-0a2834973011.png#averageHue=%23fdfdfd&clientId=u7e25f617-643c-4&from=paste&height=53&id=u868a8b9e&originHeight=106&originWidth=410&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9847&status=done&style=none&taskId=uf89a5b7f-6fbb-4249-adf2-cc29d8c563d&title=&width=205)

**方式三**<br />使用伪元素。<br />使用这种方式box不能是input、textarea、image等元素。<br />伪元素指定生成内容的样式和显示的位置，before和after伪元素指定了一个元素文档树内容之前和之后的内容。'content'属性，与这些伪元素联用，指定了插入的内容。<br />这些元素的内容模型为空，没有独立的闭合标签，无法容纳别的标签作为自身的子元素。
```css
.box {
	position: relative;
	width: 200px;
	height: 36px;
	border-radius: 18px;
	background-color: #fff;
	display: inline-block;
}

.box::after {
	content: '';
	position: absolute;
	top: -2px;
	bottom: -2px;
	left: -2px;
	right: -2px;
	background-image: linear-gradient(to right, green, pink);
	border-radius: 20px;
	z-index: -1;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628176709883-30933821-0d53-48a9-9e35-0a2834973011.png#averageHue=%23fdfdfd&clientId=u7e25f617-643c-4&from=paste&height=53&id=uOBmV&originHeight=106&originWidth=410&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9847&status=done&style=none&taskId=uf89a5b7f-6fbb-4249-adf2-cc29d8c563d&title=&width=205)

**方式四**<br />     最推荐，没有什么限制，也不需要在外边再包一个div。
```css
.box {
	width: 200px;
	height: 40px;
	border: 2px solid transparent;
	background-image: linear-gradient(#fff, #fff), linear-gradient(to right, green, pink);
	background-clip: padding-box, border-box;
	background-origin: border-box;
	border-radius: 20px;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1628176709883-30933821-0d53-48a9-9e35-0a2834973011.png#averageHue=%23fdfdfd&clientId=u7e25f617-643c-4&from=paste&height=53&id=Pm5UX&originHeight=106&originWidth=410&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9847&status=done&style=none&taskId=uf89a5b7f-6fbb-4249-adf2-cc29d8c563d&title=&width=205)

# 渐变文字




# 灵活的边框

```less
.border-bottom(@left: 10px, @right: 10px) {
  position: relative;
  &:before {
    content: '';
    position: absolute;
    bottom: 0;
    left: @left;
    right: @right;
    background-color: #eee;
    height: 1px;
  }
}


.border-top(@left, @right) {
  position: relative;
  &:before {
    content: '';
    position: absolute;
    top: 0;
    left: @left;
    right: @right;
    background-color: #eee;
    height: 1px;
  }
}
```
