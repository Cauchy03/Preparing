## 1. MVVM、MVC、MVP分别用于什么场景，区别是什么

```
MVVM、MVC、MVP是三种常见的软件架构模式，MVC是将应用抽象为数据层model、视图层view、逻辑层controller，降低了项目的耦合，但MVC并未限制数据流，Model和View之间仍可以通信；MVP与MVC的区别在于 Presenter 和 Controller，它实现了Model和View的解耦，让两个模块更加独立，易于维护和开发；MVVM则是对MVP的改造，用ViewModel代替Presenter，ViewModel负责View和Model之间通信，Model和ViewModel之间存在双向数据绑定，数据更新时自动同步到视图。
```

```
MVC适用于需要将业务逻辑和数据处理分开的应用程序
MVP适用于需要将逻辑和视图分开的应用程序
MVVM适用于需要大量数据绑定的应用程序
```

## 2. vue 父子组件传值有哪些方法

1. props
2. $emit（自定义事件、全局事件总线 ）
3. $attrs（获取props未定义的属性）、$listeners（包含该组件所有的监听事件）
4. provide/inject（主要是跨级组件间通信，在vue2中不是响应式，可以通过传入响应式数据比如当前this，或者函数式的方式实现响应式
5. v-model
6. 插槽
7. vuex

## 3. vue开发中常用的指令有哪些

```
v-bind：用于绑定 HTML 属性到 Vue 实例的数据上。
v-model：用于实现双向数据绑定，将表单元素的值与 Vue 实例的数据进行同步。
v-for：用于循环渲染列表数据。
v-if、v-else、v-else-if：用于条件性渲染元素。
v-show：用于根据条件显示或隐藏元素。
v-on：用于绑定事件处理函数。
v-html：用于输出 HTML 标记。
v-text：用于输出文本。

除了以上这些，Vue.js 还提供了很多其他的指令，如 v-cloak、v-pre、v-once 等等。指令可以使 HTML 具有更强的动态性和交互性，让开发者更加便捷地构建 Web 应用程序。
```

## 4. vue的组件加载和渲染顺序（生命周期中创建和挂载阶段）

```
vue生命周期是用来描述一个组件从创建到销毁的过程，主要分为8个钩子函数，对应4个阶段分别是创建、挂载、更新、销毁。
```

1. `beforeCreate:`通常用于插件开发中执行一些初始化任务
2. `created:`实例创建完成，可访问data、computed、watch、method，但未挂载DOM不能访问el属性
3. `beforeMount:`挂载开始之前被调用,render函数首次被调用
4. `mounted:`实例挂载到DOM上，可以通过API访问DOM节点，可以进行ajax交互
5. `beforeUpdate:`响应式数据更新时调用，此时虽然响应式数据更新了，但是对应的真实 DOM 还没有被渲染
6. `Updated:`虚拟DOM重新渲染和打补丁，可执行依赖DOM的操作
7. `beforeDestroy:`实例销毁之前调用，`this` 仍能获取到实例
8. `destroyed:`实例销毁，解绑所有事件与指令

## vue 子组件和父组件执行顺序

```
创建过程自上而下，挂载过程自下而上，即:
parent created
child created
child mounted
parent mounted

Vue创建是一个递归过程，先创建父组件，有子组件就创建子组件，因此创建阶段先有父组件再有子组件；子组件首次创建会添加到父组件mounted钩子队列中，等到patch结束再执行它们，可见子组件的mounted钩子也是先进入到队列中的，因此等到patch结束子组件mounted钩子也就先执行
```

**加载渲染过程：**

1.父组件 beforeCreate

2.父组件 created

3.父组件 beforeMount

4.子组件 beforeCreate

5.子组件 created

6.子组件 beforeMount

7.子组件 mounted

8.父组件 mounted

**更新过程：**

1.父组件 beforeUpdate

2.子组件 beforeUpdate

3.子组件 updated

4.父组件 updated

**销毁过程：**

1.父组件 beforeDestroy

2.子组件 beforeDestroy

3.子组件 destroyed

4.父组件 destoryed

## 5. vue的数据绑定机制是如何实现的   

```
vue是采用数据劫持和发布订阅模式，通过Object.defineProperty()来劫持data对象中各个属性的getter和setter，在数据发生变动时发布消息给订阅者，触发相应的监听回调。Vue3则是借助Proxy实现的
```

```
发布-订阅模式（Publish-Subscribe Pattern）是一种常见的软件设计模式，用于解耦发布者和订阅者之间的关系。一种对象间一对多的依赖关系，当一个对象状态发生改变，依赖它的对象都会收到状态改变的通知

在发布-订阅模式中，发布者（也叫做主题）维护一个订阅者列表，订阅者可以订阅主题，也可以取消订阅。当主题的状态发生变化时，它会通知所有的订阅者，订阅者会收到通知并执行相应的操作。
这种模式可以帮助我们实现松耦合的设计，让发布者和订阅者之间解除直接的依赖关系，让它们能够独立地演化，从而更容易实现可维护性和可扩展性。
```

首先要对数据进行`劫持监听`，所以我们需要设置一个监听器 `Observer`，用来监听所有属性。如果属性发上变化了，就需要告诉订阅者 `Watcher` 看是否需要更新。

因为订阅者是有很多个，所以我们需要有一个消息订阅器 Dep 来专门收集这些订阅者，然后在监听器 `Observer` 和订阅者 `Watcher` 之间进行统一管理的。

接着，我们还需要有一个指令解析器 `Compile`，对每个节点元素进行扫描和解析，将相关指令对应初始化成一个订阅者 `Watcher`，并替换模板数据或者绑定相应的函数，此时当订阅者 `Watcher` 接收到相应属性的变化，就会执行对应的更新函数，从而更新视图。

**因此接下去我们执行以下 3 个步骤，实现数据的双向绑定：**

- 实现一个监听器 `Observer`，用来劫持并监听所有属性，如果有变动的，就通知订阅者。
- 实现一个订阅者 `Watcher`，可以收到属性的变化通知并执行相应的函数，从而更新视图。
- 实现一个解析器 `Compile`，可以扫描和解析每个节点的相关指令，并根据初始化模板数据以及初始化相应的订阅器。

## 6. vue的computed和watch的区别

```
computed计算属性主要是依赖其它属性返回一个新的属性，且有缓存机制只有当依赖的属性值发生改变，下一次计算属性才会更新。watch监听器主要是观察，没有缓存性，监听的数据发生变化就会执行相应的回调操作。
```

## 7. 说下vue的keep alive

```
keep alive是一个内置组件，它不会出现在父组件链中，也不会出现在dom元素中，当包裹一个动态组件的时候，该组件会被缓存到内存当中，而不是销毁，内置提供一些钩子函数，可以让我们组件缓存和激活时执行一些额外的逻辑，当组件被换掉时，会被缓存到内存当中、触发deactivated生命周期，当组件被切回来时，再去缓存里找这个组件、触发activated生命周期，主要避免在组件切换时重复渲染和销毁。
```

## 8. v-model 的原理

```
v-model是一个语法糖，他可以用在表单元素上，也可用在组件实例上，实际上通过v-bind绑定一个数据v-on监听表单value变化。
<input v-model="sth" />
//  等同于
<input 
    v-bind:value="message" 
    v-on:input="message=$event.target.value"
>
//$event 指代当前触发的事件对象;
//$event.target 指代当前触发的事件对象的dom;
//$event.target.value 就是当前dom的value值;
//在@input方法中，value => sth;
//在:value中,sth => value;
```

## 9. vue $nextTick实现原理

```
当data中一个属性发生改变，视图并不会立即同步渲染，当监听到数据发生变化后，vue将开启一个队列，并缓冲同一事件循环中发生的所有数据变更。在下一个事件循环tick中，vue刷新队列并执行相应操作。

nextTick是将回调延迟到下一次DOM更新循环之后执行，也是利用原生JS的promise、MutationObserver、setImmediate、setTimeout方法模拟实现对应的微任务和宏任务，通过这些异步回调任务队列来实现vue框架中自己的异步回调队列

可以将多次数据更新合并成一次，减少操作DOM的次数，频繁的操作DOM更新很耗性能
```

## vue 为什么要使用虚拟DOM

```
虚拟DOM是对真实DOM的一种抽象，用JavaScript对象描述DOM的层次结构。DOM中的一切属性都在虚拟DOM中有对应的属性。
```

```
虚拟DOM的解析过程：
首先对将要插到文档中的DOM树结构进行分析，使用js对象表示出来比如一个元素对象，包含标签、属性、子元素，然后将DOM片段插到文档中，当页面状态发生改变，首先变更状态重新构建对象树，与旧的对象树进行比较，最后将有差异的部分应用到真实的DOM树中，视图就进行更新
```

## 10. vue diff算法原理

```
实质是调用patch函数，比较新旧节点，一边比较一边给真是DOM打补丁

在新老虚拟DOM对比时：首先对比节点本身，判断是否为同一级节点，如果不为相同节点，则删除该节点重新创建节点进行替换，如果为相同节点，进行patchVnode，判断该节点的子节点，如果新节点没有子节点直接删除旧的子节点，如果都有子节点，进行updateChildren，然后对新旧节点的子节点进行操作，也就是diff的核心。整个diff算法主要来讲就是只对同级的子节点比较，不跨级比较，不同类型直接替换，同类型比较属性和子节点。

而vue3中采用了Patch Flag的diff算法，它会标记每个节点需要更新的类型，进行diff比较的时候直接定位需要更新的节点，而不必再对整颗树进行完整的比较，可以更快的更新页面，提升性能。
```

## 11. key的作用

```
key是为了优化diff算法的性能，实际开发中有两个地方使用了key，v-for、transition-group：
v-for使用key时，vue的diff会根据虚拟dom的type和key判断新旧节点，vnode是否可复用，如果不加key就会导致在列表更新时造成性能浪费（不能用index作为key，因为删除中间的列表项，后面的key会更新，不能作为列表项的唯一标识）
transition-group使用key，vue会精确的知道哪个元素需要添加过渡效果，不加就会导致过渡效果的混乱
```

## 12. vue mixin解决什么问题 原理 缺点

```
mixin通常来讲就是混入，本质是一个对象，当多个组件或页面有相同的配置,可以通过mixin抽离公共代码,达到复用效果。
当在一个组件使用mixin时，vue会将mixin中的选项与组件选项进行合并，比如数据对象，methods对象，生命周期钩子等，如果有重复的属性以组件的选项为准。
在执行钩子函数时，首先执行mixin里的再执行组件里的钩子函数

缺点：命名冲突，滥用的话后期很难维护，排查问题困难
```

## 13. vue3有哪些改变

```
首先vue3采用了组合式API（Composition API）的方式替代vue2配置式API；然后是数据监听机制的改变，vue2是通过Object.defineProperty实现，vue3则基于代理proxy实现；还有生命周期的改变，总体来讲变化不大只是在大部分钩子名称前加一个'on'，而且需要提前引入；vue3可以多根节点，还提供teleport组件和Suspense异步组件，更好的支持typescript，通过tree-shaking优化了打包后的体积等。
```

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29414776/1658110268214-b9a20581-ae3b-4cee-b55d-d4777b0393c8.png?x-oss-process=image%2Fresize%2Cw_926%2Climit_0)

## 6. 讲一下Proxy对象

```
Proxy是创建一个对象的代理，用于在目标对象之前设置一层拦截，从而可以对目标对象的访问进行过滤和改写，它包含两个参数，一个目标对象，一个拦截器对象，当使用Proxy对象访问目标对象属性或方法时，拦截器对象会介入访问过程进行一些处理
```

## vue3为什么要用proxy

```
先谈Object.defineProperty的缺点
proxy是代理整个对象而非对象属性，不需要用$set或者$delete触发响应式
```

## 14. 谈一下对vuex的理解

```
vuex就是一个状态管理模式，因为单向数据流的规范，如果多个组件中要使用祖先组件的一些数据，会变得特别的繁琐，并且兄弟组件之间共享数据也会非常繁琐。这就导致我们需要用vuex管理组件共享的状态，以一个全局单例模式来管理

State就是全局共享的状态
Getter是类似于Vue的computed来将状态经过处理后返回
Mutation用于同步修改State中的状态
Action则是用于管理异步请求，并在获取到状态之后将要改变的状态分发到mutation中
Module则是将全局的状态分模块进行管理
```

## vue的路由模式

```
hash模式：路径中会有一个#，#后面的路径叫hash值，不会随着http请求发送给服务器
history模式：需要和后端配合，不然刷新会造成路径与服务器不匹配，获取不到资源
```

## 15. vue-router有哪几种导航钩子

```
路由是一种对页面跳转和页面状态管理的技术。通过监测浏览器路由（url）变化，渲染不同的页面内容
优点：前后端分离，页面切换无刷新（ajax）、动态路由
缺点：首次加载慢、SEO不友好、浏览器兼容
```

```
全局路由钩子：router.beforeEach（前置守卫）、beforeResolve（全局解析守卫）、router.afterEach（后置守卫）
单个路由独享守卫：beforeEnter
组件内钩子：beforeRouteEnter（进入组件前）、beforeRouteUpdate（路由跳转组件被复用跳转）、beforeRouteLeave（离开组件时）
```

## 16. data为什么写成函数形式

```
在 Vue 中组件是可以复用的，一个组件被创建好之后，就可以被用在其他各个地方，而组件不管被复用了多少次，组件中的 data 数据应该是相互不影响的。基于数据不影响的理念，组件被复用一次，data 数据就应该被复制一次，data 是函数，每一个函数都会有自己的存储空间，函数每次执行都会创建自己的执行上下文，相互不影响。函数类似于给每个组件实例创建一个私有的数据空间，让各个组件实例维护各自的数据。而单纯的写成对象形式，就使得所有组件实例共用了一份 data，就会造成一个变了全都会变的结果。
```

## 17. Vue template 到render的过程

```
首先会调用Parse方法对template解析生成AST抽象语法树，然后对静态节点做优化，深度遍历对静态节点做标记，为后续更新渲染可以直接跳过静态节点做优化，然后调用generate将抽象语法树编译成render字符串，再通过new Function(render)生成render函数
```

```
// 调用parse方法将template转化为ast（抽象语法树）
const ast = parse(template.trim(), options)
// 对静态节点做优化
optimize(ast,options)
// 生成代码
const code = generate(ast, options)
```

## 18. SPA单页面理解，优缺点

```
SPA仅在页面初始化加载相应的HTML、CSS、JS。一旦加载完，SPA不会因为用户的操作而进行页面的重新加载或跳转，而是利用路由机制实现HTML内容的变换
优点：用户体验更好、前后端分离、开发效率高、良好的扩展性
缺点：首屏加载时间较长、SEO不友好、路由管理较复杂
```

### **key的原理**

vue中的key有什么作用？（key的内部原理）

了解vue中key的原理需要一些前置知识。

就是vue的虚拟dom，vue会根据 data中的数据生成虚拟dom，如果是第一次生成页面，就将虚拟dom转成真实dom，在页面展示出来。

虚拟dom有啥用？每次vm._data 中的数据更改，都会触发生成新的虚拟dom，新的虚拟dom会跟旧的虚拟dom进行比较，如果有相同的，在生成真实dom时，这部分相同的就不需要重新生成，只需要将两者之间不同的dom转换成真实dom，再与原来的真实dom进行拼接。我的理解是虚拟dom就是起到了一个dom复用的作用，还有避免重复多余的操作，下文有详细解释。



而key有啥用？

key是虚拟dom的标识。



先来点预备的知识：啥是真实 DOM？真实 DOM 和 虚拟 DOM 有啥区别？如何用代码展现真实 DOM 和 虚拟 DOM

#### 真实`DOM`和其解析流程

这里参考超级英雄大佬：https://juejin.cn/post/6844903895467032589

`webkit` 渲染引擎工作流程图

![img](https://img-blog.csdnimg.cn/img_convert/b32d88931ee775d57b382d7585de3ad8.png)

> 中文版

![img](https://img-blog.csdnimg.cn/img_convert/cd1757feee540ef20c50b81af16d75ca.png)



 所有的浏览器渲染引擎工作流程大致分为5步：创建 `DOM` 树 —> 创建 `Style Rules` -> 构建 `Render` 树 —> 布局 `Layout` -—> 绘制 `Painting`。

* 第一步，构建 DOM 树：当浏览器接收到来自服务器响应的HTML文档后，会遍历文档节点，生成DOM树。需要注意的是在DOM树生成的过程中有可能会被CSS和JS的加载执行阻塞，渲染阻塞下面会讲到。

* 第二步，生成样式表：用 CSS 分析器，分析 CSS 文件和元素上的 inline 样式，生成页面的样式表；

* 渲染阻塞：当浏览器遇到一个script标签时，DOM构建将暂停，直到脚本加载执行，然后继续构建DOM树。每次去执行Javascript脚本都会严重阻塞DOM树构建，如果JavaScript脚本还操作了CSSOM，而正好这个CSSOM没有下载和构建，那么浏览器甚至会延迟脚本执行和构建DOM，直到这个CSSOM的下载和构建。所以，script标签引入很重要，实际使用时可以遵循下面两个原则：

  * css优先：引入顺序上，css资源先于js资源

  * js后置：js代码放在底部，且js应尽量少影响DOM构建

    > 还有一个小知识：当解析html时，会把新来的元素插入dom树里，同时去查找css，然后把对应的样式规则应用到元素上，查找样式表是按照从右到左的顺序匹配的例如：div p {...}，会先寻找所有p标签并判断它的父标签是否为div之后才决定要不要采用这个样式渲染。所以平时写css尽量用class或者id，不要过度层叠

* 第三步，构建渲染树：通过DOM树和CSS规则我们可以构建渲染树。浏览器会从DOM树根节点开始遍历每个可见节点(注意是可见节点)对每个可见节点，找到其适配的CSS规则并应用。渲染树构建完后，每个节点都是可见节点并且都含有其内容和对应的规则的样式。这也是渲染树和DOM树最大的区别所在。渲染是用于显示，那些不可见的元素就不会在这棵树出现了。除此以外，display none的元素也不会被显示在这棵树里。visibility hidden的元素会出现在这棵树里。

* 第四步，**渲染布局**：布局阶段会从渲染树的根节点开始遍历，然后确定每个节点对象在页面上的确切大小与位置，布局阶段的输出是一个盒子模型，它会精确地捕获每个元素在屏幕内的确切位置与大小。

* 第五步，**渲染树绘制**：在绘制阶段，遍历渲染树，调用渲染器的paint()方法在屏幕上显示其内容。渲染树的绘制工作是由浏览器的UI后端组件完成的。

**注意点：**

**1、`DOM` 树的构建是文档加载完成开始的？** 构建 `DOM` 树是一个渐进过程，为达到更好的用户体验，渲染引擎会尽快将内容显示在屏幕上，它不必等到整个 `HTML` 文档解析完成之后才开始构建 `render` 树和布局。

**2、`Render` 树是 `DOM` 树和 `CSS` 样式表构建完毕后才开始构建的？** 这三个过程在实际进行的时候并不是完全独立的，而是会有交叉，会一边加载，一边解析，以及一边渲染。

**3、`CSS` 的解析注意点？** `CSS` 的解析是从右往左逆向解析的，嵌套标签越多，解析越慢。

**4、`JS` 操作真实 `DOM` 的代价？**传统DOM结构操作方式对性能的影响很大，原因是频繁操作DOM结构操作会引起页面的重排(reflow)和重绘(repaint)，浏览器不得不频繁地计算布局，重新排列和绘制页面元素，导致浏览器产生巨大的性能开销。直接操作真实`DOM`的性能特别差，我们可以来演示一遍。

```js
<div id="app"></div>
<script>
    // 获取 DIV 元素
    let box = document.querySelector('#app');
    console.log(box);

    // 真实 DOM 操作
    console.time('a');
    for (let i = 0; i <= 10000; i++) {
        box.innerHTML = i;
    }
    console.timeEnd('a');

    // 虚拟 DOM 操作
    let num = 0;
    console.time('b');
    for (let i = 0; i <= 10000; i++) {
        num = i;
    }
    box.innerHTML = num;
    console.timeEnd('b');

</script>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/0740cb16f1354358bed6ef638b90ebfe.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5qC86Zu354uQ5oCd,size_19,color_FFFFFF,t_70,g_se,x_16)


>从结果中可以看出，操作真实 DOM 的性能是非常差的，所以我们要尽可能的复用，减少 DOM 操作。





#### **虚拟 DOM 的好处**

​	虚拟 `DOM` 就是为了解决浏览器性能问题而被设计出来的。如前，若一次操作中有 10 次更新 `DOM` 的动作，虚拟 `DOM` 不会立即操作 `DOM`，而是将这 10 次更新的 `diff` 内容保存到本地一个 `JS` 对象中，最终将这个 `JS` 对象一次性 `attch` 到 `DOM` 树上，再进行后续操作，避免大量无谓的计算量。所以，用 `JS` 对象模拟 `DOM` 节点的好处是，页面的更新可以先全部反映在 `JS` 对象(虚拟 `DOM` )上，操作内存中的 `JS` 对象的速度显然要更快，等更新完成后，再将最终的 `JS` 对象映射成真实的 `DOM`，交由浏览器去绘制。

​	虽然这一个虚拟 DOM 带来的一个优势，但并不是全部。虚拟 DOM 最大的优势在于抽象了原本的渲染过程，实现了跨平台的能力，而不仅仅局限于浏览器的 DOM，可以是安卓和 IOS 的原生组件，可以是近期很火热的小程序，也可以是各种GUI。

​	回到最开始的问题，虚拟 DOM 到底是什么，说简单点，就是一个普通的 JavaScript 对象，包含了 `tag`、`props`、`children` 三个属性。

> 接下来我们手动实现下 虚拟 DOM。
>
> 分两种实现方式：
>
> 一种原生 js DOM 操作实现；
>
> 另一种主流虚拟 DOM 库（snabbdom、virtual-dom）的实现（用h函数渲染）（暂时还不理解）



**算法实现**

**（1）**用 JS 对象模拟 DOM 树：

```html
<div id="virtual-dom">
    <p>Virtual DOM</p>
    <ul id="list">
      <li class="item">Item 1</li>
      <li class="item">Item 2</li>
      <li class="item">Item 3</li>
    </ul>
    <div>Hello World</div>
</div> 
```

我们用 `JavaScript` 对象来表示 `DOM` 节点，使用对象的属性记录节点的类型、属性、子节点等。

```js
/**
 * Element virdual-dom 对象定义
 * @param {String} tagName - dom 元素名称
 * @param {Object} props - dom 属性
 * @param {Array<Element|String>} - 子节点
 */
function Element(tagName, props, children) {
    this.tagName = tagName;
    this.props = props;
    this.children = children;
    // dom 元素的 key 值，用作唯一标识符
    if (props.key) {
        this.key = props.key
    }
}
function el(tagName, props, children) {
    return new Element(tagName, props, children);
}
```

构建虚拟的  `DOM`  ，用 javascript 对象来表示

```js
let ul = el('div', { id: 'Virtual DOM' }, [
    el('p', {}, ['Virtual DOM']),
    el('ul', { id: 'list' }, [
        el('li', { class: 'item' }, ['Item 1']),
        el('li', { class: 'item' }, ['Item 2']),
        el('li', { class: 'item' }, ['Item 3'])
    ]),
    el('div', {}, ['Hello, World'])
])
```

现在 `ul` 就是我们用 `JavaScript` 对象表示的 `DOM` 结构，我们输出查看 `ul` 对应的数据结构如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/9fb2a6336a61477c8ea39677716d1f52.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5qC86Zu354uQ5oCd,size_20,color_FFFFFF,t_70,g_se,x_16)


**（2）**将用 js 对象表示的虚拟 DOM 转换成真实 DOM：需要用到 js 原生操作 DOM 的方法。

```js
/**
 * render 将virdual-dom 对象渲染为实际 DOM 元素
 */
Element.prototype.render = function () {
    // 创建节点
    let el = document.createElement(this.tagName);

    let props = this.props;
    // 设置节点的 DOM 属性
    for (let propName in props) {
        let propValue = props[propName];
        el.setAttribute(propName, propValue)
    }

    let children = this.children || []
    for (let child of children) {
        let childEl = (child instanceof Element)
        ? child.render() // 如果子节点也是虚拟 DOM, 递归构建 DOM 节点
        : document.createTextNode(child) // 如果是文本，就构建文本节点

        el.appendChild(childEl);
    }

    return el;
}
```

我们通过查看以上 `render` 方法，会根据 `tagName` 构建一个真正的 `DOM` 节点，然后设置这个节点的属性，最后递归地把自己的子节点也构建起来。

我们将构建好的 `DOM` 结构添加到页面 `body` 上面，如下：

```js
let ulRoot = ul.render();
document.body.appendChild(ulRoot);
```

这样，页面 `body` 里面就有真正的 `DOM` 结构，效果如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/41bf566f214f4c1f943b9a3dddfc6f19.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5qC86Zu354uQ5oCd,size_19,color_FFFFFF,t_70,g_se,x_16)




> 我们知道虚拟 DOM 的好处和虚拟 DOM 的实现后就要讲讲 key 的作用了。
>
> 贴一下上面实现地完整代码

```html
<script>
    /**
         * Element virdual-dom 对象定义
         * @param {String} tagName - dom 元素名称
         * @param {Object} props - dom 属性
         * @param {Array<Element|String>} - 子节点
         */
    function Element(tagName, props, children) {
        this.tagName = tagName;
        this.props = props;
        this.children = children;
        // dom 元素的 key 值，用作唯一标识符
        if (props.key) {
            this.key = props.key
        }
    }

    function el(tagName, props, children) {
        return new Element(tagName, props, children);
    }

    let ul = el('div', { id: 'Virtual DOM' }, [
        el('p', {}, ['Virtual DOM']),
        el('ul', { id: 'list' }, [
            el('li', { class: 'item' }, ['Item 1']),
            el('li', { class: 'item' }, ['Item 2']),
            el('li', { class: 'item' }, ['Item 3'])
        ]),
        el('div', {}, ['Hello, World'])
    ])

    /**
             * render 将virdual-dom 对象渲染为实际 DOM 元素
             */
    Element.prototype.render = function () {
        // 创建节点
        let el = document.createElement(this.tagName);

        let props = this.props;
        // 设置节点的 DOM 属性
        for (let propName in props) {
            let propValue = props[propName];
            el.setAttribute(propName, propValue)
        }

        let children = this.children || []
        for (let child of children) {
            let childEl = (child instanceof Element)
            ? child.render() // 如果子节点也是虚拟 DOM, 递归构建 DOM 节点
            : document.createTextNode(child) // 如果是文本，就构建文本节点

            el.appendChild(childEl);
        }

        return el;
    }

    let ulRoot = ul.render();
    document.body.appendChild(ulRoot);
    console.log(ul);
</script>
```







#### **虚拟DOM中key的作用**

key是虚拟DOM对象的标识，当数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】, 随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：

* 旧虚拟DOM中找到了与新虚拟DOM相同的key：
  * ①.若虚拟DOM中内容没变, 直接使用之前的真实DOM！
  * ②.若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM。
* 旧虚拟DOM中未找到与新虚拟DOM相同的key
  * 创建新的真实DOM，随后渲染到到页面。
