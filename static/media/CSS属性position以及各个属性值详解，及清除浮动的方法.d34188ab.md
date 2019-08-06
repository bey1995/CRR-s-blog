position属性值：
static，absolute，relative，fixed，sticky

> {
>     /* 全局值 */
>     position: inherit;
>     position: initial;
>     position: unset;
>      }

## static
默认值，文档流中的位置。
## absolute
相对于position的值**不为static的第一个祖先元素**来定位。
![图片效果](https://img-blog.csdnimg.cn/20190506161243727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTgzNTk3Nw==,size_16,color_FFFFFF,t_70)
```
<div>1</div>
<div class="two" style="position: absolute;left: 50px;top: 50px;">2</div>
<div>3</div>
</body>
<script type="text/javascript">
</script>
</html>
<style type="text/css">
	div{
		height: 100px;
		width:200px;
		background-color: blue;
		opacity: 0.5;
	}
</style>
```

第二个div位置设为abosulte，所以相对于根元素定位。
## relative
相对于自身原本位置定位。
![图片效果](https://img-blog.csdnimg.cn/20190506151451662.png)
其中第二个div 位置设为relative，相对于自身原来的位置产生了偏移，并且由于未清除浮动，导致第三个div被盖住了。
代码：
```
<body>
<div>1</div>
<div class="two" style="position: relative;left: 10px;top: 50px;">2</div>
<div>3</div>
</body>
<script type="text/javascript">

</script>
</html>
<style type="text/css">
	div{
		height: 100px;
		width:200px;
		background-color: blue;
		opacity: 0.5;
	}
</style>
```

## fixed
相对于浏览器窗口定位。
元素始终占据同样的位置，无论剩余内容是否上下滚动。

## sticky
https://www.cnblogs.com/coco1s/p/6402723.html
粘性定位，结合relative和fixed的特点。
元素先按照普通文档流定位，然后相对于该元素在流中的 flow root（BFC）和 containing block（最近的块级祖先元素）定位。
而后，元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。
这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。

```
{
    position: -webkit-sticky;
    position: sticky;
    top: 0;
}
```
例如以上代码：
因为设定的阈值是 top:0 ，这个值表示当元素距离页面视口（Viewport，也就是fixed定位的参照）顶部距离大于 0px 时，元素以 relative 定位表现，而当元素距离页面视口小于 0px 时，元素表现为 fixed 定位，也就会固定在顶部。
例如：

    div{
    		height: 90px;
    		width: 200px;
    		background-color: blue;
    		opacity: 0.5;
    		position: sticky;
    		top:0;
    		margin-top:30px;
    	}

当元素距离页面顶部距离大于0时，表现为相对定位；
![大于本身高度](https://img-blog.csdnimg.cn/20190506162124922.png)
当第一个盒子距离顶部距离小于0，固定在顶部。
![小于本身高度](https://img-blog.csdnimg.cn/20190506162402294.png)
当第二个盒子距离顶部距离小于0px,固定在顶部。
![图片效果](https://img-blog.csdnimg.cn/20190506162601382.png)
同辈元素定位方式相同，且无z-index设置时，html靠后者居上。

所以靠后的div会遮挡住前面的。
## 清楚浮动
如何清除浮动呢？https://segmentfault.com/a/1190000004865198

> 当容器的高度为auto，且容器的内容中有浮动（float为left或right）的元素，在这种情况下，容器的高度不能自动伸长以适应内容的高度，使得内容溢出到容器外面而影响（甚至破坏）布局的现象。这个现象叫浮动溢出，为了防止这个现象的出现而进行的CSS处理，就叫CSS清除浮动。

一是利用 clear 属性，包括**在浮动元素末尾添加一个带有 clear: both 属性的空 div 来闭合元素**，其实利用 :after 伪元素的方法也是在元素末尾添加一个内容为一个点并带有 clear: both 属性的元素实现的。

二是触发浮动元素父元素的 BFC (Block Formatting Contexts, 块级格式化上下文)，使到该父元素可以包含浮动元素，关于这一点。

在网页主要布局时使用:after伪元素方法并作为主要清理浮动方式；在小模块如ul里使用overflow:hidden;（留意可能产生的隐藏溢出元素问题）；如果本身就是浮动元素则可自动清除内部浮动，无需格外处理；正文中使用邻接元素清理之前的浮动。

最后可以使用相对完美的:after伪元素方法清理浮动，文档结构更加清晰。

1，在浮动元素后添加一个空元素，如div，hr，br

    .clear{
     clear:both;
    }

测试之后发现，hr，br有效果，但是div无效。

2，使用CSS的overflow属性

给浮动元素的容器添加overflow:hidden;或overflow:auto;可以清除浮动，另外在 IE6 中还需要触发 hasLayout ，例如为父元素设置容器宽高或设置 zoom:1。在添加overflow属性后，浮动元素又回到了容器层，把容器高度撑起，达到了清理浮动的效果。
