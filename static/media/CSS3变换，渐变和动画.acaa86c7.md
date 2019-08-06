## CSS渐变-transition
简写属性由：

> transition-property
> transition-duration
> transition-timing-function
> transition-delay

**transition-property**:可以渐变的属性有

> background-color
> background-position
> background-size
> border
> border-color
> border-width
> border-spacing
> box-shadow
> bottom
> clip
> color
> flex
> font
> margin
> opacity
> padding
> perspective
> right
> text-indent
> text-shadow
> top
> transform
> vertical-align
> word-spacing
> ETC

**transition-time-function**:可以取
ease，linear,ease,ease-out,ease-in-out,step-start,step-end,steps(x,start),steps(x,end),cubic-bezier(x,y,x1,y1)

ease=快入
linear=匀速
ease-in=加速
ease-in-out=加速，减速

可以多个属性同时渐变，property以逗号分隔各个属性值。可以多个属性分开渐变。

```
transition: border, color 500ms linear 250ms,
background-color 500ms linear 750ms
```

## CSS3变换-transform
大小，旋转，平移，扭曲，重定位。
transform-origin=设置变化发生的原点，默认值50% 50% 0 ，即x, y,偏移量。
transform：函数有：

> translate()平移，translateX()，translateY ()
> scale(w,h)缩放,scaleX(),scaleY()
> rotate(angle)旋转，rotateX(),rotateY()
> skew(x,y)扭曲，skewX(),skewY()
> matrix()

可以多种变换.

3D变换函数：translate3d(x,y,z)，把元素右移x,下移y，向用户移动z
perspective(p)：获得单个元素的视图效果

## CSS3动画-animation
简写属性有:

> animation-name:name
> animation-duration:0
> animation-timing-function:ease
> animation-delay
> animation-iteration-count:整数，infinite
> animation-direction:交替循环alternate，逆序播放normal
> animation-fill-mode:none,forwards,backwords,both

> animation-play-state:播放状态pause，running

关键帧：

    @keyframes{
    from{
    }
    to{
    }
    //或者
    0%{}
    10%{}
    100%{}
    }

具体动画效果见其他文章：关键词=CSS3动画效果实战
