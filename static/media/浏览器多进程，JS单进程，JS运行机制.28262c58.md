 参考是：https://juejin.im/post/5a6547d0f265da3e283a1df7
这篇文章写的很好！清楚明白。
跟着小陈一起梳理知识点。
1，区分进程和线程
进程比作工厂，线程比作工厂内的工人。

> - 工厂的资源 -> 系统分配的内存（独立的一块内存）
> - 工厂之间的相互独立 -> 进程之间相互独立
> - 多个工人协作完成任务 -> 多个线程在进程中协作完成任务
> - 工厂内有一个或多个工人 -> 一个进程由一个或多个线程组成
> - 工人之间共享空间 -> 同一进程下的各个线程之间共享程序的内存空间（包括代码段、数据集、堆等）

从上面我们再总结下，**进程是CPU资源分配的最小单位，线程是CPU调度的最小单位**。
一般来说，单线程和多线程，指的是在一个进程内的单和多。
## 浏览器是多进程的
1，系统给浏览器的进程分配的资源（CPU，内存）
2，每打开一个Tab页，就相当于创建了一个独立的浏览器进程

**浏览器包含的进程**有：
1，Browser进程：浏览器主进程（负责协调，主控）
作用有：
（1），浏览器界面显示，交互，如前进后退；
（2），负责各页面的管理，销毁和创建其他进程；
（3），将Render进程得到的内存中的Bitmap，绘制到用户界面上；
（4），网络资源的管理、下载；
2，第三方插件进程：每个类型的插件对应一种进程
3，GPU进程：最多一个，用于3D绘制等；
4，**浏览器渲染进程（浏览器内核）**（renderer进程，内部是多线程的）：默认每一个Tab一个进程互不影响，主要作用为：页面渲染，脚本执行，事件处理等

多个空白页可能被合并为一个进程。
**浏览器多进程的优势**：
1，避免单个页面Crash影响整个浏览器；
2，避免第三方插件影响整个浏览器；
3，多进程充分利用多核优势；
4，方便使用沙盒模型隔离插件等进程，提高浏览器稳定性；

## 浏览器内核-渲染进程
页面的渲染，JS的执行，事件的循环都在这个进程里执行。

浏览器的渲染进程是**多线程**的！
常驻线程有：
1.GUI渲染线程：
 - 负责渲染界面，解析HTML，CSS，构建DOM树和RenderObject树，布局和绘制等；
 - 当页面需要重绘（Repaint）或某种操作引发回流（reflow），该线程就会执行； 
 - GUI渲染线程与JS引擎线程是互斥的，也就说JS引擎执行时，GUI会被挂
 

2.**JS引擎**线程：
 - JS内核，负责处理JavaScript脚本程序；
 - JS引擎线程负责**解析JavaScript脚本，运行代码；**
 - JS引擎一直等待任务对列中的任务的到来，然后加以处理，一个Tab页中无论什么时候都只有一个JS线程在运行JS程序；
3.事件触发线程
 - 归属于浏览器而不是JS引擎，用来控制事件循环；
 - 当JS引擎执行代码块如setTimeout时（例如鼠标点击，Ajax异步请求等），会将对应任务添加到事件线程中；
 - 当对应事件符合触发条件被触发时，该线程会把事件添加到待处理队列的队尾，等待JS引擎的处理；
 - 由于JS的单线程，所以待处理队列中的事件都得排队等待JS引擎的处理；
4.定时触发器线程
 - 传说中的setTimeout和setInterval所在线程(注，setInterval最低10ms，setTimeout最低4ms)
 - 因为浏览器定时计数器并不是由JavaScript引擎计数的，（为什么？因为JS引擎是单线程，如果阻塞线程状态会影响计时的准确）
 - 通过单独线程来计时并触发定时
5.异步Http请求线程
 - 在XMLHTTPRequest在连接后是通过浏览器新开一个线程请求；
 - 在检测到状态变更时，如果设置回调函数，异步线程就产生状态变更事件，这个回调再次放入事件队列，由JS引擎执行；

![浏览器内核进程图](https://img-blog.csdnimg.cn/20190412160138938.png)
## Browser进程和浏览器内核（Renderer进程）的通信
打开一个浏览器，任务管理器出现两个进程。
1，主控进程；
2，Tab页的渲染进程；

整个过程：Browser进程收到用户请求，首先需要获取网页内容，然后将该任务通过RendererHosts接口传递给Render进程；->Render进程接收到消息，简单解释后交给渲染线程；->渲染线程接收请求，加载网页并渲染网页，（Browser进程获取资源，GPU进程帮忙渲染）->JS操作DOM（有可能造成回流和重构）->Render进程将结果传给Browser进程 -> Browser进程接收到结果并将结果绘制出来。

![打开一个网页的进程交互](https://img-blog.csdnimg.cn/2019041520143615.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTgzNTk3Nw==,size_16,color_FFFFFF,t_70)
## 浏览器内核中线程之间的关系
1，GUI渲染线程和JS引擎线程互斥
2，JS阻塞页面加载
3，**WebWorker，JS的多线程：**
**JS引擎是单线程**的，而且JS执行时间过长会阻塞页面。
但HTML5支持了web worker。

> Web Worker为Web内容在后台线程中运行脚本提供了一种简单的方法。线程可以执行任务而不干扰用户界面
> 
> 一个worker是使用一个构造函数创建的一个对象(e.g. Worker()) 运行一个命名的JavaScript文件 
> 
> 这个文件包含将在工作线程中运行的代码; workers 运行在另一个全局上下文中,不同于当前的window
> 
> 因此，使用 window快捷方式获取当前全局的范围 (而不是self) 在一个 Worker 内将返回错误

创建worker时，JS引擎向浏览器申请开一个子线程，JS引擎线程和Worker线程间通过（postMessage API，序列化对象来与线程交互特定的数据）通信。

如果有非常耗时的工作，请单独开一个Worker线程，这样就不会影响JS引擎主线程，只要等待结果出来后，通信传给主线程。
Worker可以理解为浏览器给JS引擎开的外挂，专门用来解决大量计算问题。

## WebWorker与SharedWorker
1，WebWorker只属于某个页面，不会与其他页面的浏览器内核进程共享；所以Chrome在Render进程中，创建新的线程运行worker中的JS程序；
2，SharedWorker是浏览器所有页面共享的，不能采用与Worker同样的方式实现，因为它不隶属于某个Render进程，可以为多个Render进程共享；所以Chrome为SharedWorker单独创建一个进程运行JS程序；
3，因此，SharedWorker由独立的进程单独管理，WebWorker只是属于render进程下的一个线程。
## 浏览器的渲染过程
1，浏览器输入url，浏览器主进程接管，开启一个下载线程；
2，下载线程进行Http请求，（DNS查询，IP寻址等等）；
3，等待服务器响应，获取内容；
4，内容通过renderedHost接口转交给Renderer进程；

**5，浏览器渲染流程开始：**
1，解析HTML建立dom树；
2，解析解析CSS建立render树；（将CSS代码解析成树形的数据结构，然后结合DOM合并成render树）；
3，布局render树（Layout/reflow），负责各元素尺寸和位置的计算；
4，绘制render树（paint），绘制页面像素信息；
5，浏览器会将各层的信息发送给GPU，GPU会将层合成，显示在屏幕上；
渲染完毕，开始load。
![DOM](https://img-blog.csdnimg.cn/2019041617093820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTgzNTk3Nw==,size_16,color_FFFFFF,t_70)
注意！load事件和DOMContentLoaded事件的先后：当DOMContentLoaded事件触发时，仅当DOM加载完成，不包括样式表和图片；当onload事件触发时，页面时所有的DOM，CSS，脚本，图片都加载完毕了。
所以DOMContentLoaded->load

下面的知识点，我在另一篇浏览器阻塞中有写过。
1，css加载是否会阻塞dom树的渲染，头部引入css的情况下。
css是由单独的下载线程异步下载的。
css加载不会阻塞DOM树解析（**异步加载时DOM照常构建**）
但会阻塞render树渲染（渲染时需等css加载完毕，因为render树需要css信息）

渲染之后，提到了composite概念，浏览器渲染的图层包含两大类：普通图层，符合图层。
普通文档流可以理解为一个复合图层，无论添加多少元素都是在同一个复合图层里。
其次，absolute（fixed）也一样，虽然可以脱离普通文档流，但仍属于默认复合层；

硬件加速声明一个新的复合图层，它会单独分配资源，。
GPU中，各个复合图层是单独控制的，所以互不影响。

## 如何变成复合图层（硬件加速）
1，最常用的方式：translate3d、translateZ
2，opacity属性/过渡动画（需要动画执行的过程中才会创建合成层，动画没有开始或结束后元素还会回到之前的状态）
3，will-chang属性（这个比较偏僻），一般配合opacity与translate使用（而且经测试，除了上述可以引发硬件加速的属性外，其它属性并不会变成复合层），
作用是提前告诉浏览器要变化，这样浏览器会开始做一些优化工作（这个最好用完后就释放）
4，<video><iframe><canvas><webgl>等元素
5，其它，譬如以前的flash插件

absolute虽然可以脱离文档流，但是无法脱离默认复合图层。
**复合图层的作用？**
一般一个元素开启硬件加速后会变成复合图层，可以独立于普通文档流中，**改动后可以避免整个页面重绘，提升性能**。
但是尽量不要大量使用复合图层，否则由于**资源消耗**过度，页面反而会变的更卡

**硬件加速时请使用index**
使用硬件加速时，尽可能的使用index，防止浏览器默认给后续的元素创建复合层渲染
具体的原理时这样的：
**webkit CSS3中，如果这个元素添加了硬件加速，并且index层级比较低，
那么在这个元素的后面其它元素（层级比这个元素高的，或者相同的，并且releative或absolute属性相同的），会默认变为复合层渲染，如果处理不当会极大的影响性能**
简单点理解，其实可以认为是一个隐式合成的概念：如果a是一个复合图层，而且b在a上面，那么b也会被隐式转为一个复合图层，这点需要特别注意。

## 从EventLoop看JS的运行机制
啊，终于到这里了。
前情回顾，***JS引起单线程，事件触发线程，定时触发线程***
同时，需要了解1，JS分同步任务和异步任务；2，同步任务在主线程上执行，形成一个执行栈；3，主线程之外**事件触发线程管理一个任务队列**，只要异步任务有了运行结果，就在任务队列中放置一个事件；4，一旦执行栈中所有**同步任务**执行完毕，此时JS引擎空闲，那么系统就会读取任务队列，将可运行的异步任务添加到执行栈中。
![事件运行机制](https://img-blog.csdnimg.cn/20190416194546509.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTgzNTk3Nw==,size_16,color_FFFFFF,t_70)
![事件循环机制](https://img-blog.csdnimg.cn/20190416194647247.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTgzNTk3Nw==,size_16,color_FFFFFF,t_70)

 - 主线程运行时会产生执行栈，
 - 栈中的代码调用某些api时，它们会在事件队列中添加各种事件（当满足触发条件后，如ajax请求完毕）
 - 而栈中的代码执行完毕，就会读取事件队列中的事件，去执行那些回调
如此循环
注意，总是要等待栈中的代码执行完毕后才会去读取事件队列中的事件

## 定时器部分
事件循环机制的核心是：JS引擎，事件触发线程；
调用setTimeout后，如何等待特定时间后才添加到事件队列的呢。
它有定时器线程单独控制，为什么？因为JS引擎单线程，处于阻塞线程状态就会影响计时的准确。
当使用setTimeout或setInterval时，它需要定时器线程计时，计时完成后就会将特定的事件推入事件队列中。
注意：只有可执行栈内空了后才会主动读取事件队列。

因为每次setTimeout计时到后就会去执行，然后执行一段时间后才会继续setTimeout，中间就多了误差。
（误差多少与代码执行时间有关）
而setInterval则是每次都精确的**隔一段时间**推入一个事件
但是，事件的实际执行时间不一定就准确，还有可能是这个事件还没执行完毕，下一个事件就来了。
而且setInterval有一些比较致命的问题就是：
**累计效应**，如果setInterval代码在（setInterval）再次添加到队列之前还没有完成执行，
就会导致定时器代码连续运行好几次，而之间没有间隔。就算正常间隔执行，多个setInterval的代码执行时间可能会比预期小（因为代码执行需要一定时间）
譬如像iOS的webview,或者Safari等浏览器中都有一个特点，在滚动的时候是不执行JS的，如果使用了setInterval，会发现在滚动结束后会执行多次由于滚动不执行JS积攒回调，如果回调执行时间过长,就会非常容器造成卡顿问题和一些不可知的错误。
而且把浏览器最小化显示等操作时，setInterval并不是不执行程序，它会把setInterval的回调函数放在队列中，等浏览器窗口再次打开时，一瞬间全部执行时
所以，鉴于这么多但问题，目前一般认为的最佳方案是：**用setTimeout模拟setInterval**，或者**特殊场合直接用requestAnimationFrame**

补充：JS高程中有提到，JS引擎会对setInterval进行优化，如果当前事件队列中有setInterval的回调，不会重复添加。不过，仍然是有很多问题。

## 事件循环进阶
JS中分为两种任务类型：macrotask和microtask，在ECMAScript中，microtask称为jobs，macrotask可称为task。
macrotask（又称之为宏任务），可以理解是每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）。
每一个task会从头到尾将这个任务执行完毕，不会执行其它。
浏览器为了能够使得JS内部task与DOM任务能够有序的执行，会在一个task执行结束后，在下一个 task 执行开始前，对页面进行重新渲染。
（task->渲染->task->...）
microtask（又称为微任务），可以理解是在当前 task 执行结束后立即执行的任务
也就是说，在当前task任务后，下一个task之前，在渲染之前
所以它的响应速度相比setTimeout（setTimeout是task）会更快，因为无需等渲染
也就是说，在某一个macrotask执行完后，就会将在它执行期间产生的所有microtask都执行完毕（在渲染前）

**各自场景：**
macrotask：主代码块，setTimeout，setInterval等（可以看到，事件队列中的每一个事件都是一个macrotask）
microtask：Promise，process.nextTick等
例子：

```
console.log('script start');
setTimeout(function() {
    console.log('setTimeout');
}, 0);
Promise.resolve().then(function() {
    console.log('promise1');
}).then(function() {
    console.log('promise2');
});
console.log('script end');
```
运行结果：

```
script start
script end
promise1
promise2
setTimeout

```
在这段例子中，**执行栈**中的start，end先执行，接着执行事件队列里累计的事件任务。宏任务执行完毕以后，将微任务队列中的（Promise产生的microtask）执行完毕，继续下一个宏任务，setTimeout产生的事件。

在node环境下，**process.nextTick的优先级高于Promise**，也就是可以简单理解为：在宏任务结束后会先执行微任务队列中的nextTickQueue部分，然后才会执行微任务中的Promise部分。

再根据线程来理解下：
macrotask中的事件都是放在一个事件队列中的，而这个队列由**事件触发线程**维护。
microtask中的所有微任务都是添加到**微任务队列**（Job Queues）中，等待当前macrotask执行完毕后执行，而这个队列由**JS引擎线程**维护。

![事件运行](https://img-blog.csdnimg.cn/20190416200851866.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTgzNTk3Nw==,size_16,color_FFFFFF,t_70)
再次po上原作者链接！！！这篇文章真的不错！
作者：dailc
链接：https://juejin.im/post/5a6547d0f265da3e283a1df7
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。