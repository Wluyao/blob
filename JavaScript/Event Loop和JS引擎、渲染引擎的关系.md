[JS 运行机制最全面的一次梳理](https://mp.weixin.qq.com/s/PhuhUtQGz0uPxVrtNB4TCA)
# JS引擎
## 组成
js 引擎包括 parser、解释器、gc 再加一个 JIT 编译器这几部分。 	

- parser：负责把 javascript 源码转成 AST。
- interperter：解释器， 负责转换 AST 成字节码，并解释执行。
- JIT compiler：对执行时的热点函数进行编译，把字节码转成机器码，之后可以直接执行机器码。
- gc（garbage collector）：垃圾回收器，清理堆内存中不再使用的对象。

## 编译流水线
一般的 JS 引擎的编译流水线是 parse 源码成 AST，之后 AST 转为字节码，解释执行字节码。运行时会收集函数执行的频率，对于到达了一定阈值的热点代码，会把对应的字节码转成机器码（JIT），然后直接执行。这就是 js 代码能够生效的流程。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627650208557-aa6861bd-1195-42ac-b552-ff1d998c3de6.png#clientId=u0b4f2906-dc5e-4&from=paste&height=130&id=uf71b9936&originHeight=202&originWidth=658&originalType=binary&ratio=1&rotation=0&showTitle=false&size=51472&status=done&style=none&taskId=ua092c82e-1d10-44d9-b3b8-11debc4679b&title=&width=424)
# 渲染引擎
渲染时会把 html、css 分别用 parser 解析成 dom 和 cssom，然后合并到一起，并计算布局样式成绝对的坐标，生成渲染树，之后把渲染树的内容复制到显存就可以由显卡来完成渲染。<br /> ![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627650250218-ffb7cbac-28e5-485c-a9e9-0001dbd18af7.png#clientId=u0b4f2906-dc5e-4&from=paste&height=121&id=u456a3e6e&originHeight=184&originWidth=658&originalType=binary&ratio=1&rotation=0&showTitle=false&size=41424&status=done&style=none&taskId=u4d9fd9d5-0ce7-408c-8edf-7ab4cce7857&title=&width=433)<br />每一次渲染流程叫做一帧，浏览器会有一个帧率（比如一秒 60帧）来刷新。 

# JS引擎和渲染引擎的结合
JS引擎只负责执行JS代码，渲染引擎只负责渲染，它们两个并不知道彼此，但是要完成一个完整的网页应用，这两者都需要。有以下两种思路来结合它们。<br />**1.多线程**<br />主线程用来操作UI和渲染，其他线程用来执行一些任务(不能多个线程同时修改UI，这样顺序没法控制)。<br />比如说安卓UI架构：在主线程里面完成UI的更新。事件的绑定和其他逻辑放到别的线程，完成以后就在消息队列中存入一个消息，主线程辉不断循环取出消息去执行。<br />所有的ui系统的设计，如果使用了多线程（进程）的架构，基本都是ui只能在一个线程（进程）中操作，由别的线程（进程）来发消息到这边来更新。同时会有一个消息队列和 looper做异步消息的处理。各个子线程（进程）是消息队列的生产者，主线程（进程）消费者。

**2.单线程**<br />JS在一个线程内进行逻辑计算和 dom 操作，JS 执行渲染相互阻塞，event loop就用来让JS引擎和渲染引擎进行配合。<br />event loop是宿主环境为了集合渲染和JS执行，也为了处理JS执行时的高优先级任务而设计的机制。宿主环境有浏览器、node、跨端引擎等。
## 每次事件循环结束后的检查
在浏览器里执行一个JS任务就是一次event loop，每次事件循环结束会检查下是否需要渲染，是否需要处理worker的消息，通过这种每次循环结束都进行检查的方式来综合渲染、JS执行、worker，让它们都能在一个线程内得到执行（渲染其实是在别的线程，但是会和 JS 线程相互阻塞）。这样就解决了渲染、JS 执行、worker 这三者的调度问题。
## micro tasks
如果任务列队中有更高优先级的任务是不是要等所有任务都执行完才能被执行？所以还不行，要给 event loop 加上“急事”处理的快速通道，这就是微任务 micro tasks。<br />给优先级高的“急事”加入了插队机制，在执行完宏任务之后，就把所有的急事（micro task）全部处理完。
## requestAnimationFrame
在JS执行完，开始渲染之前会有一个生命周期，就是requestAnimationFrame。在这里边做一些计算是最合适的，能保证一定是在渲染之前做的计算。<br />requestAnimationFrame 是每次结束发现需要渲染，在渲染之前执行的一个回调函数，不是宏任务也不是微任务。
## requestIdleCallback
每一帧的计算和渲染是有固定频率的，如果 JS 执行时间过长，超过了一帧的刷新时间，那么就会导致渲染延迟，甚至掉帧（因为上一帧的数据还没渲染到界面就被覆盖成新的数据了）。<br />macrotask、microtask、requestAnimationFrame、requestIdleCallback 都有可能导致阻塞事件循环结束后的检查，这样等到了 check 的时候发现要渲染，再去渲染的时候就晚了。<br />除了JS 代码本身要注意不能让计算阻塞了渲染。而且浏览器提供了requestIdeCallback API 用来在每帧间隔执行，这样就不会阻塞了。<br />requestIdleCallback 会在每次 check 结束发现距离下一帧的刷新还有时间，就执行一下。如果时间不够，就下一帧再说。如果每一帧都没时间，那也不行，所以提供了 timeout 的参数可以指定最长的等待时间，如果一直没时间执行这个逻辑，那么就算拖延了帧渲染也要执行。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1561110/1627650564184-a470ff0b-47ec-4505-a973-29158f308834.png#clientId=u0b4f2906-dc5e-4&from=paste&height=305&id=ubbf5417f&originHeight=610&originWidth=618&originalType=binary&ratio=1&rotation=0&showTitle=false&size=148683&status=done&style=none&taskId=u7bdc8bf4-7f3a-4d32-a863-18b291e38d9&title=&width=309)
