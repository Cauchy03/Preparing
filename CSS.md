## 1. 重排(回流)和重绘是什么，有什么区别

> 重排和重绘是浏览器关键渲染路径上的两个节点， 浏览器的关键渲染路径就是 DOM树 和 CSS树 生成渲染树，然后根据渲染树进行布局(layout)来确定页面上内容的大小和位置，最后将像素绘制 (Paint)到屏幕上。
> 其中重排就是当元素的位置发生变动的时候，浏览器重新执行布局这个步骤，来重新确定页面上内容的大小和位置，确定完之后就会进行重新绘制到屏幕上，所以重排一定会导致重绘。导致重排的原因可能是修改页面元素尺寸、位置、字体大小、浏览器窗口等。
> 如果元素位置没有发生变动，仅仅只是样式发生变动，没有影响布局，这个时候浏览器重新渲染的时候会跳过布局步骤，直接进入绘制步骤，这就是重绘，所以重绘不一定会导致重排。

**页面生成的过程：**关键渲染路径

1. HTML 被 HTML 解析器解析成 DOM 树；

2. `CSS  `被 `CSS  `解析器解析成 `CSSDOM  `树；

3. 结合 DOM 树和 `CSSOM` 树，生成一棵渲染树(Render Tree)，这一过程称为 Attachment；

4. 生成布局(flow)，浏览器在屏幕上“画”出渲染树中的所有节点；

5. 将布局绘制(paint)在屏幕上，显示出整个页面。

​	在页面的生命周期中，**网页生成的时候，至少会渲染一次。在用户访问的过程中，还会不断触发重排(`reflow`)和重绘(`repaint`)**，不管页面发生了重绘还是重排，都会影响性能，最可怕的是重排，会使我们付出高额的性能代价，所以我们应尽量避免。



**重排或回流(`reflow`)：**

概念：当DOM的变化影响了元素的几何信息(元素的的位置和尺寸大小)，浏览器需要重新计算元素的几何属性，将其安放在界面中的正确位置，这个过程叫做重排。

重排也叫回流，简单的说就是重新生成布局，重新排列元素

***修改页面元素尺寸、位置、字体大小、浏览器窗口等会引起回流***



**重绘(`Repaints`):**

概念：当一个元素的外观发生改变，但没有改变布局,重新把元素外观绘制出来的过程，叫做重绘。

[重排(`reflow`)和重绘(`repaint`) - 掘金](https://juejin.cn/post/6844904083212468238)

## 2. 介绍下盒子模型

> 盒子模型由四个部分组成，分别是margin、padding、border、content，根据盒子计算方式的不同分为标准盒模型和怪异盒模型，标准盒模型的weight和height只包含了content，而怪异盒模型包含了border、padding、content，可以通过设置box-sizing属性改变元素的盒模型，默认值content-box

盒模型都是由四个部分组成的，分别是 margin、border、padding 和 content，根据盒子的计算方式不同也就是设置height和width，分为了标准盒模型和怪异盒模型

- 标准盒模型的 width 和 height 属性的范围只包含了 content，
- IE 盒模型的 width 和 height 属性的范围包含了 border、padding 和 content。

可以通过修改元素的 box-sizing 属性来改变元素的盒模型：

- `box-sizing: content-box`表示标准盒模型（默认值）
- `box-sizing: border-box`表示 IE 盒模型（怪异盒模型）

## 3. `CSS`选择器有哪些

> css选择器由id选择器，类选择器、伪类选择器、属性选择器，标签选择器、伪元素选择器，后代选择器、子选择器、相邻兄弟选择器、通配符选择器

| **选择器**     | **格式**      | **优先级权重** |
| -------------- | ------------- | -------------- |
| id 选择器      | #id           | 100            |
| 类选择器       | #classname    | 10             |
| 属性选择器     | a[ref=“eee”]  | 10             |
| 伪类选择器     | li:last-child | 10             |
| 标签选择器     | div           | 1              |
| 伪元素选择器   | li::after     | 1              |
| 相邻兄弟选择器 | h1+p          | 0              |
| 子选择器       | ul>li         | 0              |
| 后代选择器     | li a          | 0              |
| 通配符选择器   | \*            | 0              |

## 4. `css`优先级是怎么计算的

> 首先css的第一类是!important，无论引入方式是什么，选择器是什么，它的优先级都是最高的。第二类是引入方式，行内样式优先级高于内部样式和外部样式，而内部样式和外部样式的优先级取决于页面位置顺序，后面的会覆盖前面的。第三类是选择器，选择器的优先级是id>(类|伪类|属性)>(标签|伪元素)>(后代|子|相邻|通配符)。第四类是浏览器默认的样式，优先级最低

```html
<!-- 行内样式 -->
<div style="color:red;">设置文字的颜色为红色</div>
```

```html
<!-- 内部引入 -->
<style type="text/css">
    div{color: red;}
</style>
```

```html
<!-- 外部样式 -->
<link rel="stylesheet" type="text/css" href="css文件路径">
```

对于选择器的**优先级**：

- 标签选择器、伪元素选择器：1
- 类选择器、伪类选择器、属性选择器：10
- id 选择器：100
- 内联样式：1000

**注意事项：**

- !important 声明的样式的优先级最高；
- 如果优先级相同，则最后出现的样式生效；
- 继承得到的样式的优先级最低；
- 通用选择器（\*）、子选择器（>）和相邻同胞选择器（+）并不在这四个等级中，所以它们的权值都为 0 ；
- 样式表的来源不同时，优先级顺序为：行内样式 > 内部样式 > 外部样式 > 浏览器用户自定义样式 > 浏览器默认样式。

`CSS`样式的优先级应该分成几类 -第一类`!important`，无论引入方式是什么，选择器是什么，它的优先级都是最高的。 -第二类引入方式，行内样式的优先级要高于嵌入和外链，嵌入和外链如果使用的选择器相同就看他们在页面中插入的顺序，在后面插入的会覆盖前面的。 -第三类选择器，选择器优先级：id选择器>（类选择器 | 伪类选择器 | 属性选择器 ）> （后代选择器 | 伪元素选择器 ）> （子选择器 | 相邻选择器） > 通配符选择器 。 -第四类浏览器默认样式优先级最低

## 5. `css`和`less`、`sass`的区别 

> sass和less都是css的预处理器,是一种动态样式的语言，可以使用一些变量、函数、继承、运算等，less还支持在服务端上运行。结构更加清晰，而且兼容css代码。

​	他们都是 `CSS `预处理器，是 `CSS `上的一种抽象层。他们是一种特殊的语法/语言编译成 `CSS`。 例如 Less 是一种动态样式语言，将 `CSS `赋予了动态语言的特性，如变量，继承，运算， 函数，LESS 既可以在客户端上运行 (支持 `IE 6+, Webkit, Firefox` )，也可以在服务端运行 (借助 `Node.js`)。

**为什么要使用它们？**

- 结构清晰，便于扩展。 可以方便地屏蔽浏览器私有语法差异。封装对浏览器语法差异的重复处理， 减少无意义的机械劳动。
- 可以轻松实现多重继承。 完全兼容 `CSS `代码，可以方便地应用到老项目中。LESS 只是在 `CSS `语法上做了扩展，所以老的 `CSS `代码也可以与 LESS 代码一同编译。

## 6. 伪类和伪元素区别

> 伪元素是在内容元素前后插入额外的样式或元素，但是这些元素实际上不在文档中生成，只在外部显示可见。伪类是将特殊的效果添加到特定的选择器上，是在已有的元素上添加，不会产生新的元素。

- 伪元素：在内容元素的前后插入额外的元素或样式，但是这些元素实际上并不在文档中生成。它们只在外部显示可见，但不会在文档的源代码中找到它们，因此，称为“伪”元素。例如：

```css
p::before {content:"第一章：";}
p::after {content:"Hot!";}
p::first-line {background:red;}
p::first-letter {font-size:30px;}
```

- 伪类：将特殊的效果添加到特定选择器上。它是已有元素上添加类别的，不会产生新的元素。例如：

```css
a:hover {color: #FF00FF}
p:first-child {color: red}
```

**总结：**伪类是通过在元素选择器上加⼊伪类改变元素状态，⽽伪元素通过对元素的操作进⾏对元素的改变。

## 7. 什么是`BFC`，`BFC`有什么作用，如何形成`BFC`

> BFC是块级格式化上下文，是css的渲染的一部分，是一块独立的渲染区域，不会影响边界外的元素。一般可以通过overflow：hidden、浮动、定位、display:inline-block等创建，可以解决margin重叠、父元素高度塌陷、阻止元素被浮动元素重叠的问题

先来看两个相关的概念：

- Box: Box 是 `CSS `布局的对象和基本单位，⼀个页面是由很多个 Box 组成的，这个 Box 就是我们所说的盒模型。
- Formatting context：块级上下⽂格式化，它是⻚⾯中的⼀块渲染区域，并且有⼀套渲染规则，它决定了其⼦元素将如何定位，以及和其他元素的关系和相互作⽤。

块格式化上下文（Block Formatting Context，`BFC`）是 Web 页面的可视化 `CSS `渲染的一部分，是布局过程中生成块级盒子的区域，也是浮动元素与其他元素的交互限定区域。

通俗来讲：`BFC`是一个独立的布局环境，可以理解为一个容器，在这个容器中按照一定规则进行物品摆放，并且不会影响其它环境中的物品。如果一个元素符合触发 `BFC`的条件，则 `BFC`中的元素布局不受外部影响。**独立的渲染区域，不会影响边界外的元素**

**创建 `BFC`的条件：**

- 根元素：body；
- 元素设置浮动：float 除 none 以外的值；
- 元素设置绝对定位：position (absolute、fixed)；
- display 值为：`inline-block`、table-cell、table-caption、flex 等；
- overflow 值为：hidden、auto、scroll；

**`BFC`的特点：**

- 垂直方向上，自上而下排列，和文档流的排列方式一致。
- 在 `BFC`中上下相邻的两个容器的 margin 会重叠
- 计算 `BFC`的高度时，需要计算浮动元素的高度
- `BFC`区域不会与浮动的容器发生重叠
- `BFC`是独立的容器，容器内部元素不会影响外部元素
- 每个元素的左 margin 值和容器的左 border 相接触

**`BFC`的作用：**

- **解决 margin 的重叠问题**：由于 `BFC`是一个独立的区域，内部的元素和外部的元素互不影响，将两个元素变为两个 `BFC`，就解决了 margin 重叠的问题。
- **解决高度塌陷的问题**：在对子元素设置浮动后，父元素会发生高度塌陷，也就是父元素的高度变为 0。解决这个问题，只需要把父元素变成一个 `BFC`。常用的办法是给父元素设置`overflow:hidden`。
- **阻止元素被浮动元素覆盖**
- **创建自适应两栏布局**：可以用来创建自适应两栏布局：左边的宽度固定，右边的宽度自适应。

```css
.left{
    width: 100px;
    height: 200px;
    background: red;
    float: left;
}
.right{
    height: 300px;
    background: blue;
    overflow: hidden;
}

<div class="left"></div>
<div class="right"></div>
```

左侧设置`float:left`，右侧设置`overflow: hidden`。这样右边就触发了 `BFC`，`BFC`的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠，实现了自适应两栏布局。

## 8. position有哪些值，作用分别是什么

position 有以下属性值：

| 属性值   | 概述                                                         |
| -------- | ------------------------------------------------------------ |
| absolute | 元素会被移出正常文档流，生成绝对定位的元素，相对于 static 定位以外的一个父元素进行定位。元素的位置通过 left、top、right、bottom 属性进行规定。 |
| relative | 生成相对定位的元素，相对于其原来的位置进行定位。元素的位置通过 left、top、right、bottom 属性进行规定。 |
| fixed    | 元素会被移出正常文档流，生成绝对定位的元素，指定元素相对于屏幕视⼝（viewport）的位置来指定元素位置。元素的位置在屏幕滚动时不会改变，⽐如回到顶部的按钮⼀般都是⽤此定位⽅式。 |
| static   | 默认值，没有定位，元素出现在正常的文档流中，会忽略 top, bottom, left, right 或者 z-index 声明，块级元素从上往下纵向排布，⾏级元素从左向右排列。 |
| inherit  | 规定从父元素继承 position 属性的值                           |
| sticky   | 元素根据正常文档流进行定位，粘性定位，动态固定               |

前面三者的定位方式如下：

- **relative：**元素的定位永远是相对于元素自身位置的，和其他元素没关系，也不会影响其他元素。

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603554694939-58dfe7f7-2fc9-45e5-9961-a953f95496a7.png#align=left&display=inline&height=105&margin=%5Bobject%20Object%5D&originHeight=105&originWidth=448&size=0&status=done&style=stroke&width=447)

**fixed：**元素的定位是相对于 window （或者 iframe）边界的，和其他元素没有关系。但是它具有破坏性，会导致其他元素位置的变化。

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603554694841-89472ba9-b236-4098-802f-c3c26ff49466.png#align=left&display=inline&height=117&margin=%5Bobject%20Object%5D&originHeight=135&originWidth=516&size=0&status=done&style=stroke&width=446)

**absolute：**元素的定位相对于前两者要复杂许多。如果为 absolute 设置了 top、left，浏览器会根据什么去确定它的纵向和横向的偏移量呢？答案是浏览器会递归查找该元素的所有父元素，如果找到一个设置了`position:relative/absolute/fixed`的元素，就以该元素为基准定位，如果没找到，就以浏览器边界定位。如下两个图所示：

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603554694882-589670e0-cd52-41d4-a3ed-4ebbdfc88f32.png#align=left&display=inline&height=142&margin=%5Bobject%20Object%5D&originHeight=183&originWidth=576&size=0&status=done&style=stroke&width=446)

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603554694842-2764d9ed-d5fe-45f4-8ede-34a73d237f94.png#align=left&display=inline&height=118&margin=%5Bobject%20Object%5D&originHeight=137&originWidth=516&size=0&status=done&style=stroke&width=446)

## 9. flex布局有什么好处

> flex 布局是 CSS3 新增的一种布局方式，可以通过将一个元素的 display 属性值设置为 flex 从而使它成为一个 flex 容器，它的所有子元素都会成为它的项目。一个容器默认有两条轴：一个是水平的主轴，一个是与主轴垂直的交叉轴。可以使用 flex-direction 来指定主轴的方向。可以使用 justify-content 来指定元素在主轴上的排列方式，使用 align-items 来指定元素在交叉轴上的排列方式。还可以使用 flex-wrap 来规定当一行排列不下时的换行方式。对于容器中的项目，可以使用 order 属性来指定项目的排列顺序，还可以使用 flex-grow 来指定当排列空间有剩余的时候，项目的放大比例，还可以使用 flex-shrink 来指定当排列空间不足时，项目的缩小比例。
>
> 优点：易上手简单灵活性好，根据flex规则很容易达到某个布局效果
> 缺点：兼容较差

Flex 是 FlexibleBox 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。任何一个容器都可以指定为 Flex 布局。行内元素也可以使用 Flex 布局。注意，设为 Flex 布局以后，**子元素的 float、clear 和 vertical-align 属性将失效**。采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis），项目默认沿水平主轴排列。

以下 6 个属性设置在**容器上**：

- flex-direction 属性决定主轴的方向（即项目的排列方向）。
- flex-wrap 属性定义，如果一条轴线排不下，如何换行。
- flex-flow 属性是 flex-direction 属性和 flex-wrap 属性的简写形式，默认值为 row nowrap。
- justify-content 属性定义了项目在主轴上的对齐方式。
- align-items 属性定义项目在交叉轴上如何对齐。
- align-content 属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

以下 6 个属性设置在**项目上(子元素)**：

- order 属性定义项目的排列顺序。数值越小，排列越靠前，默认为 0。
- flex-grow 属性定义项目的放大比例，默认为 0，即如果存在剩余空间，也不放大。
- flex-shrink 属性定义了项目的缩小比例，默认为 1，即如果空间不足，该项目将缩小。
- flex-basis 属性定义了在分配多余空间之前，项目占据的主轴空间。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为 auto，即项目的本来大小。
- flex 属性是 flex-grow，flex-shrink 和 flex-basis 的简写，默认值为 0 1 auto。
- align-self 属性允许单个项目有与其他项目不一样的对齐方式，可覆盖 align-items 属性。默认值为 auto，表示继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch。

## 10. `flex:1`是什么组成的

flex属性设置置了弹性项目（使用了flex布局项目）如何增大或缩小以适应其弹性容器中可用的空间

flex属性由三个简写属性： `flex-grow` `flex-shrink` `flex-basis`

```less
flex:1 
flex-grow:1   flex-shrink:1    flex-basis:0%
```

- flex-grow 属性定义项目的放大比例，默认为 0，即如果存在剩余空间，也不放大。

- flex-shrink 属性定义了项目的缩小比例，默认为 1，即如果空间不足，该项目将缩小。

- flex-basis 属性指定了 flex 元素在主轴方向上的初始大小。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为 auto，即项目的本来大小。

  优先级：max-width/min-width > flex-basis > width > box

  flex-basis的优先级高于width，且在父元素容器不足时会进行调整

## 11. `css`三角形和省略号 

**三角形：**

`CSS `绘制三角形主要用到的是 border 属性，也就是边框。

平时在给盒子设置边框时，往往都设置很窄，就可能误以为边框是由矩形组成的。实际上，border 属性是右三角形组成的，下面看一个例子：

```css
div {
    width: 0;
    height: 0;
    border: 100px solid;
    border-color: orange blue red green;
}
```

将元素的长宽都设置为 0，显示出来的效果是这样的：

![image](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603636245125-d60dca78-0577-4aa1-8809-f1d38594889a.png#align=left&display=inline&height=298&margin=%5Bobject%20Object%5D&originHeight=502&originWidth=1012&size=0&status=done&style=stroke&width=600)

所以可以根据 border 这个特性来绘制三角形：

**（1）三角 1**

```css
div {
    width: 0;
    height: 0;
    border-top: 50px solid red;
    border-right: 50px solid transparent;
    border-left: 50px solid transparent;
}
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603636245420-28c28277-5074-4725-9386-ffc955cf950a.png?x-oss-process=image%2Fresize%2Cw_500#align=left&display=inline&height=132&margin=%5Bobject%20Object%5D&originHeight=162&originWidth=738&size=0&status=done&style=stroke&width=600)

**（2）三角 2**

```css
div {
    width: 0;
    height: 0;
    border-bottom: 50px solid red;
    border-right: 50px solid transparent;
    border-left: 50px solid transparent;
}
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603636245172-36f955bd-075b-442a-b88b-6c084c66ed25.png?x-oss-process=image%2Fresize%2Cw_500#align=left&display=inline&height=133&margin=%5Bobject%20Object%5D&originHeight=140&originWidth=630&size=0&status=done&style=stroke&width=600)

**（3）三角 3**

```css
div {
    width: 0;
    height: 0;
    border-left: 50px solid red;
    border-top: 50px solid transparent;
    border-bottom: 50px solid transparent;
}
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603636245082-84a06746-dfc7-4782-a0d4-27d521aecac7.png?x-oss-process=image%2Fresize%2Cw_500#align=left&display=inline&height=195&margin=%5Bobject%20Object%5D&originHeight=210&originWidth=646&size=0&status=done&style=stroke&width=600)

**（4）三角 4**

```css
div {
    width: 0;
    height: 0;
    border-right: 50px solid red;
    border-top: 50px solid transparent;
    border-bottom: 50px solid transparent;
}
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603636245064-ecd01197-99fc-476e-897f-a4165b55792a.png?x-oss-process=image%2Fresize%2Cw_500#align=left&display=inline&height=191&margin=%5Bobject%20Object%5D&originHeight=220&originWidth=690&size=0&status=done&style=stroke&width=600)

**（5）三角 5**

```css
div {
    width: 0;
    height: 0;
    border-top: 100px solid red;
    border-right: 100px solid transparent;
}
```

![img](https://cdn.nlark.com/yuque/0/2020/png/1500604/1603636245095-5b40d585-7e68-4bb0-aaef-6c8fce7d36b8.png?x-oss-process=image%2Fresize%2Cw_500#align=left&display=inline&height=177&margin=%5Bobject%20Object%5D&originHeight=218&originWidth=738&size=0&status=done&style=stroke&width=600)

还有很多，就不一一实现了，总体的原则就是通过上下左右边框来控制三角形的方向，用边框的宽度比来控制三角形的角度。



**省略：**

- 单行文本溢出

```css
overflow: hidden;            // 溢出隐藏
text-overflow: ellipsis;      // 溢出用省略号显示
white-space: nowrap;         // 规定段落中的文本不进行换行
```

- 多行文本溢出

```css
overflow: hidden;            // 溢出隐藏
text-overflow: ellipsis;     // 溢出用省略号显示
display:-webkit-box;         // 作为弹性伸缩盒子模型显示
-webkit-box-orient:vertical; // 设置伸缩盒子的子元素排列方式：从上到下垂直排列
-webkit-line-clamp:3;        // 显示的行数
```

注意：由于上面的三个属性都是 `CSS3 `的属性，没有浏览器可以兼容，所以要在前面加一个`-webkit-` 来兼容一部分浏览器。

## 12. 怎么在浏览器可视区域画一个最大的正方形

- 用 `padding-bottom:100%` 撑起来，`padding-top: 100%;`也同理
- 由于margin, padding 的百分比数值是相对父元素的宽度计算的，只需将元素垂直方向的一个padding值设定为与width相同的百分比就可以制作出自适应正方形了

```css
.box{
    width: 100%;
    padding-top: 100%;
    background-color: pink;
}
```

## 13. 怎么在浏览器可视区域画一个自适应的正方形

- 利用 vw 来实现：

```css
.square {
  width: 10%;
  height: 10vw;
  background: tomato;
}
```

- 利用元素的 margin/padding 百分比是相对父元素 width 的性质来实现：

由于margin, padding 的百分比数值是相对父元素的宽度计算的，只需将元素垂直方向的一个padding值设定为与width相同的百分比就可以制作出自适应正方形了。

但要注意，仅仅设置padding-bottom是不够的，若向容器添加内容，内容会占据一定高度，为了解决这个问题，需要设置height: 0。

```css
.square {
  width: 20%;
  height: 0;
  padding-top: 20%;
  background: orange;
}
```

- 利用子元素的 margin-top 的值来实现：

```css
.square {
  width: 30%;
  overflow: hidden;
  background: yellow;
}
.square::after {
  content: '';
  display: block;
  margin-top: 100%;
}
```

## 14. `CSS3`有哪些新特性

> 新增css选择器，圆角，文字阴影，文字渲染，渐变，媒体查询，flex布局，transform属性等

- 新增各种 `CSS `选择器 （: not(.input)：所有 class 不是“input”的节点）
- 圆角 （border-radius:8px）
- 多列布局 （multi-column layout）
- 阴影和反射 （Shadoweflect）
- 文字阴影 （text-shadow）
- 文字渲染 （Text-decoration）
- 线性渐变 （gradient）
- 旋转 （transform）
- 增加了旋转,缩放,定位,倾斜,动画,多背景
- flex布局

## 15. 如何设置一个0.5像素的线 

- **采用 transform: scale()的方式**，该方法用来定义元素的 2D 缩放转换：

```css
transform: scaleY(0.5);
```

- **采用 meta viewport 的方式**

```html
<meta name="viewport" content="width=device-width, initial-scale=0.5, minimum-scale=0.5, maximum-scale=0.5"/>	
```

这样就能缩放到原来的 0.5 倍，如果是 1px 那么就会变成 0.5px。viewport 只针对于移动端，只在移动端上才能看到效果

## 16. 如何在浏览器上显示一个`5px`的文本？

现实开发中会遇到设计稿的字体大小小于 12px,但是大多数默认浏览器的最小字体是 12px,这种情况怎么办？

```css
transform: scale(0.7);
```

但是开发过程中会发现，会导致一些 margin 和 padding不准确，所以需设置 `transform-origin: left top`

```css
transform: scale(0.7);
transform-origin: left top
```

## 17. 垂直水平居中

- 利用绝对定位，先将元素的左上角通过 top:50%和 left:50%定位到页面的中心，然后再通过 translate 来调整元素的中心点到页面的中心。该方法需要**考虑浏览器兼容问题。**

```css
.parent {
    position: relative;
}

.child {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
}
```

- 利用绝对定位，设置四个方向的值都为 0，并将 margin 设置为 auto，由于宽高固定，因此对应方向实现平分，可以实现水平和垂直方向上的居中。该方法适用于**盒子有宽高**的情况：

```css
.parent {
    position: relative;
}

.child {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
}
```

- 利用绝对定位，先将元素的左上角通过 top:50%和 left:50%定位到页面的中心，然后再通过 margin 负值来调整元素的中心点到页面的中心。该方法适用于**盒子宽高已知**的情况

```css
.parent {
    position: relative;
}

.child {
    position: absolute;
    top: 50%;
    left: 50%;
    margin-top: -50px;     /* 自身 height 的一半 */
    margin-left: -50px;    /* 自身 width 的一半 */
}
```

- 使用 flex 布局，通过 align-items:center 和 justify-content:center 设置容器的垂直和水平方向上为居中对齐，然后它的子元素也可以实现垂直和水平的居中。该方法要**考虑兼容的问题**，该方法在移动端用的较多：

```css
.parent {
    height: 100vh; /* 设置高度 避免align-items失效 */
    display: flex;
    justify-content:center;
    align-items:center;
}
```

## 18. 浮动元素会造成什么影响，如何清除浮动

> 浮动主要是指脱离文档标准流，不占据空间，碰到包含它或者其他浮动元素会停留。如果子元素设置浮动，父元素没有设置高度，那么父元素的高度就无法被撑开，造成一个高度塌陷的问题，影响布局就必须清除浮动。清除浮动的方式：第一个给父元素设置高度，第二个在最后一个浮动元素后添加一个空的div标签并且设置clear:both，第三个给父元素设置overflow:hidden，第四个伪类或者伪元素清除浮动

**浮动的定义：** 非 IE 浏览器下，容器不设高度且子元素浮动时，容器高度不能被内容撑开。 此时，内容会溢出到容器外面而影响布局。这种现象被称为浮动（溢出）。

**标准文档流：**

文档流指的是元素排版布局过程中，元素会**默认**自动从左往后，从上往下的流式排列方式。

即不对页面进行任何布局控制时，浏览器默认的HTML布局方式，这种布局方式从左往右，从上往下，有点像流水的效果，我们称为`流式布局`。

**浮动的工作原理：**

- 浮动元素脱离文档流，不占据空间（引起“高度塌陷”现象）
- 浮动元素碰到包含它的边框或者其他浮动元素的边框停留

浮动元素可以左右移动，直到遇到另一个浮动元素或者遇到它外边缘的包含框。浮动框不属于文档流中的普通流，当元素浮动之后，不会影响块级元素的布局，只会影响内联元素布局。此时文档流中的普通流就会表现得该浮动框不存在一样的布局模式。当包含框的高度小于浮动框的时候，此时就会出现“高度塌陷”。

**浮动元素引起的问题？**

- 父元素的高度无法被撑开，影响与父元素同级的元素
- 与浮动元素同级的非浮动元素会跟随其后
- 若浮动的元素不是第一个元素，则该元素之前的元素也要浮动，否则会影响页面的显示结构

**清除浮动的方式如下：**

- 给父级 div 定义`height`属性
- 最后一个浮动元素之后添加一个空的 div 标签，并添加`clear:both`样式
- 包含浮动元素的父级标签添加`overflow:hidden`或者`overflow:auto`
- 使用 :after 伪元素。由于 IE6-7 不支持 :after，使用 zoom:1 触发 hasLayout

```css
.clearfix:after{
    content: '';
    display: block;
    height: 0;
    clear: both;
}
.clearfix{
    *zoom: 1;
}
```

**使用clear清除浮动原理：**

使用 clear 属性清除浮动，其语法如下：

```css
clear:none|left|right|both
```

如果单看字面意思，clear:left 是“清除左浮动”，clear:right 是“清除右浮动”，实际上，这种解释是有问题的，因为浮动一直还在，并没有清除。

官方对 clear 属性解释：“**元素盒子的边不能和前面的浮动元素相邻**”，对元素设置 clear 属性是为了避免浮动元素对该元素的影响，而不是清除掉浮动。

还需要注意 clear 属性指的是元素盒子的边不能和前面的浮动元素相邻，注意这里“**前面的**”3 个字，也就是 clear 属性对“后面的”浮动元素是不闻不问的。考虑到 float 属性要么是 left，要么是 right，不可能同时存在，同时由于 clear 属性对“后面的”浮动元素不闻不问，因此，当 clear:left 有效的时候，clear:right 必定无效，也就是此时 clear:left 等同于设置 clear:both；同样地，clear:right 如果有效也是等同于设置 clear:both。由此可见，clear:left 和 clear:right 这两个声明就没有任何使用的价值，至少在 CSS 世界中是如此，直接使用 clear:both 吧。

一般使用伪元素的方式清除浮动：

```css
.clear::after{
  content:'';
  display: block;
  clear:both;
}
```

clear 属性只有块级元素才有效的，而::after 等伪元素默认都是内联水平，这就是借助伪元素清除浮动影响时需要设置 display 属性值的原因。

## 19. 行内元素、块级元素有哪些，区别是什么

**块级：会独占一行，多个元素会另起一行，可以设置 width、height、margin 和 padding 属性；**

**行内：元素不会独占一行，设置 width、height 属性无效。但可以设置水平方向的 margin 和 padding 属性，不能设置垂直方向的 padding 和 margin；**

1、常见的行内元素

```
<span>` `<a>` `<lable>` `<strong>` `<b>` `<small>` `<abbr>` `<button>` `<input>` `<textarea>` `<select>` `<code>` `<img>` `<br>` `<q>` `<i>` `<cite>` `<var>` `<kbd>` `<sub>` `<bdo>
```

2、常见的块级元素

```
<div> <p> <li> <h1> <h2> <h3> <h4> <h5> <h6> <form> <header> <hr> <ol> <address> <article> <aside> <audio> <canvas> <dd> <dl> <fieldset> <section> <ul> <video>
```

**区别：**

**1、默认情况下，行内元素不会以新的一行开始，而块级元素会新起一行。**

**2、块级元素可以设置 width, height属性，注意：块级元素即使设置了宽度，仍然是独占一行的。**

**而行内元素设置width, height无效。**

**3、块级元素可以设置margin 和 padding。**

**行内元素的水平方向的padding-left,padding-right,margin-left,margin-right 都产生边距效果，但是竖直方向的padding-top,padding-bottom,margin-top,margin-bottom都不会产生边距效果。（也就是水平方向有效，竖直方向无效）**

**4、块级元素可以包含行内元素和块级元素。行内元素不能包含块级元素**

比如我们想在`<span>`标签内包含`<div>`标签是不被允许的。而在`<div>`标签中包含`<span>`标签是经常看到的。

## 20. `diaplay:none;visibility:hidden;opacity:0`

- **display: none**：渲染树不会包含该渲染对象，因此该元素不会在页面中占据位置，也不会响应绑定的监听事件。
- **visibility: hidden**：元素在页面中仍占据空间，但是不会响应绑定的监听事件。
- **opacity: 0**：将元素的透明度设置为 0，以此来实现元素的隐藏。元素在页面中仍然占据空间，并且能够响应元素绑定的监听事件。
- **z-index: 负值**：来使其他元素遮盖住该元素，以此来实现隐藏。
- **transform: scale(0,0)**：将元素缩放为 0，来实现元素的隐藏。这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。

## 21. `css `动画 (transition、animation)

> 动画指过渡动画和自定义动画，过渡动画强调过渡，一般需要触发一个事件才执行，设置一个开始关键帧一个结束关键帧。自定义动画不需要触发事件，自己执行且可以循环通过@keyframes可设置多个关键帧
>

- **transition 是过度属性**，强调过度，它的实现需要触发一个事件（比如鼠标移动上去，焦点，点击等）才执行动画。它类似于 flash 的补间动画，设置一个开始关键帧，一个结束关键帧。

  | 属性                       | 含义                      |
  | -------------------------- | ------------------------- |
  | transition-property        | 指定使用过渡效果的css属性 |
  | transition-duration        | 设置过渡动画持续时间      |
  | transition-timing-function | 设置动画的时间函数。      |
  | transition-delay           | 设置动画的延迟时间        |

- **animation 是动画属性**，它的实现不需要触发事件，设定好时间之后可以自己执行，且可以循环一个动画。它也类似于 flash 的补间动画，但是它可以设置多个关键帧（用@keyframe 定义）完成动画。

- 通过`@keyframes`自定义关键帧动画并为动画命名，可以在其中对每一帧进行设置。

- 使用自定义动画的元素，需要通过`animation`相关属性进行配置

- - animation-name
  - animation-duration
  - animation-timing-function
  - animation-delay
  - animation-iteration-count
  - animation-direction
  - animation-fill-mode
  - animation-play-state

## 22. rem与em的区别 vw、vh是什么

> px表示一个个相同大小的像素点，em，rem是一个相对长度，em是相对父元素的font-size的值，而rem是相对HTML根元素的。vw，vh是根据可视化窗口宽度的百分占比，100vw就是满宽。它和百分比的区别就是，百分比是根据父元素，而vw，vh是根据可视化区域

传统的项目开发中，我们只会用到`px`、`%`、`em`这几个单位，它可以适用于大部分的项目开发，且拥有比较良好的兼容性

从`CSS3`开始，浏览器对计量单位的支持又提升到了另外一个境界，新增了`rem`、`vh`、`vw`、`vm`等一些新的计量单位

在`css`单位中，可以分为长度单位、绝对单位，如下表所指示

| CSS单位      |                                        |
| ------------ | -------------------------------------- |
| 相对长度单位 | em、ex、ch、rem、vw、vh、vmin、vmax、% |
| 绝对长度单位 | cm、mm、in、px、pt、pc                 |

### px

px，表示像素，所谓像素就是呈现在我们显示器上的一个个小点，每个像素点都是大小等同的，所以像素为计量单位被分在了绝对长度单位中

有些人会把`px`认为是相对长度，原因在于在移动端中存在设备像素比，`px`实际显示的大小是不确定的

这里之所以认为`px`为绝对单位，在于`px`的大小和元素的其他属性无关

### [#](https://vue3js.cn/interview/css/em_px_rem_vh_vw.html#em)em

em是相对长度单位。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸（`1em = 16px`）

为了简化 `font-size` 的换算，我们需要在`css`中的 `body` 选择器中声明`font-size`= `62.5%`，这就使 em 值变为 `16px*62.5% = 10px`

这样 `12px = 1.2em`, `10px = 1em`, 也就是说只需要将你的原来的`px` 数值除以 10，然后换上 `em`作为单位就行了

特点：

- em 的值并不是固定的
- em 会继承父级元素的字体大小
- em 是相对长度单位。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸
- 任意浏览器的默认字体高都是 16px

举个例子

```html
<div class="big">
    我是14px=1.4rem<div class="small">我是12px=1.2rem</div>
</div>
```

样式为

```css
<style>
    html {font-size: 10px;  } /*  公式16px*62.5%=10px  */  
    .big{font-size: 1.4rem}
    .small{font-size: 1.2rem}
</style>
```

这时候`.big`元素的`font-size`为14px，而`.small`元素的`font-size`为12px

### [#](https://vue3js.cn/interview/css/em_px_rem_vh_vw.html#rem)rem

rem，相对单位，相对的只是HTML根元素`font-size`的值

同理，如果想要简化`font-size`的转化，我们可以在根元素`html`中加入`font-size: 62.5%`

```css
html {font-size: 62.5%;  } /*  公式16px*62.5%=10px  */ 
```

1

这样页面中1rem=10px、1.2rem=12px、1.4rem=14px、1.6rem=16px;使得视觉、使用、书写都得到了极大的帮助

特点：

- rem单位可谓集相对大小和绝对大小的优点于一身
- 和em不同的是rem总是相对于根元素，而不像em一样使用级联的方式来计算尺寸

### [#](https://vue3js.cn/interview/css/em_px_rem_vh_vw.html#vh、vw)vh、vw

vw ，就是根据窗口的宽度，分成100等份，100vw就表示满宽，50vw就表示一半宽。（vw 始终是针对窗口的宽），同理，`vh`则为窗口的高度

这里的窗口分成几种情况：

- 在桌面端，指的是浏览器的可视区域
- 移动端指的就是布局视口

像`vw`、`vh`，比较容易混淆的一个单位是`%`，不过百分比宽泛的讲是相对于父元素：

- 对于普通定位元素就是我们理解的父元素
- 对于position: absolute;的元素是相对于已定位的父元素
- 对于position: fixed;的元素是相对于 ViewPort（可视窗口）

## 23. link与@import区别与选择

> link与@import引入css的方式，link是一个标签除了引入css之外，还可以设置一些rel、Rss连接属性，而@import只能加载css。link引入css的时候，页面载入时同时加载，而@import只能等到页面加载完在进行加载，还有就是兼容性的问题

两者都是外部引用 CSS 的方式，它们的区别如下：

- link 是 XHTML 标签，除了加载 CSS 外，还可以定义 RSS、rel 连接属性等；@import 属于 CSS 范畴，只能加载 CSS。
- link 引用 CSS 时，在页面载入时同时加载；@import 需要页面网页完全载入以后加载。
- link 是 XHTML 标签，无兼容问题；@import 是在 CSS2.1 提出的，低版本的浏览器不支持。
- link 支持使用 Javascript 控制 DOM 去改变样式；而@import 不支持。

```
1、从属关系区别
    @import是 CSS 提供的语法规则，只有导入样式表的作用；link是HTML提供的标签，不仅可以加载 CSS 文件，还可以定义 RSS、rel 连接属性等。
2、加载顺序区别
    加载页面时，link标签引入的 CSS 被同时加载；@import引入的 CSS 将在页面加载完毕后被加载。
3、兼容性区别
    @import是 CSS2.1 才有的语法，故只可在 IE5+ 才能识别；link标签作为 HTML 元素，不存在兼容性问题。
4、DOM可控性区别
    可以通过 JS 操作 DOM ，插入link标签来改变样式；由于 DOM 方法是基于文档的，无法使用@import的方式插入样式。
    document.styleSheets; //styleSheets返回的是link标签的数组对象，
5、权重区别
    link引入的样式权重大于@import引入的样式。（相同的类名或者标签名，而不是含有！important之类的才生效）
```

## 24. CSS相关的性能优化

**加载性能：**

（1）css 压缩：将写好的 css 进行打包压缩，可以减小文件体积。

（2）css 单一样式：当需要下边距和左边距的时候，很多时候会选择使用 margin:top 0 bottom 0；但 margin-bottom:bottom;margin-left:left;执行效率会更高。

（3）减少使用@import，建议使用 link，因为后者在页面加载时一起加载，前者是等待页面加载完成之后再进行加载。

**选择器性能：**

（1）关键选择器（key selector）。选择器的最后面的部分为关键选择器（即用来匹配目标元素的部分）。CSS 选择符是从右到左进行匹配的。当使用后代选择器的时候，浏览器会遍历所有子元素来确定是否是指定的元素等等；

（2）如果规则拥有 ID 选择器作为其关键选择器，则不要为规则增加标签。过滤掉无关的规则（这样样式系统就不会浪费时间去匹配它们了）。

（3）避免使用通配规则，如\*{}计算次数惊人，只对需要用到的元素进行选择。

（4）尽量少的去对标签进行选择，而是用 class。

（5）尽量少的去使用后代选择器，降低选择器的权重值。后代选择器的开销是最高的，尽量将选择器的深度降到最低，最高不要超过三层，更多的使用类来关联每一个标签元素。

（6）了解哪些属性是可以通过继承而来的，然后避免对这些属性重复指定规则。

**渲染性能：**

（1）慎重使用高性能属性：浮动、定位。

（2）尽量减少页面重排、重绘。

（3）去除空规则：｛｝。空规则的产生原因一般来说是为了预留样式。去除这些空规则无疑能减少 css 文档体积。

（4）属性值为 0 时，不加单位。

（5）属性值为浮动小数 0.\*\*，可以省略小数点之前的 0。

（6）标准化各种浏览器前缀：带浏览器前缀的在前。标准属性在后。

（7）不使用@import 前缀，它会影响 css 的加载速度。

（8）选择器优化嵌套，尽量避免层级过深。

（9）css 雪碧图，同一页面相近部分的小图标，方便使用，减少页面的请求次数，但是同时图片本身会变大，使用时，优劣考虑清楚，再使用。

（10）正确使用 display 的属性，由于 display 的作用，某些样式组合会无效，徒增样式体积的同时也影响解析性能。

（11）不滥用 web 字体。对于中文网站来说 WebFonts 可能很陌生，国外却很流行。web fonts 通常体积庞大，而且一些浏览器在下载 web fonts 时会阻塞页面渲染损伤性能。

**可维护性、健壮性：**

（1）将具有相同属性的样式抽离出来，整合并通过 class 在页面中进行使用，提高 css 的可维护性。

（2）样式与内容分离：将 css 代码定义到外部 css 中。

## 25. 两栏布局的实现

> 两栏布局主要是左边定宽，右边宽度自适应，可以通过浮动，左边盒子设置浮动宽度随便，右边盒子设置margin-left大小为左边盒子的宽度，也可以设置overflow:hidden触发BFC，还可以通过绝对定位和flex布局

一般两栏布局指的是**左边一栏宽度固定，右边一栏宽度自适应**，两栏布局的具体实现：

- 利用浮动，将左边元素宽度设置为 200px，并且设置向左浮动。将右边元素的 margin-left 设置为 200px，宽度设置为 auto（默认为 auto，撑满整个父元素）。

```css
.left {
  float: left;
  width: 200px;
  height: 100px;
  background: tomato;
}
.right {
  margin-left: 200px;
  width: auto;
  height: 100px;
  background: gold;
}
```

- 利用浮动，左侧元素设置固定大小，并左浮动，右侧元素设置 overflow: hidden; 这样右边就触发了 BFC，BFC 的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠。

```css
.left{
     width: 100px;
     height: 200px;
     background: red;
     float: left;
 }
 .right{
     height: 300px;
     background: blue;
     overflow: hidden;
 }
```

- 利用 flex 布局，将左边元素设置为固定宽度 200px，将右边的元素设置为 flex:1。

```css
.outer {
  display: flex;
  height: 100px;
}
.left {
  width: 200px;
  background: tomato;
}
.right {
  flex: 1;
  background: gold;
}
```

- 利用绝对定位，将父级元素设置为相对定位。左边元素设置为 absolute 定位，并且宽度设置为 200px。将右边元素的 margin-left 的值设置为 200px。

```css
.outer {
  position: relative;
  height: 100px;
}
.left {
  position: absolute;
  width: 200px;
  height: 100px;
  background: tomato;
}
.right {
  margin-left: 200px;
  background: gold;
}
```

- 利用绝对定位，将父级元素设置为相对定位。左边元素宽度设置为 200px，右边元素设置为绝对定位，左边定位为 200px，其余方向定位为 0。

```css
.outer {
  position: relative;
  height: 100px;
}
.left {
  width: 200px;
  background: tomato;
}
.right {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 200px;
  background: gold;
}
```

## 26. 三栏布局的实现

> 三栏布局主要是左右盒子定宽，中间盒子自适应，可以通过浮动，绝对定位，flex布局，圣杯布局，双飞翼布局。圣杯布局主要利用的浮动和负边距，先将三个盒子放到一个父元素中，中间的盒子放在最前面，父元素设置左右padding边距,然后将三个盒子全部浮动，中间盒子宽度设置100%，左右盒子被挤到下一行然后通过margin负值移到上一行，最后通过定位，定位到两边。双飞翼布局相对于圣杯布局来说，左右位置的保留是通过中间盒子的margin，而不是父元素的padding实现，本质也是浮动和负边距实现，主要是在中间盒子再嵌套一个盒子

三栏布局一般指的是页面中一共有三栏，**左右两栏宽度固定，中间自适应的布局**，三栏布局的具体实现：

- 利用**绝对定位**，左右两栏设置为绝对定位，中间设置对应方向大小的 margin 的值。

```css
.outer {
  position: relative;
  height: 100px;
}

.left {
  position: absolute;
  width: 100px;
  height: 100px;
  background: tomato;
}

.right {
  position: absolute;
  top: 0;
  right: 0;
  width: 200px;
  height: 100px;
  background: gold;
}

.center {
  margin-left: 100px;
  margin-right: 200px;
  height: 100px;
  background: lightgreen;
}
```

- 利用 flex 布局，左右两栏设置固定大小，中间一栏设置为 flex:1。

```css
.outer {
  display: flex;
  height: 100px;
}

.left {
  width: 100px;
  background: tomato;
}

.right {
  width: 100px;
  background: gold;
}

.center {
  flex: 1;
  background: lightgreen;
}
```

- 利用浮动，左右两栏设置固定大小，并设置对应方向的浮动。中间一栏设置左右两个方向的 margin 值，注意这种方式**，中间一栏必须放到最后：**

```css
.outer {
  height: 100px;
}

.left {
  float: left;
  width: 100px;
  height: 100px;
  background: tomato;
}

.right {
  float: right;
  width: 200px;
  height: 100px;
  background: gold;
}

.center {
  height: 100px;
  margin-left: 100px;
  margin-right: 200px;
  background: lightgreen;
}
```

- 圣杯布局，利用浮动和负边距来实现。三个元素放在同一个父级元素中，代表中间盒子的元素放在最前面，父级盒子设置左右`padding`，三个盒子全部浮动，设置中间盒子宽度100%，因此后面两个盒子都被挤到了下一行，通过设置 margin 负值(左边margin-left:-100%，右边margin-left:-自身宽度)将其移动到上一行，再利用相对定位，定位到两边。

```css
.outer {
  height: 100px;
  padding-left: 100px;
  padding-right: 200px;
}

.left {
  position: relative;
  left: -100px;
  float: left;
  margin-left: -100%;
  width: 100px;
  height: 100px;
  background: tomato;
}

.right {
  position: relative;
  left: 200px;
  float: right;
  margin-left: -200px;
  width: 200px;
  height: 100px;
  background: gold;
}

.center {
  float: left;
  width: 100%;
  height: 100px;
  background: lightgreen;
}
```

```html
<div class="outer">
    <div class="center"></div>
    <div class="left">左</div>
    <div class="right">右</div>
</div>
```

- 双飞翼布局，双飞翼布局相对于圣杯布局来说，左右位置的保留是通过中间列的 margin 值来实现的，而不是通过父元素的 padding 来实现的。本质上 来说，也是通过浮动和外边距负值来实现的。

  三个盒子对应三个元素，其中中间盒子套了两层，中间盒子内部盒子设置`margin`,三个盒子全部浮动，设置中间盒子宽度100%，左右盒子设置固定宽度,设置左边盒子左边距-100%,右边盒子设置右边距-自身宽度

```css
.left {
  float: left;
  margin-left: -100%;
  width: 100px;
  height: 100px;
  background: tomato;
}

.right {
  float: left;
  margin-left: -200px;
  width: 200px;
  height: 100px;
  background: gold;
}

.wrapper {
  float: left;
  width: 100%;
  height: 100px;
  background: lightgreen;
}

.center {
  margin-left: 100px;
  margin-right: 200px;
  height: 100px;
}
```

```html
<div class="wrapper">
    <div class="center"></div>
</div>
<div class="left">左</div>
<div class="right">右</div>
```

## 27. 渲染合成层是什么

## 28. css3怎么开启硬件加速(GPU加速)

## 29. 让浏览器立即进行渲染更新的方法

## 30. RAF和RIC是什么

## 31. 图片的预加载和懒加载

[超详细的图片预加载和懒加载教程。分析应用场景。大量插图。完整的代码。累死我了 - 掘金 (juejin.cn)](https://juejin.cn/post/7004001535206260749)

## 32. rgba和opacity的区别

> 这两个属性都是设置元素透明度的，opacity取值范围是0到1之间，0表示透明，1表示不透明，rgba其中R表示red G表示绿色 B表示蓝色 a表示透明度 可以是百分数也可是整数，也是0到1之间。区别在于继承性，opacity可以继承父元素，rgba后代不会继承

## 鼠标事件

> mouseover和mouseout：当鼠标移入移出元素或子元素都会触发事件。（支持冒泡）
> mouseenter和mouseleave：当鼠标移入移出元素才会触发事件。（不支持冒泡）
> hover的效果等同于mouseenter，mouseleave。

