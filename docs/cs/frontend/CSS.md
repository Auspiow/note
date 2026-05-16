# CSS

> 很久之前的简陋css笔记

### 三种引入方式

内联（在标签内直接写）

```css
<div style="height:500px;width:1000px;background-color: blanchedalmond;"></div>
```

内嵌(在head标签里，加一个style标签，在style里添加样式)

```css
<head>
    <style>
        div{background-color:red;height: 300px;width: 300px;}
    </style>
</head>
```

外联(新建一个.css文件，通过link来引入样式)

```css
css:
	body{background-color: red;}

<body>
<link rel="stylesheet" href="example.css">
</body>
```

CSS 规则由两个主要的部分构成：选择器和声明。每条声明由一个属性和一个值组成：

```css
选择器	 属性 ：值
h1 	{color:orange}
```



## 选择器

#### 命名规则

根据 CSS 规范，CSS 标识符（用来作为类名和 ID 名的字符串）必须遵循以下规则：

- 只能包含字母（`a-z`、`A-Z`）、数字（`0-9`）、连字符（`-`）和下划线（`_`）
- 不能以数字（`0-9`）开头
- 不能连字符后紧跟数字（如 `-1`）开头
- 不能两个连字符（`--`）开头（这种写法被保留专门用于 CSS 自定义属性/变量）

#### id选择器

根据标签的id属性值选择对应的标签的。

```css
<style>
#p1
{text-align:center;color:red;}
</style>

<body>
<p id="p1">Hello World!</p>
</body>
```

#### 元素/标签选择器

根据标签的名称来选择对应的标签的。

```css
<style>
div{color:red;font-size:10px;}
</style>

<body>
<div>Hello World!</div>
</body>
```

#### class类选择器

根据标签的class属性值选择对应的标签的。

```css
<style>
.center{text-align:center;}
</style>

<body>
<h1 class="center">标题居中</h1>
<p class="center">段落居中</p> 
</body>
```

也可以指定特定的HTML元素使用class。
在以下实例中, 所有的 p 元素使用 class="center" 让该元素的文本居中，其他不管用:

```css
<style>
p.center
{
	text-align:center;
}
</style>

<body>
<h1 class="center">不受影响</h1>
<p class="center">段落居中对齐</p> 
</body>
```

#### 群组化选择器
CSS 样式中需要使用到相同的设定时，可以把这几个相同设定的选择器合并在一起

```css
h1 { color:#666666; }
h2 { color:#666666; }
h3 { color:#666666; }
h4 { color:#666666; }
```

群组化之后：

```css
h1, h2, h3, h4 { color:#666666; } 
```

### 层次选择器：

#### 后代选择器

`空格` 选择的是指定标签下的所有对应的后代标签

```html
<style>
    div p {color: red;}
</style>
<body>
	<div>
		<p>text</p> <!-- red -->
		<ul>
			<li><p>text</p></li> <!-- red -->
		</ul>
	</div>
</body>
```

#### 子代选择器

`>` 选取的是指定id（或class或标签元素div等）下包含的子元素

```html
<style>
    div>p {color: red;}
</style>
<body>
	<div>
		<p>text</p> <!-- red -->
		<ul>
			<li><p>text</p></li> <!-- not red -->
		</ul>
	</div>
</body>
```

#### 兄弟选择器
`~` 选择的是当前标签之后的所有同级标签

```html
<style>
    #name~p {background-color: red;}
</style>
<body>
	<p id="name">1</p>
	<p>2</p> <!-- red -->
	<p>3</p> <!-- red -->
</body>
```

#### 相邻选择器
`+` 选择的是当前标签的下一个同级标签

```html
<style>
    #name+p {color: red;}
</style>
<body>
	<p id="name">1</p>
	<p>2</p> <!-- red -->
	<p>3</p> <!-- not red -->
</body>
```

### 伪类选择器

分为状态伪类（鼠标键盘交互），结构伪类（选中某元素），表单伪类（处理输入框）。本文只介绍状态伪类。

#### 状态伪类：

顺序一般使用 “LVHAF” 原则

```css
a:link{}
a:visited{}
a:hover{}
a:active{}
a:focus{}
```

##### link实例

未访问过的样式

```html
<style>
a:link{color:#03c;} /* blue */
</style>

<body>
<ul>
	<li><a href="?">link</a></li>
</ul>
</body>
```

##### visited实例

访问过后的样式

```html
<style>
:link{color:#03c;}
:visited{color:#f00;}  /* red */
</style>

<body>
<ul>
	<li><a href="?">link</a></li>
</ul>
</body>
```

##### hover实例

鼠标悬停的样式

鼠标悬停时字体变红，背景变灰：

```html
<style>
a,div{display:block;margin-top:10px;padding:10px;border:1px solid #ddd;}
a:hover{display:block;background:#ddd;color:#f00;}
div:hover{background:#ddd;color:#f00;}
</style>

<body>
    <a href="?">a</a>
    <div>div</div>
</body>
```

#### active实例

激活的样式

鼠标按住时字体变红，背景变灰：

```html
<style>
h1{font-size:16px;}
a,div{display:block;margin-top:10px;padding:10px;border:1px solid #ddd;}
a:active{display:block;background:#ddd;color:#f00;}
div:active{background:#ddd;color:#f00;}
</style>

<body>
<a href="?">a</a>
<div>div</div>
</body>
```

#### focus实例

设置对象在成为输入焦点（该对象的onfocus事件发生）时的样式。

```html
<style>
h1{font-size:16px;}
ul{list-style:none;margin:0;padding:0;}
input:focus{background:#f6f6f6;color:#f60;border:1px solid #f60;outline:none;}
</style>

<body>
<form action="#">
	<ul>
		<li><input value="姓名" /></li>
		<li><input value="单位" /></li>
	</ul>
</form>
</body>
```

### 优先级

| 优先级级别 | 选择器类型              | 示例                          |
| ---------- | ----------------------- | ----------------------------- |
| 最高特权   | `!important`            | `color: red !important;`      |
| 第一级     | 内联样式 (Inline)       | `<div style="color: red;">`   |
| 第二级     | ID 选择器               | `#header`, `#nav`             |
| 第三级     | 类选择器 (Class)        | `.btn`, `.container`          |
|            | 伪类 (Pseudo-class)     | `:hover`, `:nth-child(2)`     |
|            | 属性选择器 (Attribute)  | `[type="text"]`, `[disabled]` |
| 第四级     | 标签选择器 (Tag)        | `div`, `p`, `h1`              |
|            | 伪元素 (Pseudo-element) | `::before`, `::after`         |
| 无权重     | 通配符、关系符          | `*`, `>`, `+`, `~`            |



## 字体/文本

### 字体常用样式

#### 字体

```css
font-family: "font1","font2";
```

如果在 font-family 属性中定义了多种字体，浏览器中浏览时会由前向后选择字体。

当浏览器不支持“字体 1”时，则会采用“字体 2”，以此类推。如果浏览器不支持font-family定义中的所有字体，则会采用系统默认的字体。

#### 字号
```css
font-size: <size>
```

绝对字体：

```css
font-size: xx-small; x-small; small; medium; large; x-large; xx0large; 
font-size: 16px; /*使用像素值定义字体大小*/
```

相对字体：相对父对象中字体尺寸进行调整

```css
font-size: large /*相对增大*/
font-size: smaller; /*相对减小*/
```

#### 字体风格

字体风格font-style属性用来设置字体是否为斜体。

```css
font-style: italic;
```

| **属性值** | **描述**                     |
| ---------- | ---------------------------- |
| normal     | 默认的正常字体。             |
| italic     | 以斜体显示文字。             |
| oblique    | 属于中间状态，以偏斜体显示。 |

#### 加粗字体
```css
font-weight: 字体粗细值
```

| **属性值** | **描述**                                             |
| ---------- | ---------------------------------------------------- |
| normal     | 表示正常粗细。                                       |
| bold       | 表示粗体。                                           |
| bolder     | 表示更粗（特粗）的字体。                             |
| lighter    | 表示特细体。                                         |
| number     | 不是真正的取值，其范围是 100~900，一般取整百的数字。 |

#### 小写字母转为大写 font-variant

```css
font-variant: 取值
```

| **属性值** | **描述**                                                     |
| ---------- | ------------------------------------------------------------ |
| normal     | 表示正常显示。                                               |
| small-caps | 能将小写的英文字母转化为大写字母且字体较小（小型大写字母）。 |

#### 字体复合属性
可以设置 font 的符合属性，用来简化 CSS 代码。

```css
font: [font-style] [font-variant] [font-weight] [font-size]/[line-height] [font-family];
```

复合属性可以取值字体大小、字体风格、加粗字体等，各值之间用空格相连。

必填项：`font-size`（字号）和 `font-family`（字体族）**必须同时存在**，且必须放在**最后两位**。如果缺少其中任何一个，整行 `font` 属性都将被浏览器忽略。

### 文本常用样式

#### 对齐方式

```css
text-align:center
```

| **属性值** | **描述**                                   |
| ---------- | ------------------------------------------ |
| left       | 把文本排列到左边。默认值：由浏览器决定。   |
| right      | 把文本排列到右边。                         |
| center     | 把文本排列到中间。                         |
| justify    | 实现两端对齐文本效果。                     |
| inherit    | 规定应该从父元素继承 text-align 属性的值。 |

```html
<style>
    h1 {text-align:center}
    h2 {text-align:left}
    h3 {text-align:right}
</style>

<body>
    <h1>text</h1>
    <h2>text</h2>
    <h3>text</h3>
</body>
```

#### 首行缩进
将段落的第一行缩进 50 像素

```html
<style>
    p {text-indent:50px;}
</style>

<body>
    <p>text</p>
</body>
```

#### 文本线

```css
text-decoration: <property>
```

| 属性值                   | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| nonenone                 | 默认。定义标准的文本。默认。定义标准的文本。                 |
| underlineunderline       | 定义文本下的一条线。定义文本下的一条线。                     |
| overlineoverline         | 定义文本上的一条线。定义文本上的一条线。                     |
| line-throughline-through | 定义穿过文本下的一条线。定义穿过文本下的一条线。             |
| blinkblink               | 定义闪烁的文本。定义闪烁的文本。                             |
| inheritinherit           | 规定应该从父元素继承 text-decoration 属性的值。规定应该从父元素继承 text-decoration 属性的值。 |

```html
<style>
    h1 {text-decoration: overline}
    h2 {text-decoration: line-through}
    h3 {text-decoration: underline}
    h4 {text-decoration: blink}
</style>

<body>
    <h1>text1</h1>
    <h2>text2</h2>
    <h3>text3</h3>
    <h4>text4</h4>
</body>
```

#### 字距

增加或减少字符间的空白（字符间距）

```html
<style>
    h1 {letter-spacing: -0.5em}
    h2 {letter-spacing: 20px}
</style>

<body>
    <h1>text1</h1>
    <h2>text2</h2>
</body>
```

#### 词距

```css
word-spacing: 30px
```

增加或减少单词间的空白（即字间隔）

```html
<style>
    p.spread {word-spacing: 30px;}
    p.tight {word-spacing: -0.5em;}
</style>

<body>
    <p class="spread">text1</p>
    <p class="tight">text2</p>
</body>
```

#### 行高

在大多数浏览器中默认行高大约是 110% 到 120%

```css
line-height: 90%
```

```html
<style>
p.small {line-height: 90%}
p.big {line-height: 200%}
</style>
</head>
<body>
    <p>text1</p>
    <p class="small">text2</p>
    <p class="big">text3</p>
</body>
```



## 背景

#### 背景颜色

```html
<style>
    body {background-color: yellow}
    h1 {background-color: #00ff00}
    h2 {background-color: transparent}
    p {background-color: rgb(250,0,255)}
</style>

<body>
    <h1>text1</h1>
    <h2>text2</h2>
    <p>text3</p>
</body>
```

#### 背景图片

```css
<style>
    body {background-image:url(图1.jpg);}
</style>
```

#### 背景铺盖
| **属性值** | **描述**                                            |
| ---------- | --------------------------------------------------- |
| repeat     | 默认。背景图像将在垂直方向和水平方向重复。          |
| repeat-x   | 背景图像将在水平方向重复。                          |
| repeat-y   | 背景图像将在垂直方向重复。                          |
| no-repeat  | 背景图像将仅显示一次。                              |
| inherit    | 规定应该从父元素继承 background-repeat 属性的设置。 |

```css
<style>
	body {background-image: url(图1.jpg);background-repeat: no-repeat}
</style>
```

#### 背景大小

```html
<style>
    body {
        background:url(图1.jpg);
        background-size:63px 100px;
        background-repeat:no-repeat;
        padding-top:80px;
    }
</style>

<body>
    <p>上面是缩小的背景图片。</p>
    <p>原始图片：<img src="图1.jpg" alt="Flowers"></p>
</body>
```

#### 背景定位

设置背景图像的起始位置。

```css
<style>
    body {
        background-image:url('图1.jpg');
        background-repeat:no-repeat;
        background-attachment:fixed;
        background-position:center;
    }
</style>
```

#### 复合样式

把多个背景样式组合起来使用，简单易用

```css
<style>
	body { background: blueviolet url("图1.jpg")  center no-repeat; width: 180px;height: 180px; }
</style>
```



## 盒子模型

### 边框

CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容。

所有的文档元素（标签）都会生成一个矩形框，称为元素框，描述了一个文档元素在网页布局汇所占位置大小。每个盒子除了有自己大小和位置外，还影响着其他盒子的大小和位置。

每个盒子都有：边界、边框、填充、内容 4个属性；
每个属性都包括4个部分：上、右、下、左。属性的4部分可以同时设置，也可以分别设置。

```css
Margin(外边距) - 清除边框外的区域，外边距是透明的。
Border(边框) - 围绕在内边距和内容外的边框。
Padding(内边距) - 清除内容周围的区域，内边距是透明的。
Content(内容) - 盒子的内容，显示文本和图像。
```

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS进阶</title>
  <style>
    div{
      width: 200px;
      height: 200px;
      /*复合样式：边框粗细1px，实线，红色线*/
      /*border: 1px solid red;*/

      /*边框颜色*/
      /*border-color: green;*/

      /*!*边框宽度*!*/
      /*border-width: 5px;*/

      /*!*边框样式*!*/
      /*border-style: solid;*/
      
      /*上边框-：1px 实线 红色*/
      border-top: 1px solid red;
      /*右边框-：2px 虚线 绿色*/
      border-right: 2px dashed green;
      /*下边框-：3px 点线 蓝色*/
      border-bottom: 3px dotted blue;
      /*左边框-：4px 双线 分红色*/
      border-left: 4px double pink;
    }
  </style>
</head>
<body>
<div></div>

</body>
</html>
```

### 内边距

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS进阶</title>
  <style>
    div{
      width: 200px;
      height: 200px;
      border: 1px solid red;

      /*复合样式：可以设置4个值，3个值，2个值，1个值*/
      padding: 50px 60px 70px 80px;

      /*内边距，上下左右，设置属性，会把盒子撑大,不能设置负数，负数就还原初始状态*/
      /*padding-top: 100px;*/
      /*padding-right: 100px;*/
      /*padding-bottom: 100px;*/
      /*padding-left: 100px;*/
    }
  </style>
</head>
<body>
<div>我是内容</div>

</body>
</html>
```

### 外边距

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS进阶</title>
  <style>
    div{
      width: 200px;
      height: 200px;
    }
    .d1{
      border: 1px solid red;

      /*外边距，可以设置负数，不会撑大盒子*/
      /*margin-top: 50px;*/
      /*margin-right: 50px;*/
      /*margin-bottom: 50px;*/
      /*margin-left: 50px;*/

      /*复合样式,可以设置4个值，3个值，2个值，1个值*/
      margin: 50px 60px 70px 80px;
      
      /*融合行内于块级，用通俗的话讲，就是不独占一行的块级元素*/
      display:inline-block;

    }
    .d2{
      border: 2px solid blue;
      display:inline-block;
    }
  </style>
</head>
<body>
<div class="d1"></div>
<div class="d2"></div>
</body>
</html>
```



## 重置样式

每个浏览器都有自带的默认样式，可能导致布局变化，`CSS Reset` 的作用就是重置默认样式，使样式表现一致。

为了让页面获得浏览器跨浏览器的兼容性，需要用重置文件css代码覆盖浏览器默认的样式来统一样式。

Eric Meyer 写的重置样式表：

```css
https://meyerweb.com/eric/tools/css/reset/
```

新建css文件，把网页里面的重置样式复制进去

```css
<link rel="stylesheet" href="../css/reset.css">
```



## 浮动

CSS 的 Float（浮动），会使元素向左或向右移动，其周围的元素也会重新排列。
浮动一般是用于图像，但它在布局时一样非常有用。

元素怎样浮动:
元素的水平方向浮动，意味着元素只能左右移动而不能上下移动。
一个浮动元素会尽量向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。
浮动元素之后的元素将围绕它，之前的元素将不会受到影响。

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS进阶</title>
  <style>
    .div1{
      width: 100px;
      height: 100px;
      background: red;
      /*第一种：右浮动*/
      /*float: right;*/
      /*第二种：左浮动*/
      /*float: left;*/
        
      /*第三种，站一排*/
      float: left;
    }
    .div2{width: 100px;height: 100px;background: pink;
      /*第三种，站一排*/
      float: left;
    }
    .div3{width: 100px;height: 100px;background: blue;
      /*第三种，站一排*/
      float: left;
    }
  </style>
</head>
<body>
  <div>
    <div class="div1"></div>
    <div class="div2"></div>
    <div class="div3"></div>
  </div>
</body>
</html>
```

### 问题：高度坍塌

三种解决办法：

#### 父元素设置

style中添加一个类元素：（然看查看网页检查高度）

```html
<style>
    .div4{
      /*超出部分隐藏*/
      overflow: hidden;
    }
</style>
  
<body>
    <div class="div1"></div>
    <div class="div2"></div>
</body>
```

#### 添加一个空div

布局过多，就需要很多的空盒子，不建议使用

```html
<style>
.div5{width: 100px;height: 100px;}
</style>

<body>
  <div>
    <div class="div1"></div>
    <div class="div2"></div>
    <!--添加一个空盒子-->
    <div class="div4"></div>
  </div>
</body>
```

#### 使用伪元素

原理：给最后一个盒子添加一个元素，把盒子撑起来

```html
<style>
    .div4:after{				/* after:之后 */
        content: " ";			/* content内容 */
        display: block;			/* display显示，block块 */
        clear: both;			/* clear清空，both左右浮动 */
        }
</style>

<body>
  <div class="div">
    <div class="div1"></div>
    <div class="div2"></div>
  </div>
</body>
```

### **定位**

元素可以使用的顶部，底部，左侧和右侧属性定位。然而，这些属性无法工作，除非是先设定position属性。他们也有不同的工作方式，这取决于定位方法。

#### static 定位

HTML 元素的默认值，即没有定位，遵循正常的文档流对象。
静态定位的元素不会受到 top, bottom, left, right影响。

```html
<style>
    .moren{position: static; border: 3px solid #73AD21;}
</style>

<body>
    <div class="moren">text1</div>
</body>
```

#### fixed 定位

元素的位置相对于浏览器窗口是固定位置。

即使窗口是滚动的它也不会移动：

```html
<style>
.guding {
  position:fixed;
  width: 100px;
  height: 100px;
  background:deepskyblue;
  top:30px;
  left: 50px;
}
</style>

<body>
    <div class="guding"></div>
    <div style="width: 100px;height: 100px;border: 1px solid red"></div>
</body>
```

#### relative 定位

相对定位元素的定位是相对其正常位置。

```html
<style>
h2.pos_left
{
	position:relative;
	left:-20px;
}

h2.pos_right
{
	position:relative;
	left:20px;
}
</style>

<body>
<h2>正常位置的标题</h2>
<h2 class="pos_left">这个标题相对于其正常位置向左移动</h2>
<h2 class="pos_right">这个标题相对于其正常位置向右移动</h2>
<p>相对定位会按照元素的原始位置对该元素进行移动。</p>
<p>样式 "left:-20px" 从元素的原始左侧位置减去 20 像素。</p>
<p>样式 "left:20px" 向元素的原始左侧位置增加 20 像素。</p>
</body>
```

**移动相对定位元素，但它原本所占的空间不会改变。**

```css
<style>
h2.pos_top
{
	position:relative;
	top:-50px;
}
</style>

<body>
<h2>这是一个没有定位的标题</h2>
<h2 class="pos_top">这个标题是根据其正常位置向上移动</h2>
<p><b>注意:</b> 即使相对定位元素的内容是移动,预留空间的元素仍保存在正常流动。</p>
</body>
```

**相对定位元素经常被用来作为绝对定位元素的容器块。**

#### absolute 定位

```
绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于<html>:
```

```css
<style>
h2
{
	position:absolute;
	left:100px;
	top:150px;
}
</style>
<body>
<h2>这是一个绝对定位了的标题</h2>
<p>用绝对定位,一个元素可以放在页面上的任何位置。标题下面放置距离左边的页面100 px和距离页面的顶部150 px的元素。.</p>
</body>
```

**absolute 定位使元素的位置与文档流无关，因此不占据空间。absolute 定位的元素和其他元素重叠。**

#### sticky 定位

sticky 英文字面意思是粘，粘贴，所以可以把它称之为粘性定位。

position: sticky; 基于用户的滚动位置来定位。

粘性定位的元素是依赖于用户的滚动，在 position:relative 与 position:fixed 定位之间切换。

它的行为就像 position:relative; 而当页面滚动超出目标区域时，它的表现就像 position:fixed;，它会固定在目标位置。

元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。

这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。

注意: Internet Explorer, Edge 15 及更早 IE 版本不支持 sticky 定位。 Safari 需要使用 -webkit- prefix (查看以下实例)。

```css
<style>
div.sticky {
  position: -webkit-sticky;
  position: sticky;
  top: 0;
  padding: 5px;
  background-color: #cae8ca;
  border: 2px solid #4CAF50;
}
</style>

<body>
<p>尝试滚动页面。</p>
<p>注意: IE/Edge 15 及更早 IE 版本不支持 sticky 属性。</p>
<div class="sticky">我是粘性定位!</div>
<div style="padding-bottom:2000px">
  <p>滚动我</p>
  <p>来回滚动我</p>
  <p>滚动我</p>
  <p>来回滚动我</p>
  <p>滚动我</p>
  <p>来回滚动我</p>
</div>
</body>
```

#### **重叠的元素**

元素的定位与文档流无关，所以它们可以覆盖页面上的其它元素

z-index属性指定了一个元素的堆叠顺序（哪个元素应该放在前面，或后面）

一个元素可以有正数或负数的堆叠顺序：

```css
<style>
img{
	position:absolute;
	left:0px;
	top:0px;
	z-index:-1;
}
</style>

<body>
<h1>这是标题一</h1>
<img src="图片" />
<p>由于图像的 z-index 是 -1，因此它在文本的后面出现。</p>
</body>
```

**具有更高堆叠顺序的元素总是在较低的堆叠顺序元素的前面。**

**注意： 如果两个定位元素重叠，没有指定z - index，最后定位在HTML代码中的元素将被显示在最前面。**

| **属性值** | **描述**                                |
| ---------- | --------------------------------------- |
| auto       | 默认。堆叠顺序与父元素相等。            |
| number     | 设置元素的堆叠顺序。                    |
| inherit    | 规定应该从父元素继承 z-index 属性的值。 |







