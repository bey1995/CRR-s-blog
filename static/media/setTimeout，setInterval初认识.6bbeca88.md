## setInterval
周期性地调用一个函数function或者执行一段代码。
clearInterval：取消调用setInterval设置的重复执行动作

```
var intervalID = window.setInterval(func,delay[,param1,param2,…])；
var intervalID = window.setInterval(code,delay);//不推荐
```
若是取消该周期执行命令，可将intervalID 的值传给clearInterval（）。
delay 是每次延迟的毫秒数 （1s=1000ms)，函数的每次调用会在该延迟之后发生。和setTimeout一样，实际的延迟时间可能会稍长一点。  

    setTimeout(function(){
        //处理代码
        setTimeout(arguments.callee,interval)
    },intercal);

## setTimeout
在指定的延时之后调用一个函数或者执行一个代码片段。
clearTimeout：方法可取消由setTimeout设置的timeout

setTimeout对队列的工作方式：当**特定时间过去后将代码添加到队列**中，但并不意味着会马上将执行，设定一个200ms后执行的定时器，指的是在200ms后它将被添加到队列中，是否执行，还得看队列中是否没有其他的东西。

setTimeout和setInterval函数，都返回一个整数值，表示计数器编号。将该整数传入clearTimeout和clearInterval函数，就可以取消对应的定时器。
## 使用
以下内容参考自[https://www.jeffjade.com/2016/01/10/2016-01-10-javaScript-setInterval/](https://www.jeffjade.com/2016/01/10/2016-01-10-javaScript-setInterval/)

1，setInterval
常见用于轮询。比如轮询一个url的hash值是否会发生变化。
```
var hash = window.location.hash;
    var hashWatcher = setInterval(function () {
        if (window.location.hash != hash){
            updataPage();
        }
    },1000);
```
setInterval指定开始执行间隔，不考虑每次事件消耗的时间，如果事件执行时间小于设定间隔时间，那么执行结束后，执行+等待=设定间隔时间，再次执行；而如果事件执行时间超过设定间隔时间，则结束后立马执行；
例如一次执行，用户始终不进行操作，导致浏览器处于堵塞状态，后面执行将会累计。比如，隔了很久用户才操作，会导致后面第二次、第三次等累计，不会有等待间隔。

```
var timer = setInterval(function() {
        alert(i++);
    }, 1000);
```
比如第一次不立马点击确定，等待3000ms后点击，则第二次会直接执行，第三次也会直接执行，没有等待时间。

如果为了确保两次执行有固定的间隔时间，可以使用setTimeout去指定下次执行时间。

```
function interval(func, wait){
  var interv = function(){
    func.call(null);
    setTimeout(interv, wait);
  };
  setTimeout(interv, wait);
}//循环调用setTimeout模拟setInterval；
interval(function(){
  console.log(2);
},1000);
```
HTML 5标准规定，setInterval 的最短间隔时间是10毫秒，也就是说，**小于10毫秒的时间间隔会被调整到10毫秒。**
**setInterval 的运行机制**是，将指定的代码移出本次执行，等到下一轮Event Loop时，再检查是否到了指定时间。如果到了，就执行对应的代码；如果不到，就等到再下一轮Event Loop时重新判断。这意味着，setTimeout 和 setInterval 指定的代码，必须等到本次执行的所有代码都执行完，才会执行。
每一轮Event Loop时，都会将“任务队列”中需要执行的任务，一次执行完。setTimeout 和 setInterval 都是把任务添加到“任务队列”的尾部。因此，它们实际上要等到当前脚本的所有同步任务执行完，然后再等到本次 Event Loop 的“任务队列”的所有任务执行完，才会开始执行。由于前面的任务到底需要多少时间执行完，是不确定的，**所以没有办法保证 setTimeout 和 setInterval 指定的任务，一定会按照预定时间执行**。
对于setInterval得使用，个人建议是能不用尽量不用。涉及到必须要的定时器，前文已经叙述可以使用两个setTimeout嵌套组合来实现，并且还能规避掉一些问题得发生。涉及到要用它来制作动画（ jQuery就使用setInterval来写动画，也是导致其慢原因之一），更建议使用requestAnimationFrame(RAF)，或者直接采用CSS来写（如果可以的话）。

requestAnimationFrame 比起 setTimeout、setInterval 的优势主要有两点：

 - requestAnimationFrame会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率，一般来说，这个频率为每秒60帧。
 - 在隐藏或不可见的元素requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的的cpu，gpu和内存使用量。

## 例子
内容来自阮一峰的日志！[http://javascript.ruanyifeng.com/advanced/timer.html#toc1](http://javascript.ruanyifeng.com/advanced/timer.html#toc1)
debounce防抖动函数：
```
$('textarea').on('keydown', debounce(ajaxAction, 2500));
//文本输入框，按下按键后则通过ajax发送到服务器查询。
//为了防止连续按键，重复发送；
function debounce(fn, delay){
  var timer = null; // 声明计时器
  return function() {
    var context = this;//上下文对象
    var args = arguments;//参数
    clearTimeout(timer);//清除计时器
    timer = setTimeout(function () {
      fn.apply(context, args);
    }, delay);
  };
}
```
只要在2500毫秒之内，用户再次击键，就会取消上一次的定时器，然后再新建一个定时器。这样就保证了回调函数之间的调用间隔，至少是2500毫秒。
```
setTimeout(function () {
  console.log(1);
}, 0);
console.log(2);
// 2
// 1
```
上面代码先输出2，再输出1。因为2是同步任务，在本轮事件循环执行，而1是下一轮事件循环执行。

总之，setTimeout(f, 0)这种写法的目的是，尽可能早地执行f，但是并不能保证立刻就执行f。
setTimeout(f, 0)有几个非常重要的用途。它的一大应用是，可以调整事件的发生顺序。比如，网页开发中，某个事件先发生在子元素，然后冒泡到父元素，即子元素的事件回调函数，会早于父元素的事件回调函数触发。如果，想让**父元素的事件回调函数先发生**，就要用到setTimeout(f, 0)。
```
// HTML 代码如下
// <input type="button" id="myButton" value="click">
var input = document.getElementById('myButton');
input.onclick = function A() {
  setTimeout(function B() {
    input.value +=' input';
  }, 0)
};
document.body.onclick = function C() {
  input.value += ' body'
};
```
同步任务：先触发A，再触发C；第二轮事件中，B才会执行；
另一个应用是，**用户自定义的回调函数，通常在浏览器的默认动作之前触发。**比如，用户在输入框输入文本，keypress事件会在浏览器接收文本之前触发。因此，下面的回调函数是达不到目的的。

    // HTML 代码如下
    // <input type="text" id="input-box">
    document.getElementById('input-box').onkeypress = function (event) {
      this.value = this.value.toUpperCase();
    }
上面代码想在用户每次输入文本后，立即将字符转为大写。但是实际上，它只能将本次输入前的字符转为大写，因为浏览器此时还没接收到新的文本，所以this.value取不到最新输入的那个字符。只有用setTimeout改写，上面的代码才能发挥作用。

```
document.getElementById('input-box').onkeypress = function() {
  var self = this;
  setTimeout(function() {
    self.value = self.value.toUpperCase();
  }, 0);
}
```
上面代码将代码放入setTimeout之中，就能使得它在浏览器接收到文本之后触发。
## setTimeout注意点
链接：https://juejin.im/post/5aa4c47af265da239866e236
大家都知道setInterval()和setTimeout()可以接收两个参数，第一个参数是需要回调的函数，必须传入的参数，第二个参数是时间间隔，毫秒数，可以省略。但其实他可以接收更多的参数，从第三个参数开始，依次用来表示传入回调函数的参数。
例子：
setTimeout(function(a,b){
   console.log(0+a+b);//这里打印的是：7
},1000,3,4);

注意：IE 9.0及以下版本，只允许setTimeout有两个参数，不支持更多的参数

setTimeout( function(a,b){}.bind(3,4), 1000 );

