# 介绍
web缓存主要指的是两部分：浏览器缓存和http缓存。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627652344186-d6076aee-a565-401e-bf13-93cecba8a8b1.png#averageHue=%23fefdfd&clientId=uf129fb23-f752-4&from=paste&id=Ka9Xp&originHeight=396&originWidth=1032&originalType=binary&ratio=1&rotation=0&showTitle=false&size=90459&status=done&style=none&taskId=uc5660348-a3fc-4b28-b4c9-091ead61ecf&title=)

## 浏览器缓存
比如,localStorage,sessionStorage,cookie等等。这些功能主要用于缓存一些必要的数据，比如用户信息。比如需要携带到后端的参数。亦或者是一些列表数据等等。

## HTTP缓存
HTTP缓存是可以自动保存常见文档副本的 HTTP 设备。当 Web 请求抵达缓存时， 如果本地有“已缓存的”副本，就可以从本地存储设备而不是原始服务器中提取这 个文档。<br />![](https://cdn.nlark.com/yuque/0/2022/webp/1561110/1662599686508-c234f084-535b-456e-8cfc-5a92bc794414.webp#averageHue=%23f5f5f5&clientId=ue8d82de3-217c-4&from=paste&id=udba7fda9&originHeight=1163&originWidth=1875&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ueded5b3f-4fd1-4485-8798-6ae6a5cf7a9&title=)

## 优缺点
### 优点

- 减少不必要的网络传输，节约宽带。
- 更快的加载页面。
- 减少服务器负载，避免服务器过载的情况出现。

### 缺点

- 占内存


# 强缓存
不会向服务器发送请求，直接从内存中读取资源。 请求返回200的状态码。<br />在控制台的network选项中可以看到size显示disk cache或memory cache。<br />memory cache代表使用内存中的缓存，disk cache则代表使用的是硬盘中的缓存，浏览器读取缓存的顺序为memory -> disk。<br />几乎所有的网络资源都会被浏览器自动加入到 memory cache 中，一般地，当关闭浏览器窗口后，memory cache就失效了。<br />disk cache 存储在硬盘上的缓存，因此它是持久存储的，是实际存在于文件系统中的。<br />入口文件index.html不会被缓存。

服务端通过过在响应头设置Cache-Control或者Expires来设置强缓存策略：
:::info
Cache-Control：max-age=3600;<br />Expires: Thu, 25 May 2020 12:30:00 GMT
:::




## Expires
在以前，通常会使用响应头的Expires字段去实现强缓存。

在响应头中携带的资源过期时间。如果本地时间没有超过该时间则直接命中强缓存。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627652400123-6d967e10-80fe-4189-b6ef-5861acab6912.png#averageHue=%23ededed&clientId=uf129fb23-f752-4&from=paste&id=EUxQe&originHeight=30&originWidth=404&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5465&status=done&style=none&taskId=u4f76bca1-9ca7-4eec-a65e-30488e1ebb4&title=)

Expires已经被废弃了。因为Expires判断强缓存是否过期的机制是：获取本地时间戳，然后和先前拿到的资源文件中的Expires字段的时间做比较，来判断是否需要对服务器发起请求。如果本地与服务器时间不同步，就会出现资源无法被缓存或者资源永远被缓存的情况。所以，强缓存功能通常使用cache-control字段来代替Expires字段。



## Cache Control
Cache-Control与Expires的作用一致，都是指明当前资源的有效期，控制浏览器是否直接从浏览器缓存中取数据还是重新发送请求到服务器取数据。

Expires 是http1.0的产物，Cache-Control是http1.1的产物。两者同时存在的话，Cache-Control优先级高于Expires。Cache-Control的选择更多，设置更细致。<br />Cache-control只要在资源的响应头上写上需要缓存多久，单位是秒：
```javascript
//往响应头中写入需要缓存的时间
res.writeHead(200,{
  'Cache-Control':'max-age=10'
});
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1561110/1662601205831-abdbf9ce-63a6-4c46-a815-101eb92499eb.png#averageHue=%23f6edeb&clientId=ue8d82de3-217c-4&from=paste&height=177&id=ua1002372&originHeight=310&originWidth=558&originalType=binary&ratio=1&rotation=0&showTitle=false&size=117870&status=done&style=none&taskId=u0247d7ed-c78b-4b52-8d98-7bd2d76e8e8&title=&width=318)


### Cache-control的属性
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627652515135-80074a45-b757-4634-ac33-bb40b7b02c57.png#averageHue=%23f0eef3&clientId=uf129fb23-f752-4&from=paste&id=Oa7Kw&originHeight=416&originWidth=402&originalType=binary&ratio=1&rotation=0&showTitle=false&size=52679&status=done&style=none&taskId=uf65df878-d7ef-4044-a027-31f5ff5250d&title=)

:::info
Cache-control:max-age=10000,s-maxage=200000,public
:::


#### max-age
缓存在多少秒之后失效，单位为秒（s）。<br /> 从第一次请求资源的时候开始，往后N秒内，资源若再次请求，则直接从磁盘（或内存）中读取，不与服务器做任何交互。

#### s-maxage
max-age表示的资源在客户端缓存的时长，而s-maxage表示资源在代理服务器可以缓存的时长，比如CDN缓存。<br />如果存在s-maxage，它会覆盖max-age和Expires 。<br />它必须和public属性一起使用（public属性表示资源是否可以在代理服务器中缓存）。

####  no-cache
强制进行协商缓存。<br />在Cache-control中设置了no-cache，那么该资源会跳过强缓存的校验，直接进行协商缓存。可理解为"max-age=0，must-revalidate"。<br />因此有的时候只设置no-cache防止缓存就是不够保险，还可以加上private指令，将过期时间设置为过去的时间。 


#### no-store
不允许缓存资源。不进行强缓存，也不进行协商缓存。<br />no-cache和no-store是一组互斥属性，这两个属性不能同时出现在Cache-Control中。

#### public
资源在客户端和代理服务器都可以缓存，并且在用户间共享。默认是public。

#### private
资源只能在客户端缓存，不能在用户间共享。如果需要HTTP认证，默认是private。 


一般请求是从客户端直接发送到服务端：<br />![](https://cdn.nlark.com/yuque/0/2022/webp/1561110/1662602299498-f50ed5d6-9a0a-4a7e-b0dd-d3413f8c9b7b.webp#averageHue=%23fafafa&clientId=ue8d82de3-217c-4&from=paste&height=102&id=ucd603a4d&originHeight=156&originWidth=718&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=uf7148759-79b0-4ecf-b051-5a11963b480&title=&width=469)<br />但有些情况下是例外的，比如，出现代理服务器：<br />![](https://cdn.nlark.com/yuque/0/2022/webp/1561110/1662602342924-c03424bf-36dd-4fb5-836b-19fd2c12ea48.webp#averageHue=%23fbfbfb&clientId=ue8d82de3-217c-4&from=paste&height=115&id=u6344e03c&originHeight=248&originWidth=1281&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ub226c875-a7ab-4128-8c87-1a04f9a6a47&title=&width=596)<br />public和private就是决定资源是否可以在代理服务器进行缓存的属性。

如果这两个属性值都没有被设置，则默认为private<br />public和private也是一组互斥属性。他们两个不能同时出现在响应头的cache-control字段中。


#### must-revalidate
如果怕缓存不过期就可以继续使用。但是过期了还想用就必须去服务器验证。

# 协商缓存
协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程。协商缓存生效，返回304和Not Modified。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627653014654-f2d0d933-3fcc-43d1-a92f-db67da735fbd.png#averageHue=%23f3f3f8&clientId=uf129fb23-f752-4&from=paste&id=u1b31add7&originHeight=172&originWidth=422&originalType=binary&ratio=1&rotation=0&showTitle=false&size=11611&status=done&style=none&taskId=ue16a0c9d-631c-427a-843a-de1b7fae584&title=)

## Last-Modified和If-Modified-Since
在第一次请求资源时，服务器返回资源的同时，在响应头中添加Last-Modified，值是这个资源在服务器上的最后修改时间，浏览器接收后缓存资源和它的修改时间。<br />后续客户端每次请求这个资源，都会在请求头中添加If-Modified-Since，值就是之前保存的Last-Modified。	<br />服务器再次收到这个资源请求，根据请求头中的If-Modified-Since 中与服务器中该资源的最后修改时间对比。如果没有变化，返回304和空的响应体，客户端直接从浏览器的缓存读取资源；如果If-Modified-Since的时间小于服务器中这个资源的最后修改时间，说明文件有更新，于是返回新的资源文件和200。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627653104904-c3229180-60b2-45ab-a8f4-6b38413f1049.png#averageHue=%23eeeeee&clientId=uf129fb23-f752-4&from=paste&id=u918c4274&originHeight=34&originWidth=446&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5619&status=done&style=none&taskId=ub440f0ad-cdec-4c4f-8497-32cd4714b4e&title=)

![](https://cdn.nlark.com/yuque/0/2022/webp/1561110/1662603156418-281475f7-12de-497a-9b82-538dd6de857d.webp#averageHue=%23fafafa&clientId=u41261789-a3dd-4&from=paste&height=443&id=u3f0e2db1&originHeight=955&originWidth=1274&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u659309ef-8545-44d5-a57b-94b07bb0c4f&title=&width=591)

使用这种方式的协商缓存存在两个非常明显的漏洞。这两个漏洞都是因为比较的是文件的修改时间。<br />1.因为是根据文件修改时间来判断的，所以在文件内容本身不修改的情况下，依然有可能更新文件修改时间（比如修改文件名再改回来），这样就有可能文件内容明明没有修改，但是缓存依然失效了。<br />2.当文件在极短时间内完成修改的时候（比如几百毫秒）。因为文件修改时间记录的最小单位是秒，所以，如果文件在几百毫秒内完成修改的话，文件修改时间不会改变，这样即使文件内容修改了依然不会<br />返回新的文件。<br />为了解决上述的这两个问题。从http1.1开始新增了一个头信息，ETag(Entity 实体标签)



## ETag和If-None-Match
在第一次请求资源时，服务器返回资源的同时，在响应头中添加Etag(校验值)，值代表该资源的唯一标识，只要资源有变化，Etag就会重新生成，浏览器接收后缓存资源和Etag。<br />后续客户端每次请求资源时，在请求头里带上If-None-Match，值就是之前保存的Etag。<br />服务器只需要比较客户端传来的If-None-Match跟自己服务器上该资源的ETag是否一致。如果服务器发现ETag匹配不上，那么直接将新的资源（包括新的ETag）发给客户端；如果ETag是一致的，则直接返回304告诉客户端直接使用本地缓存。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627653186461-889ea2f3-8bd5-4a65-9f78-22c41d326b56.png#averageHue=%23e8e8e8&clientId=uf129fb23-f752-4&from=paste&id=ucb949a72&originHeight=28&originWidth=402&originalType=binary&ratio=1&rotation=0&showTitle=false&size=6191&status=done&style=none&taskId=u410a458e-4c97-42c9-8972-8b6e620c72d&title=)![](https://cdn.nlark.com/yuque/0/2022/webp/1561110/1662603452130-4e292252-a6eb-4788-a05f-96e0798a89e7.webp#averageHue=%23fbfafa&clientId=u41261789-a3dd-4&from=paste&id=ud3fa6b7a&originHeight=968&originWidth=1272&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=uf4c2cbaa-caf4-475a-a56f-e12f8604bd4&title=)

-  精确度上，Etag要优于Last-Modified，Last-Modified的时间单位是秒，如果某个文件在1秒内改变了多次，那么他们的Last-Modified其实并没有体现出来修改，但是Etag每次都会改变确保了精度；如果是负载均衡的服务器，各个服务器生成的Last-Modified也有可能不一致。 	
- 性能上，Etag要逊于Last-Modified，毕竟Last-Modified只需要记录时间，而Etag需要服务器通过算法来计算出一个hash值。
-  优先级上，服务器校验优先考虑Etag。

# 其他

## 页面刷新对缓存的影响

- 前进/后退：浏览器使用最基本的请求头，没有"cache-control"，所以会检查缓存，直接利用之前的资源，不进行网络通信。 
- F5刷新：浏览器会在请求头里带上"cache-control:max-age=0"。意思是需要新的资源，所以浏览器不会使用缓存，而是向服务器发请求，服务器看到max-age=0就使用最新生成的报文返回。
-  ctrl+F5刷新，请求头里的If-Modified-Since和If-None- Match会被清空，所以服务器返回最新的数据。

## 缓存的设置
从前端的角度来说，什么都不用干，缓存只是缓存在前端，但实际上代码是后端的同学来写的，但是前端也可以通过Nginx去配置。<br />有哈希值的文件设置强缓存(打包后的css文件)，没有哈希值的文件（比如index.html）设置协商缓存。


