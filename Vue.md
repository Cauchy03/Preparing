## MVVM、MVC、MVP分别用于什么场景，区别是什么

> MVVM、MVC、MVP是三种常见的软件架构模式，MVC是将应用抽象为数据层model、视图层view、逻辑层controller，所有通信都是单项的，降低了项目的耦合，但MVC并未限制数据流，Model和View之间仍可以通信；
> MVP与MVC的区别在于 Presenter 和 Controller，它实现了Model和View的解耦，让两个模块更加独立，易于维护和开发；
> MVVM则是对MVP的改造，用ViewModel代替Presenter，ViewModel负责View和Model之间通信，Model和ViewModel之间存在双向数据绑定，数据更新时自动同步到视图

> MVC适用于需要将业务逻辑和数据处理分开的应用程序
> MVP适用于需要将逻辑和视图分开的应用程序
> MVVM适用于需要大量数据绑定的应用程序

## vue 父子组件传值有哪些方法

> props
> $emit（自定义事件、全局事件总线 ）
> $attrs（获取props未定义的属性）、$listeners（包含该组件所有的监听事件）
> provide/inject（主要是跨级组件间通信，在vue2中不是响应式，可以通过传入响应式数据比如当前this，或者函数式的方式实现响应式）
> v-model
> 插槽
> vuex

## v-model 的原理

> v-model是一个语法糖，他可以用在表单元素上，也可用在组件实例上，实际上通过v-bind绑定一个数据v-on监听表单value变化

```vue
<input v-model="sth" />
// 等同于
<input v-bind:value="message" v-on:input="message=$event.target.value">
// $event 指代当前触发的事件对象;
// $event.target 指代当前触发的事件对象的dom;
// $event.target.value 就是当前dom的value值;
// 在@input方法中，value => sth;
// 在:value中,sth => value;
```

### vue2

v-model 只是 value + change 的语法糖，监听输入并触发改变，因此只要实现 “监听” + “触发” 就可以自定义 v-model

```vue
<ChildComponent  v-model="pageTitle" />
<!-- 简写: -->
<ChildComponent  :value="pageTitle" @input="pageTitle = $event" />
```

```vue
<!-- 子组件: -->
<template>
  <input @input="handleChange"/>
</template>

<script>
export default {
  // 通过model可以自定义属性和事件名
  model: {
    event: 'change',
    prop: 'title'
  },
  props: {
    title: {
      type: String,
    }
  },
  methods: {
    handleChange () {
      // 对外暴露一个change事件
      this.$emit('change', !this.checked)
    }
  }
}
</script>
```

使用v-model实现双向绑定。但有时候我们希望一个组件可以实现多个数据的“双向绑定”，而`v-model`一个组件只能有一个(Vue3.0可以有多个)，这时候就需要使用到`.sync`

```vue
<ChildComponent :title.sync="pageTitle" />
<!-- 简写: -->
<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
```

```js
// 必须使用update:title事件名
this.$emit('update:title', newValue)
```

.sync与v-model的异同

相同点:

1. 两者的本质都是语法糖，目的都是实现组件与外部数据的双向绑定
2. 两个都是通过属性+事件来实现的

不同点:

1. 一个组件只能定义一个v-model,但可以定义多个.sync
2. v-model与.sync对于的事件名称不同，v-model默认事件为input,可以通过配置model来修改，.sync事件名称固定为update:属性名

### vue3

vue3移除了.sync修饰符，将两者进行合并  形式 v-model:modelValue = 自定义数据

子组件接收props.modelValue  和 update:modelValue自定义事件

```vue
<ChildComponent v-model="pageTitle" />
<!-- 是以下的简写: -->
<ChildComponent
  :modelValue="pageTitle"
  @update:modelValue="pageTitle = $event"/>
```

更改事件名，可多个v-model数据双向绑定

```vue
<ChildComponent v-model:title="pageTitle" />
<!-- 是以下的简写: -->
<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
```

```vue
<!-- 子组件接收: -->
<script setup lang="ts">
//接受props
let props = defineProps(["title"])
let emit = defineEmits(['update:title']) // 固定形式
//子组件内部按钮的点击回调
const handler = () => {
  //触发自定义事件
  emit('update:title', props.title + 1000);
}
</script>
```

## vue开发中常用的指令有哪些

> v-bind：用于绑定 HTML 属性到 Vue 实例的数据上
> v-model：用于实现双向数据绑定，将表单元素的值与 Vue 实例的数据进行同步
> v-for：用于循环渲染列表数据
> v-if、v-else、v-else-if：用于条件性渲染元素
> v-show：用于根据条件显示或隐藏元素
> v-on：用于绑定事件处理函数
> v-html：用于输出 HTML 标记
> v-text：用于输出文本
>
> 除了以上这些，Vue.js 还提供了很多其他的指令，如 v-cloak、v-pre、v-once 等等。指令可以使 HTML 具有更强的动态性和交互性，让开发者更加便捷地构建 Web 应用程序

## vue生命周期

> vue生命周期是用来描述一个组件从创建到销毁的过程，主要分为8个钩子函数，对应4个阶段分别是创建、挂载、更新、销毁。

> `beforeCreate:` 通常用于初始化数据和事件
>
> `created:` 实例创建完成，可访问data、computed、watch、method，但未挂载DOM不能访问el属性
>
> `beforeMount:` 挂载开始之前被调用，render函数首次被调用
>
> `mounted:` 实例挂载到DOM上，可以通过API访问DOM节点，可以进行ajax交互
>
> `beforeUpdate:` 响应式数据更新时调用，此时虽然响应式数据更新了，但对应的真实 DOM 还没有被渲染
>
> `Updated:` 虚拟DOM重新渲染和打补丁，可执行依赖DOM的操作
>
> `beforeDestroy:` 实例销毁之前调用，`this` 仍能获取到实例
>
> `destroyed:` 实例销毁，解绑所有事件与指令

## vue 子组件和父组件执行顺序

> 创建过程自上而下，挂载过程自下而上，即:
> parent created
> child created
> child mounted
> parent mounted
>
> Vue创建是一个递归过程，先创建父组件，有子组件就创建子组件，因此创建阶段先有父组件再有子组件；子组件首次创建会添加到父组件mounted钩子队列中，等到patch结束再执行它们，可见子组件的mounted钩子也是先进入到队列中的，因此等到patch结束子组件mounted钩子也就先执行

**加载渲染过程：**

> 父组件 beforeCreate
>
> 父组件 created
>
> 父组件 beforeMount
>
> 子组件 beforeCreate
>
> 子组件 created
>
> 子组件 beforeMount
>
> 子组件 mounted
>
> 父组件 mounted

**更新过程：**

> 父组件 beforeUpdate
>
> 子组件 beforeUpdate
>
> 子组件 updated
>
> 父组件 updated

**销毁过程：**

> 父组件 beforeDestroy
>
> 子组件 beforeDestroy
>
> 子组件 destroyed
>
> 父组件 destoryed

## Vue的双向数据绑定和响应式的区别

> Vue的响应式是指数据驱动视图，当创建一个普通js对象，传递给Vue的data属性或者ref reactive时，vue会进行检测变化，通知视图更新
>
> 双向数据绑定是指当数据变化视图自动更新，可以利用V-model实现
>
> 双向数据绑定只是响应式的一部分，也是响应式能力的体现之一，但并不是全部。Vue的响应式系统还包括数据检测等，确保数据和视图保持同步

## 发布订阅模式

> 发布-订阅模式（Publish-Subscribe Pattern）是一种常见的软件设计模式，用于解耦发布者和订阅者之间的关系。一种对象间一对多的依赖关系，当一个对象状态发生改变，依赖它的对象都会收到状态改变的通知
>
> 在发布-订阅模式中，发布者（也叫做主题）维护一个订阅者列表，订阅者可以订阅主题，也可以取消订阅。当主题的状态发生变化时，它会通知所有的订阅者，订阅者会收到通知并执行相应的操作。
> 这种模式可以帮助我们实现松耦合的设计，让发布者和订阅者之间解除直接的依赖关系，让它们能够独立地演化，从而更容易实现可维护性和可扩展性。

## vue的响应式机制

> vue是采用数据劫持和发布订阅模式，通过Object.defineProperty()来劫持data对象中各个属性的getter和setter，在数据发生变动时发布消息给订阅者，触发相应的监听回调。但是会有一个问题，当给data中某个对象新增一个属性的时候，它不会是响应性的，应为defineProperty无法监听到，所以vue提供$set来解决。vue3引入反射和代理的概念，也就是reflect和proxy，创建一个对象的代理，用于在目标对象之前设置一层拦截，从而可以对目标对象的访问进行过滤和改写。在vue3中这样一个过程叫做reactive，但reactive只能代理引用数据类型，所以vue3额外提供ref处理简单数据类型的响应性，ref本质上并没有进行数据的监听而是构建了一个RefImpl的类通过get set标记这个类里面的value函数来实现，所以ref必须要通过.value触发，本质就是调用value函数
>

> 底层的话，首先对数据进行劫持监听，组件实例化的时候，监听器Observer也会实例化，对data属性进行遍历，用defineProperty对其进行劫持，针对每个属性会生成该属性的Dep，在get里面调用Dep.depend方法将Dep实例和Watcher实例用一个数组subs进行存储，在set里面调用Dep.notify将对subs进行遍历执行Watcher中绑定的数据更新组件，Watcher是在mount阶段初始化，主动调用一次render，这个时候才会去触发data属性劫持中的get将Watcher和Dep进行关联，响应真实Dom

> 模拟Observer，没考虑递归
>
> ```js
> let data = { name: 'qwe' }
> // 创建一个监听的实例对象，用于监视data中属性的变化
> const obs = new Observer(data)
> function Observer(obj) {
>     // 汇总对象中所有的属性形成一个数组
>     const keys = Object.keys()
>     keys.forEach(item => {
>         // 这里的this指的obs实例，给实例添加属性
>         Object.defineProperties(this, item, {
>             get() {
>                 return obj[item]
>             },
>             set(value) {
>                 // ...解析模板，生成虚拟dom...
>                 obj[item] = value
>             }
>         })
>     })
> }
> ```

首先要对数据进行`劫持监听`，所以我们需要设置一个监听器 `Observer`，用来监听所有属性。如果属性发上变化了，就需要告诉订阅者 `Watcher` 看是否需要更新。

因为订阅者是有很多个，所以我们需要有一个消息订阅器 Dep 来专门收集这些订阅者，然后在监听器 `Observer` 和订阅者 `Watcher` 之间进行统一管理的。

接着，我们还需要有一个指令解析器 `Compile`，对每个节点元素进行扫描和解析，将相关指令对应初始化成一个订阅者 `Watcher`，并替换模板数据或者绑定相应的函数，此时当订阅者 `Watcher` 接收到相应属性的变化，就会执行对应的更新函数，从而更新视图。

**因此接下去我们执行以下 3 个步骤，实现数据的双向绑定：**

- 实现一个监听器 `Observer`，用来劫持并监听所有属性，如果有变动的，就通知订阅者。
- 实现一个订阅者 `Watcher`，可以收到属性的变化通知并执行相应的函数，从而更新视图。
- 实现一个解析器 `Compile`，可以扫描和解析每个节点的相关指令，并根据初始化模板数据以及初始化相应的订阅器。

## Vue template 到render的过程

> 首先会调用Parse方法对template解析生成AST抽象语法树，然后对静态节点做优化，深度遍历对静态节点进行标记，为后续更新渲染可以直接跳过静态节点做优化，然后调用generate将抽象语法树编译成render字符串，再通过new Function(render)生成render函数

```js
// 调用parse方法将template转化为ast（抽象语法树）
const ast = parse(template.trim(), options)
// 对静态节点做优化
optimize(ast,options)
// 生成代码
const code = generate(ast, options)
```

## vue3有哪些改变

> 就像刚才提到的数据监听机制的改变，通过Proxy代理替代Object.defineProperty进行响应式。然后就是这个composition API替代了V2的一个配置式API，composition API在这个Vue3.0和3.2在呈现上有一点不同，比如最初composition API以setup函数作为入口，也必须返回两种数据类型的值，一种是对象另一种是函数，当返回一个对象时，对象中的属性和方法可以在template中直接进行使用，返回一个函数的时候，这个函数会作为一个render函数，但是这种setup函数逻辑就会导致这个函数特别臃肿，所以在vue3.2中出现了script setup的一个语法糖尝试去解决这个问题。然后就是v3性能的一些优化，比如tree-shaking的优化减小打包的体积，diff的优化利用patchflag对需要更新的节点打上标记，就不需要新旧对象树的一个比较。v3还提供一些内置组件比如，Suspense异步组件，Telport组件，更好的支持TypeScript

> 首先vue3采用了组合式API（Composition API）的方式替代vue2配置式API；然后是数据监听机制的改变，vue2是通过Object.defineProperty实现，vue3则基于代理proxy实现；还有生命周期的改变，总体来讲变化不大只是在大部分钩子名称前加一个'on'，而且需要提前引入；vue3可以多根节点，还提供teleport组件和Suspense异步组件，更好的支持typescript，通过tree-shaking优化了打包后的体积等

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29414776/1658110268214-b9a20581-ae3b-4cee-b55d-d4777b0393c8.png?x-oss-process=image%2Fresize%2Cw_926%2Climit_0)

## Proxy对象

> Proxy是创建一个对象的代理，用于在目标对象之前设置一层拦截，从而可以对目标对象的访问进行过滤和改写，它包含两个参数，一个目标对象，一个拦截器对象，当使用Proxy对象访问目标对象属性或方法时，拦截器对象会介入访问过程进行一些处理

## computed和watch的区别

> computed计算属性主要是依赖其它属性返回一个新的属性，且有缓存机制只有当依赖的属性值发生改变，下一次计算属性才会更新。watch监听器主要是观察，没有缓存性，监听的数据发生变化就会执行相应的回调操作
>
> computed属性是计算属性，它是根据其它属性的值计算得出的一个值，因此computed属性本身不能被直接修改

## keep alive

> keep alive是一个内置组件，它不会出现在父组件链中，也不会出现在dom元素中，当包裹一个动态组件的时候，该组件会被缓存到内存当中，而不是销毁，内置提供一些钩子函数，可以让我们组件缓存和激活时执行一些额外的逻辑，当组件被换掉时，会被缓存到内存当中、触发deactivated生命周期，当组件被切回来时，再去缓存里找这个组件、触发activated生命周期，主要避免在组件切换时重复渲染和销毁

## nextTick

> 监听到数据发生变化后，视图并不会立即同步渲染，vue而是开启一个队列，并缓冲同一事件循环中发生的所有数据变更。在下一个事件循环tick中，刷新队列并执行相应操作。也就是将回调延迟到下一次DOM更新循环之后执行，也是利用原生JS的promise、MutationObserver、setImmediate、setTimeout方法模拟实现对应的微任务和宏任务，通过这些异步回调任务队列来实现vue框架中自己的异步回调队列
>
> **使用原生的 setTimeout 方法**：在 [Vue.js](http://vue.js/) 2.x 中，如果浏览器支持 Promise，则会优先使用 [Promise.then](http://promise.then/)() 方法。如果不支持 Promise，则会使用原生的 setTimeout 方法模拟异步操作。
> **使用 MutationObserver**：如果浏览器支持 MutationObserver，[Vue.js](http://vue.js/) 会使用 MutationObserver 监听 DOM 更新，并在 DOM 更新完成后执行回调函数。
> **使用 setImmediate**：在 IE 中，setImmediate 方法可以用来延迟异步执行任务。在 [Vue.js](http://vue.js/) 2.x 中，如果浏览器支持 setImmediate，则会优先使用 setImmediate，否则会使用 setTimeout。
>
> 作用：减少操作DOM的次数，频繁的操作DOM更新很耗性能

## 虚拟DOM

> 在传统的web页面开发中，每当页面发生变化，浏览器都会重新渲染整个页面，这样效率很低而且性能不好。为了解决这个问题，框架就采用了虚拟DOM，虚拟DOM是对真实DOM的一种抽象，用js对象描述DOM的层级结构，包括元素的标签，属性，子元素，DOM中的一切属性都在虚拟DOM中有对应的属性。当页面数据发生变化，再次调用render函数来获取到新的虚拟dom树（第一次初次渲染时调用），新旧对象树进行对比，也就是diff算法，最后将有差异的地方应用到真实DOM树中，视图就会更新
>
> 优点：减少操作DOM的次数，提升性能、最大的优势在于抽象了原本的渲染过程，实现了跨平台的能力，而不仅仅局限于浏览器的 DOM，可以是安卓和 IOS 的原生组件
> 缺点：首次渲染大量DOM时，由于多了一层虚拟DOM的计算，会比innerHTML插入慢

虚拟DOM的生成

> 在vue中我们通常会编写template模板，这个模板会被编译器compiler编译，先转化成AST抽象语法树，进而转换成js代码，也就是render函数
>
> ![image.png](https://cdn.nlark.com/yuque/0/2022/png/29414776/1657994012522-f742065c-4766-446b-a300-6e12bb72c147.png)

## vue diff算法原理

> 实质是调用patch函数，比较新旧节点，一边比较一边给真实DOM打补丁
>
> 在新老虚拟DOM对比时：首先对比节点本身，判断是否为同一级节点，这里主要比较标签名和key，如果标签名不同则为不同节点，直接删除该节点重新创建节点进行替换不用进行深度遍历；如果标签名相同key也相同则为相同节点，则也不用进行深度遍历。这也就是为什么v-for要写key的作用，会更有效更新虚拟DOM；如果key不同，则进行patchVnode，判断该节点的子节点，如果新节点没有子节点直接删除旧的子节点，如果都有子节点，进行updateChildren，然后对新旧节点的子节点进行操作，也就是diff的核心。
> diff核心收尾指针（双端比较法）头对头 尾对尾 新头的老尾 老头对新尾，进行4次比对，如果4次找寻到key下相同则进行复用，若都没有匹配，就会在新的Vdom的队头开始再去寻找，看老的Vdom有没有对应的元素进行相应的移动删除和创建
>
> 而vue3中采用了双端快速diff，核心是patchKeyedChildren基于最长递增子序列进行移动添加删除，在生成VNode的时候，同时打上标记，也就是Patch Flag，patch过程中就会判断这个标记来 优化diff，跳过一些静态节点对比，具体的话也有两个指针，新老Vdom各有两个，只对比两种情况，队头和队头，队尾和队尾，匹配上了就和V2是一样的，如果没有匹配上，对新的Vdom进行最长递增子序列的计算，也就是寻找依次递增的元素，这些元素就是固定的，再去寻找不是这些元素的Vdom去和老的Vdom进行对比，总的来说，新旧虚拟dom不用对整颗树进行完整的比较，可以更快的更新页面，提升性能
>
> V3的时间复杂度是O(nlogn)，V2时间复杂度O(n)，虽然V3的复杂度更高，核心是为了减少dom的移动，损失js一点性能来提升浏览器dom的渲染效率

## key的作用

> key是为了优化diff算法的性能，实际开发中有两个地方使用了key，v-for、transition-group
> v-for使用key时，vue的diff会根据虚拟dom的标签名type和key判断新旧节点，也就是Vnode是否可复用，如果不加key就会导致在列表更新时造成性能浪费（不能用index作为key，因为删除中间的列表项，后面的key会更新，不能作为列表项的唯一标识）
> transition-group使用key，vue会精确的知道哪个元素需要添加过渡效果，不加就会导致过渡效果的混乱

## vue的路由模式

> hash模式：路径中会有一个#，#后面的路径叫hash值，不会随着http请求发送给服务器，还可以个hash改变监听事件，
>
> ```js
> window.addEventListener("hashchange", funcRef, false);
> ```
>
> history模式：需要和后端配合，不然刷新会造成路径与服务器不匹配，获取不到资源

## vue-router有哪几种导航钩子

> 路由是一种对页面跳转和页面状态管理的技术。通过监测浏览器路由（url）变化，渲染不同的页面内容
> 优点：前后端分离，页面切换无刷新（ajax）、动态路由
> 缺点：首次加载慢、SEO不友好、浏览器兼容

> 全局路由钩子：router.beforeEach（前置守卫）、beforeResolve（全局解析守卫）、router.afterEach（后置守卫）
> 单个路由独享守卫：beforeEnter
> 组件内钩子：beforeRouteEnter（进入组件前）、beforeRouteUpdate（路由跳转组件被复用跳转）、beforeRouteLeave（离开组件时）

## data写成函数形式

> 在 Vue 中组件是可以复用的，一个组件被创建好之后，就可以被用在其他各个地方，而组件不管被复用了多少次，组件中的 data 数据应该是相互不影响的。基于数据不影响的理念，组件被复用一次，data 数据就应该被复制一次，写成函数形式，每一个函数都会有自己的存储空间，每次执行都会创建自己的执行上下文，类似于给每个组件实例创建一个私有的数据空间，互不影响。如果单纯写成对象形式，这些实例都是同一个构造函数，就使得所有组件实例共用了一份 data，就会造成一个变了全都会变的结果

## SPA单页面理解，优缺点

> SPA仅在页面初始化加载相应的HTML、CSS、JS。一旦加载完，SPA不会因为用户的操作而进行页面的重新加载或跳转，而是利用路由机制实现HTML内容的变换
> 优点：用户体验更好、前后端分离、开发效率高、良好的扩展性
> 缺点：首屏加载时间较长、SEO不友好、路由管理较复杂

## mixin解决什么问题 原理 缺点

> mixin通常来讲就是混入，本质是一个对象，当多个组件或页面有相同的配置，可以通过mixin抽离公共代码，达到复用效果
> 当在一个组件使用mixin时，vue会将mixin中的选项与组件选项进行合并，比如数据对象，methods对象，生命周期钩子等，如果**有重复的属性以组件的选项为准**
> 在执行钩子函数时，**首先执行mixin里的再执行组件里的钩子函数**
> 不同mixin里的同名方法，按照引进属性，**后面的覆盖前面同名方法**
>
> 缺点：命名冲突，滥用的话后期很难维护，排查问题困难

## mixin的不足 和 hooks对比

> mixin 和 hooks都是将部分组件相同逻辑抽离成可重用的模块
>
> 1. mixin容易发生冲突：一个组件中如果引用多个mixin，变量的来源会变得错综复杂，需要我们自己手动调试，才知道数据来源；hooks引入变量和方法是显示传入，能清楚的知道变量和方法的数据来源
> 2. mixin同名变量会被覆盖；hooks可以在引入的时候对同名变量重命名
> 3. 可重用性是有限的：我们不能向mixin传递任何参数来改变它的逻辑，降低了它们在抽象逻辑方面的灵活性

## watch和watchEffect

> watch既要指明监视属性，也要指明监视的回调
>
> watchEffect不用指明监视属性，用到哪个属性就监视哪个属性
>
> watchEffect有点像computed：
>
> - 但computed注重计算出来的结果，必须要写返回值
> - 而watchEffect注重过程，不用写返回值
>
> ```js
> //watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
> watchEffect(()=>{
>     const x1 = sum.value
>     const x2 = person.age
>     console.log('watchEffect配置的回调执行了')
> })
> ```

## Vue和React的区别

> Vue和React都采用了组件化开发、响应式数据绑定、虚拟DOM机制和组件间通信

> 核心思想不同，Vue是一个灵活易用的渐进式双向绑定的MVVM框架；React是一个用于构建用户界面框架，主要用于构建UI，它的核心思想是声明式渲染和组件化、单向数据流，通过状态驱动视图，React既不属于MVC也不属于MVVM架构；(声明式与之相对应的是命令式，命令式指的是通过DOM操作一步步把网页变成想要的样子，而声明式则是只需要通过状态去形容最后的网页长什么样子即可)
>
> 响应式数据绑定的实现方式不同，Vue 3使用了Proxy API来实现响应式数据绑定，实现动态改变对象属性。而React则需要使用setState方法来触发视图的重新渲染；
>
> 组件渲染方式不同，Vue3采用了template语法来描述组件的结构和行为，组件可以被定义为一个对象并且包含一个template、script和style标签，React组件可以被定义为一个类或者函数，并且采用JSX语法来描述组件的结构和行为；
>
> API设计风格不同，Vue3的API设计倾向于提供语法糖和便捷方法，使得开发者能够更加高效地编写代码。例如，Vue 3中提供了v-model指令来实现双向数据绑定。React则倾向于提供一些基础API，并且鼓励开发者自行封装复杂的功能。这样做可以让代码更加灵活和可扩展

## Vue Hooks 和 React Hooks的区别

## VueX和redux的区别

## 谈一下对vuex的理解

> vuex就是一个状态管理模式，因为单向数据流的规范，如果多个组件中要使用祖先组件的一些数据，会变得特别的繁琐，并且兄弟组件之间共享数据也会非常繁琐。这就导致我们需要用vuex管理组件共享的状态，以一个全局单例模式来管理
>
> State就是全局共享的状态
> Getter是类似于Vue的computed来将状态经过处理后返回
> Mutation用于同步修改State中的状态
> Action则是用于管理异步请求，并在获取到状态之后将要改变的状态分发到mutation中
> Module则是将全局的状态分模块进行管理

## vuex和pinia的区别

> Pinia和Vuex都是vue的全局状态管理器，其实Pinia就是Vuex5。
>
> pina最大的优点就是体积轻巧，更好的支持TS，支持多个Store，去除了Vuex的mutation。Vuex适用于规模大、复杂的大型项目

## vuex为什么要区分mutation和action

> mutation处理同步操作，action主要处理异步操作。在mutation中混合异步调用会导致你的程序很难调试，而且区分action和mutation也不是为了解决竞态问题，主要是为了能用devtools追踪状态变化。这样mutation执行完成后就可以对应到新的状态，devtool就可以打个snapshot存下来，就可以time-travel

## Vue是单向数据流为什么还有双向数据绑定

> 单双向数据绑定与数据流是两个不同维度的概念，数据绑定是View与Model之间的映射关系，数据流指的是组件之间的数据流动。因此，单向数据流也可有双向绑定，双向数据流也可以有双向绑定
>
> 双向数据绑定，是利用V-model实现，也是一个语法糖通过给表单元素绑定数据和监听input事件实现
>
> 单项数据流，父组件可以向子组件传递props，子组件不能修改props，只能通过事件通知父组件数据更改

## uniapp为什么能多端发布
