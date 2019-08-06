经历之前春招的视频面环节，我深知自己在编码方面的不足，决定放弃WebStorm作为日常练习的编码器，选择sublime text作为练习代码的编辑器。

# CH2-升级至HTML5

## HTML5语法

**元素**
在HTML5中，内联元素和块级元素变为区段元素，标题元素，短语元素，嵌入式元素，流元素，元元素以及交互式元素。
使用元素时，要基于语义去选择。
**属性**
所有元素都有属性，属性是典型的键值对，布尔属性的值是可选的（checked，readonly，disabled）
全局属性和国际化属性：
id\class\title\sytle\lang\dir属性在所有元素上支持，HTML5往核心属性列表添加了accesskey，hidden，tabindex属性，同时还有contenteditable，contentmenu，spellcheck，draggable，dropzone共五个交互式属性。
除了全局属性，所有元素还可以有微数据（microdata）属性和自定义数据属性（data-）。
**id**
id属性唯一标识符。
**class**
class类可以共用。
**title**
**style**

**lang**
国际化属性之一。一个Web的首要语言使用<html>元素的lang属性，通过http头的Content-Language或者http-equiv="language"属性进行设置。
使得搜索引擎可以按照内容的语言种类编制索引，允许屏幕阅读器使用国家发音规则。
**dir**
与lang属性经常一起使用，用于更改文本方向，默认值ltr，从左到右。
在HTML5中dir的属性值包括rtl,ltr,**auto**。

**tabindex**
给所有元素添加了可聚焦性，当用户按下tab键时，具有tabindex属性的元素将根据tabindex属性的数字值接收焦点，适合于正值的tabindex。
为了不改变源自于页面源顺序的tab或者键盘焦点的顺序，所以tabindex的值只能是0、-1。
-1用于可编程聚焦。0用于提供可访问性等方便的事由，比如给链接或表单元素提供键盘访问的同时不改变页面的顺序的话。
**accesskey**
把焦点直接移动到触发访问键。
属性值创建了键盘快捷键，例如`<input accesskey="s" name="search" type="text"/>`创建了一个accesskey等于s的搜索框，当用户按下s，焦点聚焦到搜索框。
属性值是一个或多个空格分开的字符。语法与class属性类似，但是字符顺序是有意义的。第一个标记之后的标记被认为是无法支持初始值的用户代理的回退方案。

注意：tabindex由于修改页面原本正常的焦点顺序会产生糟糕的用户体验，accesskey可能会扰乱客户端浏览器设定的默认行为和快捷键。

## HTML5新内容：全局可访问性和交互性属性
**hidden**：该元素尚未或不再适用
**contenteditable**：该元素是否是可编辑的
**contextmenu**：使元素能够与一个提供了更多上下文的`<menu>\<command>`关联起来。该属性把你希望与之关联的menu的id值作为它的值。Chrome支持。
**draggable**：该元素是否是可拖动的，一般与JS事件处理器结合在一起适用，例如dragstart，drag，dragenter，dragleave，dragover，drop，dragend。
**dropzone**：指定在一个元素上可放下什么类型的内容。可以利用move，copy，link属性值分别移动，拷贝或者为其创建一个新的链接。移动设备对拖放支持不足。
**spellcheck**：一个元素是否要进行拼写和语法检查。autocorrect自动更正属性。
**ARIA**：无障碍属性，支持富Internet应用（ARIA）可访问性模块的role，aria-属性。
单独模块，使用实时（arialive属性）区域（live region），角色（role）以及ARIA状态和属性。
WAI-ARIA能够帮助提高动态更新内容和被劫持的元素易访问性。
**data-自定义数据属性**：为了给用户交互提供数据，开发者们一直在编写无效的属性和滥用title和rel属性。
## HTML元素/属性的语法
对于每个元素的开始属性来说，每个属性只能出现一次。
**自闭合/空元素**有：`img、br、meta、hr、base、link、keygen、area、col、command、embed、input、source、track（外部文本轨道）、wbr(单词换行机制）、param（对象参数）`
标记使用小写，所有属性用引号括起来，闭合所有元素，嵌套所有元素，为非布尔型属性提供值，使用语义化元素。
**要素**：5个，构成有效XHTML
1，文档声明或DTD，`<!DOCTYPE html>`
2，HTML根元素，`<html></html>`
3，文档头，HTML的直接儿子：`<head></head>`
4，文档标题，`<title></title>`
5，文档正文，html的直接儿子：`<body></body>`

在html中包含lang方法是一个好的做法，但不是必须。manifest属性，class，都可以包含进去。

```
<html lang="en" manifest="cache.appache" class="no-js">
```

head标签里除了title元素其他通常不显示。

title是必须的，内容应该是对文档综合内容的定义，涉及搜索引擎时，title是最重要的元素。

## 存在于head之中的元素
meta：添加元数据
除了设置字符集charset，每个meta必须包含name，http-equiv，content属性。meta属性的类型是由name或者http-equiv的值定义。
描述meta的标记：name = "description"
关键meta的标记：name = "keyword"

http-equiv替换name的话，能够替换由服务器创建的http响应头。http-equiv="cache-control"

**视口meta标记：viewport**，能够让我们在移动设备上支持浏览器窗口的逻辑尺寸及缩放。
例如：

```
<meta name="viewport" content="user-scalable=no, width=device-width, initial-scale=1.0"/>
```
移动厂商特有的值：**apple-mobile-web-app-capable**，如果把content设置为yes，那么web应用就以全屏模式运行，否则不会。
**app-mobile-web-app-status-bar-style**，可以对状态栏的呈现进行控制，标记颜色和透明度。
IOS下`content="black|default|black-translucent|"`

format-detection，启用或者禁用对网页上可能出现的移动手机号的自动检测。`content="telephone=no"`

base标签在设计本地测试时非常有用
**link标签-不仅用于样式**

```
<link rel="icon" href="/appleicon.png"/>
<link rel="apple-touch-icon" href="/appleicon.png"/>
<link rel="app-touch-startup-image" href="startup.png"/>
<link rel="sheetstyle" href="style.css"/>
```

**link可以有的属性**：rel\href\title\type\size\hreflang\media等
**href**：必选
**media**：被包含的链接适用哪些媒体，**默认all**，可为（screen，print，tty，tv，projection，handheld，braille,aural,all）
同时可使用媒体查询@media

```
<link rel="stylesheet" media="all and (orientation:portait" href="prtrt.css">
<link rel="stylesheet" media="only screen and (max-device-width: 480px)" href="style.css"/>
```
rel：指定的link和当前文档的关系。常用值：

    stylesheet | next、pre、index | help | contents |alternate
    copyright | glossary | icon | apple-touch-icon | apple-touch-startup-image

hreflang：被链接媒体的语言
type：被链接媒体的MIME类型
sizes：新的，如果媒体是个图标。那么定义图标的尺寸。

**style标签**
scoped属性，H5新增，添加则表示渲染代理在该范围查找样式
移动反模式：为了减少等待时间，站点CSS被内嵌包含在主响应的一个或多个style标签内，被JS提取作为localStorage，并把key值添加到cookie字符串。可以减少http请求。
**script标签**
type默认“text/javascript”
建议把脚本放在接近文档末尾的地方。
有两个属性可以改变JS脚本（只对外部脚本有效）的执行顺序：defer（文档解析成功再执行），async（异步-脚本加载成功就立刻执行）。
**用户关闭JavaScript**
noscript
该元素只在用户禁用JavaScript时可见。
**body元素>**
没有元素特有的属性，但是有很多事件处理器。
包括：
```
onafterprint
onbeforeprint
onbeforeonload
onblur
onerror
onfocus
onhashchange
onload
onmessage
ononline
onredo
onresize
sonstorage
onundo
onunload
```
这些事件处理器最好包裹在外部JS中，而不是body元素中。

# CH3-HTML5中的新元素
## HTML5分节元素
body是所有分节元素的根元素。
新增分节元素：

```
section：主题上分组的内容，通常有一个标题
article：就像新的文章
nav
header
footer
aside
```
保持对旧元素的支持：

```
body
h1-h6
address：离它最近的父级元素或者body元素的联系信息
```

![H5网页布局](https://img-blog.csdnimg.cn/20190422203828463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTgzNTk3Nw==,size_16,color_FFFFFF,t_70)
article和section在各自内部可以互相嵌套。

article可以是一篇论坛的帖子，一篇杂志或者是博客文章，用户提交的评论，交互式小挂件，或者其他任何独立的内容项。
section类似于文章的章节。

nav元素-文档内部的导航块
aside元素-与主要内容有点关联的内容，侧边栏，醒目提文之类的。
footer-离它最近父级区段的页脚。

**内容分组，新的HTML5元素**
新的分组元素：

```
main：定义了页面的主要内容，唯一的
figure：即使被删除也不会影响原来内容意图的内容
figcaption：figure的可选标题
hr：段落主题分割，硬回车
```
旧的分组元素：

```
p
pre
blockquote
ol,li,ul,dl,dd,dt,div
```
## HTML5文本级语义化元素
新增：

```
mark：标记与上下文具有相关性的文本
time：定义一个具体的时间和日期，datatime属性提供的是机器可读的时间
ruby：用来显示东亚字符
rt
rp
bdi：隔离一段特定的双向内容，（bdo会覆盖文本的方向）
wbr：内容中的换行机会
data
```
改变了的旧元素：

```
a
small
s
cite
i
b
u
```
无变化的旧元素：

```
q：来自别处的简短引用文本
samp
kbd：用户键盘的输入
sub\sup：下标，上标
bdo
span
br
em
strong
dfn
abbr：缩略
code
var
```
a-超级链接，对于移动设备的一些链接类型，会进行特定处理。
例如：mailto-邮件，tel-电话，sms-打开消息应用。

## 嵌入式元素
新的元素：

```
embed：针对第三方插件
video
audio
source
track
canvas
```

```
已有的：
img：添加了srcset属性，用于基于宽高像素提供备选图像
iframe：改变了，获得了srcdoc，sandbox，seamless属性
object
param：name,value属性
map
area
```
## 交互式元素

```
表单元素
menu：命令或控件列表，menuitem定义一个命令按钮和上下文菜单命令
新增的：
details：包围一个用来披露信息的小挂件，获取补充信息和控件
summary：作为details子元素包含，提供父级details的标题摘要或内容的一个说明点击summary，用户可以隐藏和显示details的内容，这是组合的默认交互
command
```
