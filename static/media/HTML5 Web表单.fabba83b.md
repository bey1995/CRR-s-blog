## 引入
HTML5表单提高了可用性，降低了对JavaScript验证的依赖性。
## input
1，type属性-唯一必需输入属性
默认`type="text"`
使用html5，type属性有23种可能的值，如果不支持一个新输入，默认就是text
2，required属性-标记一个表单字段是必须的
支持required属性的浏览器也支持：required和：invalid伪类，可以通过视觉提醒。
为了提高可访问性，包含required属性，就要包含ARIA属性。

```
<input type="tel" name="phone" required aria-required="true"/>
```
3，min 和 max 属性-设置允许值范围
只能用于日期或时间输入，number或range
例如：

```
<label>Reservation Time:
<input type="time" min="17:00" max="22:00" name="dinner" required>
</label>
```
4，step属性
为用户页面提供微调组件，按照step的值逐渐增加。
range-滑块
number-上下调整数字组件

```
<p>
		<label for="cost_1">Price:</label>
	<input id="cost_1" type="range" min="5" max="100" step="5" name="cost_1"/>
	</p>
	<p>
		<label for="cost_2">Price:</label>
	<input id="cost_2" type="number" min="5" max="100" step="5" name="cost_2" required="required" />
	</p>
```

![range+number](https://img-blog.csdnimg.cn/20190422220647201.png)
4，placeholder属性-占位文本，给用户关于输入的简短提示
一旦开始数据输入，占位文本就消失。
5，pattern属性-哪里允许placeholder，哪里就支持pattern属性。包含一个关于JavaScript的正则表达式。
比如：点击颜色，会弹出选择颜色的小工具。

```
<label for="color">Color:</label>
	<input type="color" name="color" placeholder="#FFFFFF" pattern="#[0-9A-Za-z]{6}"
	id="col" title="a hash sign followed by six hexadecimal digits"/>
	<hr size="1" />
	<label for="cc">Credit Card:</label>
	<input type="text" name="cc" placeholder="credit Card" 
	id="cc" title="13-16 number" pattern="[0-9]{13,16}">
```

![pattern示意图](https://img-blog.csdnimg.cn/20190422221734383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTgzNTk3Nw==,size_16,color_FFFFFF,t_70)
6，readonly属性-表单控件不可编辑
7，disabled属性-禁用一个表单元素，无法得到焦点
8，maxlength-输入最大长度，适用于text，password，url，search，telephone，email，textarea
9，size属性-古老属性
10，form属性-表单控件希望关联的form的id

```
<form id="form1">
		<!--当form1提交的时候，userid将与表单一起发送出去-->
	</form>
	<p>
		<label for="userid">User ID:</label>
		<input type="text" name="userid" form="form1">
	</p>
```
11，autocomplete属性-是否为表单特定字段打开自动完成功能
三个值：on，off，default
12，autofocus属性-指定在页面载入时获得焦点
13，value-显示在文本框中，但不推荐
14，password-密码栏
如果要索要密码，使用SSL的POST方法
14，checkbox-复选框

```
<p>
		<label>Checkbox:</label>
		<input type="checkbox" value="true" name="remember"/>
	</p>
```
其中的name，value将作为键值对被提交。
15，radio-单选按钮
一组单选框，name名相同，只能选择一个。
16，submit-提交按钮
居中显示value属性的按钮
17，reset-重置按钮
18，file-选择文件按钮
19，hidden-隐藏
20，image-接受img的所有属性
21，button-按钮

HTML5为input新增的值：

```
13个值：
search
tel
url
email-支持multiple属性，允许用多个逗号分隔的电子邮件地址
datetime
data
month
week
time
datatime-local：本地时间-month，time，week
number-数字
range-滑动条
color-拾色器
```
## 表单验证
HTML5通过validity引入了8种表单控件：

```
element.validity.valueMissing
element.validity.typeMissmatch
element.validity.patternMissmatch
element.validity.tooLong
element.validity.rangeUnderflow
element.validity.rangeOverflow
element.validity.stepMismatch
element.validity.valid

element.validity.customError
```
UI上可以使用webkit的验证气泡，设计样式例如必须填写的表单控件后面加上`<span class="required">*</span>`
CSS 还有一些伪类：

    input:required
    input:invalid
    input:valid
    input:read-only
    input:checked 

## 新的表单元素
5个新增的表单元素：

```
<datalist>
<output>
<keygen>
<progress>
<meter>
```

**datalist元素以及list属性**
input新增的**list属性，还可以提供给用户一个值的列表**。例如：
datalist与option配合，option是其选项，输入文本中含有option-value值得内容会呈现。
```
<p>
		<label for="userid">User ID:</label>
		<input type="text" name="userid" placeholder="name" 
		required list="animalnames"/>
	</p>
	<datalist id="animalnames">
		<option value="crr">CRR</option>
		<option value="xxd tutou"/>
	</datalist>
```
优雅降级时，可以用select替代option来写。

**output元素**
除了通用属性和事件处理之外，还可以有form，name，for属性，以及onchange，onforminput和onformchange事件。
没有value属性。
使用情况：用户永远不会直接操作它的值，它的可以通过其他值导出。

```
    <output name="score">0</output>
	<output name="level">1</output>
	<output name="time">120</output>
```
**meter元素**-采用浮点数作为值，通常被用作在一个已知范围内提供一个测量结果的测量仪。
代码：

```
    <p>Grade:<meter value="61" min="0" max="100" low="73" high="87">D-</meter>
	<p>Grade:<meter value="74" min="0" max="100" low="73" high="87">D-</meter>
	<p>Grade:<meter value="89" min="0" max="100" low="73" high="87">D-</meter>	
```

![meter示例](https://img-blog.csdnimg.cn/20190423201252102.png)
**progress元素**-用于标识一个目标或者任务的进度
认可value和max属性，正的浮点数。
**form元素**-novallidate属性新增，使得表单提交时不进行原生认证。
**fieldset元素**-第一个可选子元素是legend，用于给fieldset提供组名。
**select元素-**指定一个选择菜单
**textarea元素**-自由形态文本框，wrap属性中soft文本无换行提交，hard显式换行，需设置cols。
button
label
