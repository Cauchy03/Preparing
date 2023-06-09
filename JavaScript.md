## 解释一下原型链

> 在js中是使用构造函数创建一个对象，每一个构造函数内部都有一个prototype属性，这个属性是一个对象包含了该构造函数所有实例公享的属性和方法，也被称为原型对象。当使用构造函数新建一个对象后，在这个对象的内部包含一个指针，指向构造函数的prototype属性对应的值，也就是通过__proto__属性访问原型对象。
> 当访问一个对象的属性，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型也会有自己的原型，于是就这么一直找下去，直到大Object，这就是原型链的概念

![](https://static.vue-js.com/6a742160-725e-11eb-ab90-d9ae814b240d.png)

## 具体原型链判断

[彻底搞懂Function，Object，__proto__，prototype之间的关系 - 掘金 (juejin.cn)](https://juejin.cn/post/6844903930216841230)

- **`javascript中一切皆对象，函数也属于对象。`**
- **`所有对象都含有__proto__。`**
- **`只有函数才有prototype。`**
- **`所有函数的默认原型都是Object的实例。`**

### `Object.prototype.__proto__`  原型链的尽头是什么

由于`Object`是构造函数，原型链终点是`Object.prototype.__proto__`，而`Object.prototype.__proto__=== null // true`，所以，原型链的终点是`null`。原型链上的所有原型都是对象，所有的对象最终都是由`Object`构造的，而`Object.prototype`的下一级是`Object.prototype.__proto__`。

![image](https://cdn.nlark.com/yuque/0/2020/jpeg/1500604/1605247722640-5bcb9156-a8b4-4d7c-83d7-9ff80930e1de.jpeg)

### `Function.prototype.__proto__` 答案是什么

`Object.prototype`	

Function 的 prototype 属性对象也是 Object 构造函数构造的，所以`Function.prototype `的原型指向 Object 的 prototype 属性

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/9/2/16cf0217636cafd0~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.image)

### 构造函数自身的`__proto__`是什么   

`Function.prototype`

函数都是有 Function 构造函数构建的，包括 Function 构造函数本身也是由自己构建的，所以构造函数的原型会指向 Function 的 prototype 属性

可以看出当把函数当成对象的时候，函数也有`__proto__`属性，并且生成它的函数就是`Function`，那么`Function`自己呢，因为`Function`本身也是函数，函数是由`Function`生成的，那么看下例。

```javascript
   Function.__proto__ === Function.prototype  //true
```

### `Object.__proto__` 答案是什么

Object函数也是一个函数对象，也是由Function生成的，那么看下例

```javascript
  Object.__proto__ === Function.prototype  //true
```

### `Object instanceof Function`

返回 true ,因为 Object 是由 Function 构造函数创建的，所以 Object 的原型上有`Function.prototype`

### `Function instanceof Object  `

返回 true , Function 的 `__proto__` 指向 `Function.prototype`, `Function.prototype` 由 Object构建，所以 `Function.prototype` 的`__proto__`指向` Object.prototype`

### 总结

一般而言，一个"function"类型的对象，应该是由Function函数生成的，也就是`Function.prototype.__proto__ === Function.prototype`才对，如果是这样的话，也就出现了跟Object一样的问题，一直循环利用，没有尽头。所以Javascript规定，`Function.prototype.__proto__ === Object.prototype`,`Object.prototype.__proto__ === null`，是原型链有终点。也就是在原型链的终点处有2个特殊情况。

- 函数含有`__proto__`与`prototype`属性，`__proto__`指向`Function.prototype`,`prototype`指向`Object.prototype`，以Array函数为例

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/9/2/16cf0b28fcf48c50~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.image)

- 所有的类型的`[[Prototype]]`特性，即 `__proto__`属性均指向的是 `Function.prototype`，同时 `Function.prototype` 的`[[Prototype]]`特性，即 `__proto__`属性又指向了 `Object.prototype`，`Object.prototype`的`__proto__`又指向`null`，即原型链的终点。

## 对象继承方式

> 对象的继承
> - 原型链继承，引用值共享的问题
> - 构造函数继承，无法拿到原型上的方法
> - 组合继承，(伪经典继承，会调用两次父类构造函数)
> - 寄生组合继承

- 原型链继承：通过让子类的原型对象指向父类的实例对象来实现继承。缺点是父类的引用属性会被所有子类实例共享。
- 构造函数继承：通过在子类构造函数中调用父类构造函数来实现继承。优点是可以实现多重继承，缺点是不能访问父类原型对象上的方法和属性。
- 组合继承：同时使用原型链继承和构造函数继承的方式来实现继承。优点是可以访问父类原型对象上的方法和属性，缺点是会调用两次父类构造函数。
- 原型式继承：通过 Object.create() 方法来实现继承。优点是简单易懂，缺点是父类的引用属性会被所有子类实例共享。
- 寄生式继承：在原型式继承的基础上，将创建子类实例的过程封装在一个函数中，可以在这个函数中对子类实例进行扩展。缺点和原型式继承相同。
- 寄生组合式继承：通过寄生式继承来继承父类原型对象，并将继承得到的原型对象赋值给子类的原型对象来实现继承。优点是避免了组合继承中重复调用父类构造函数的问题。

## this指向

> this指向包括函数调用模式，作为函数来调用，this指向全局对象window；方法调用模式，作为一个对象的方法调用，this指向这个对象；构造函数调用模式，this指向构造函数创建的实例；call、apply、biand调用模式，可以指定调用函数的this指向。

this 是执行上下文中的一个属性，它指向最后一次调用这个方法的对象。在实际开发中，this 的指向可以通过四种调用模式来判断。

- 第一种是**函数调用模式**，当一个 函数不是一个对象的属性时，直接作为函数来调用时，this 指向全局对象。
- 第二种是**方法调用模式**，如果一个函数作为一个对象的方法来调用时，this 指向这个对象。
- 第三种是**构造器调用模式**，如果一个函数用 new 调用时，函数执行前会新创建一个对象，this 指向这个新创建的对象。
- 第四种是 **apply 、 call 和 bind 调用模式**，这三个方法都可以显示的指定调用函数的 this 指向。其中 apply 方法接收两个参数：一个是 this 绑定的对象，一个是参数数组。call 方法接收的参数，第一个是 this 绑定的对象，后面的其余参数是传入函数执行的参数。也就是说，在使用 call() 方法时，传递给函数的参数必须逐个列举出来。bind 方法通过传入一个对象，返回一个 this 绑定了传入对象的新函数。这个函数的 this 指向除了使用 new 时会被改变，其他情况下都不会改变。



这四种方式，使用构造器调用模式的优先级最高，然后是 apply、call 和 bind 调用模式，然后是方法调用模式，然后是函数调用模式

## var和let、const的区别

> let、const是ES6新增的指令，可以声明块级作用域，不存在变量提升和重复声明、存在暂时性死区也就是不能在声明之前使用变量，const还有两个特点就是必须设置初始值且不能改变指针指向，当const声明一个基本数据类型的值时，其值就是保存在变量指向的内存地址中，所以不能改变，如果是一个引用数据类型，变量指向的内存地址保存一个指针，指针指向另一个数据结构，而这个数据结构是可以改变的

## 闭包的作用和原理以及使用场景

> 其实就是一个函数访问另一个函数，两个作用：保护和保存
> 函数执行形成一个私有作用域，不受外部的干扰，适合模块化开发
> 另一个是保存，当一个函数返回另一个引用数据类型的时候，被外界所接收了就会形成不销毁的作用域，一直存储在堆内存里面，容易造成内存泄漏
>
> 内存泄漏：浏览器无法回收这个代码的内存

```
基本数据类型:Undefined、Null、布尔值（Boolean）、字符串（String）、数值（Number）、Symbol、BigInt
引用数据类型:Object 
	Symbol 代表创建后独一无二且不可变的数据类型，它主要是为了解决可能出现的全局变量冲突的问题。
	BigInt 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地存储和操作大整数，即使这个数已经超出了 Number 能够表示的安全整数范围。
	两种类型的区别在于存储位置的不同：
	原始数据类型直接存储在栈中，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储；
引用数据类型存储在堆中，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。
```

比如，函数 A 内部有一个函数 B，函数 B 可以访问到函数 A 中的变量，那么函数 B 就是闭包。

```js
function A() {
  let a = 1
  window.B = function () {
      console.log(a)
  }
}
A()
B() // 1
```

在 JS 中，闭包存在的意义就是让我们可以间接访问函数内部的变量。经典面试题：循环中使用闭包解决 var 定义函数的问题

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```

首先因为 `setTimeout` 是个异步函数，所以会先把循环全部执行完毕，这时候 `i` 就是 6 了，所以会输出一堆 6。解决办法有三种：

- 第一种是使用闭包的方式

```js
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j)
    }, j * 1000)
  })(i)
}
```

在上述代码中，首先使用了立即执行函数将 `i` 传入函数内部，这个时候值就被固定在了参数 `j` 上面不会改变，当下次执行 `timer` 这个闭包的时候，就可以使用外部函数的变量 `j`，从而达到目的。

- 第二种就是使用 `setTimeout` 的第三个参数，这个参数会被当成 `timer` 函数的参数传入。

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer(j) {
      console.log(j)},
    i * 1000,i)
}
```

- 第三种就是使用 `let` 定义 `i` 了来解决问题了，这个也是最为推荐的方式

```js
for (let i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```

## 谈谈你对作用域的理解

> 对于比编程语言来讲，最基本的功能就是能够存储变量的值，对变量的值进行修改和访问。这样一套规则就是作用域
>
> 作用域包括全局作用域、函数作用域（局部作用域）和块级作用域，任何不在函数或者大括号中声明的变量都是全局作用域，可以在程序的任意位置访问，函数作用域只能在函数内部访问，ES6新增的let、const指令可以声明块级作用域，通过{}包裹，只能在{}内部访问，一般在循环中使用块级作用域。
> 作用域链指在当前作用域下查找所需变量，如果没有找到，就去父级作用域寻找，一直往上级作用域查找直到windo对象，这一层次关系被称为作用域链，当查找一个变量时，如果当前执行环境中没有找到，可以沿着作用域链向后查找
>
> 块级作用域可以解决过多的全局变量和函数产生的命名冲突

## apply和call的作用及区别

```
都是改变this指向，唯一的区别就是传入的参数不同，call的第一个参数是函数的this指向，第二个参数往后依次传入函数；而apply的第二个参数是一个数组，包含要传入的所用参
```

## 连续多个bind，最后this指向是什么？

> this永远指向第一次bind绑定的函数
>

## `es6`新特性？箭头函数和普通函数有啥区别？

> ES6新增let、const指令，模板字符串、箭头函数、默认函数参数、扩展运算符、对象的解构、类、proxy代理等等
>
> 箭头函数没有自己this、没有原型对象、没有arguments、不能作为构造函数、call、apply、bind也不能改变箭头函数的this指向，但是比普通函数更加简洁

```
ES6新特性

- 不一样的变量声明：const和let
- 模板字符串
- 箭头函数
- 函数的参数默认值
- 扩展运算符
- 二进制和八进制字面量
- 对象和数组的解构
- 对象超类（允许在对象中使用super）
- for...of和for...in
- ES6中的类
```

### 箭头函数与普通函数的区别

**1）箭头函数比普通函数更加简洁**

- 如果没有参数，就直接写一个空括号即可
- 如果只有一个参数，可以省去参数的括号
- 如果有多个参数，用逗号分割
- 如果函数体的返回值只有一句，可以省略大括号
- 如果函数体不需要返回值，且只有一句话，可以给这个语句前面加一个void关键字。最常见的就是调用一个函数：

```js
let fn = () => void doesNotReturn();
```

**（2）箭头函数没有自己的this**

箭头函数不会创建自己的this， 所以它没有自己的this，它只会在自己作用域的上一层继承this。所以箭头函数中this的指向在它在定义时已经确定了，之后不会改变。

**（3）箭头函数继承来的this指向永远不会改变**

```js
var id = 'GLOBAL';
var obj = {
  id: 'OBJ',
  a: function(){
    console.log(this.id);
  },
  b: () => {
    console.log(this.id);
  }
};
obj.a();    // 'OBJ'
obj.b();    // 'GLOBAL'
new obj.a()  // undefined
new obj.b()  // Uncaught TypeError: obj.b is not a constructor
```

对象obj的方法b是使用箭头函数定义的，这个函数中的this就永远指向它定义时所处的全局执行环境中的this，即便这个函数是作为对象obj的方法调用，this依旧指向Window对象。需要注意，定义对象的大括号`{}`是无法形成一个单独的执行环境的，它依旧是处于全局执行环境中。

**（4）call()、apply()、bind()等方法不能改变箭头函数中this的指向**

```js
var id = 'Global';
let fun1 = () => {
    console.log(this.id)
};
fun1();                     // 'Global'
fun1.call({id: 'Obj'});     // 'Global'
fun1.apply({id: 'Obj'});    // 'Global'
fun1.bind({id: 'Obj'})();   // 'Global'
```

**（5）箭头函数不能作为构造函数使用**

构造函数在new的步骤在上面已经说过了，实际上第二步就是将函数中的this指向该对象。 但是由于箭头函数时没有自己的this的，且this指向外层的执行环境，且不能改变指向，所以不能当做构造函数使用。

**（6）箭头函数没有自己的arguments**

箭头函数没有自己的arguments对象。在箭头函数中访问arguments实际上获得的是它外层函数的arguments值。

**（7）箭头函数没有prototype**

**（8）箭头函数不能用作Generator函数，不能使用yeild关键字**

## `promise.all` 和 `promise.allsettled`区别

> Promise.all()可以将多个Promise实例包装成一个新的Promise，成功时返回一个结果数组，如果失败则返回第一个失败Promise的值
>
> Promise.allsettled()也是将多个Promise包装成一个新的Promise，不管成功还是失败，都返回一个包含所有Promise结果状态的数组
>
> Promise.race()，传入一个包含多个Promise实例的数组，返回一个新的Promise，第一个完成Promise结果状态就是最终的结果状态

- `Promise.all` 

  可以将多个`Promise`实例包装成一个新的Promise实例。同时，成功和失败的返回值是不同的，成功的时候返回的是**一个结果数组**，而失败的时候则返回**最先被reject失败状态的值**。

- `Promise.race`

  `Promise.race([p1, p2, p3])`里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。当要做一件事，超过多长时间就不做了，可以用这个方法来

  **返回一个新的Promise，第一个完成Promise结果状态就是最终的结果状态**

- `Promise.allSettled`

  当所有的 promises 都已经结束无论是完成状态或者是失败状态，它都会返回一个 promise，这个 promise 将会**包含一个关于描述每个 promise 状态结果的对象数组**。

  ```js
  const p1 = new Promise((resolve, reject) => {resolve('ok')})
  const p2 = Promise.resolve('ok2')
  const p3 = Promise.reject('ok3')
  let result = Promise.allSettled([p1, p2, p3]).then(values => {
      console.log(values);
  })
  console.log(result);
  /* [{status: 'fulfilled', value: 'ok'},
  {status: 'fulfilled', value: 'ok2'},
  {status: 'rejected', reason: 'ok3'}]
  */
  ```

## substring和substr的区别

> substring()方法返回指定一个索引到另一个索引之间的字符串
> substr()方法返回一个指定位置开始指定长度的字符串

- `substring()`方法返回一个索引和另一个索引之间的字符串，语法如下：

  > str.substring(indexStart, [indexEnd])
  >
  > - substring()从提取的字符indexStart可达但不包括 indexEnd
  >
  > - 如果indexStart 等于indexEnd，substring()返回一个空字符串。
  >
  > - 如果indexEnd省略，则将substring()字符提取到字符串的末尾。
  >
  > - 如果任一参数小于0或是NaN，它被视为为0。
  >
  > - 如果任何一个参数都大于stringName.length，则被视为是stringName.length。
  >
  > - 如果indexStart大于indexEnd，那么效果substring()就好像这两个论点被交换了一样； 例如，str.substring(1, 0) == str.substring(0, 1)
  >
  > ```js
  > var str = 'abcdefghij';
  > console.log('(1, 2): '   + str.substring(1, 2));   // '(1, 2): b'
  > console.log('(1, 1): '   + str.substring(1, 1));   // '(1, 1): '
  > console.log('(-3, 2): '  + str.substring(-3, 2));  // '(-3, 2): ab'
  > console.log('(-3): '     + str.substring(-3));     // '(-3): abcdefghij'
  > console.log('(1): '      + str.substring(1));      // '(1): bcdefghij'
  > console.log('(-20, 2): ' + str.substring(-20, 2)); // '(-20, 2): ab'
  > console.log('(2, 20): '  + str.substring(2, 20));  // '(2, 20): cdefghij'
  > console.log('(20, 2): '  + str.substring(20, 2));  // '(20, 2): cdefghij'
  > ```

- `substr()`方法返回从指定位置开始的字符串中指定字符数的字符，语法如下：

  > str.substr(start, [length])
  >
  > - `substr()`会从`start`获取长度为`length`字符（如果截取到字符串的末尾，则会停止截取）。
  > - 如果`start`是正的并且大于或等于字符串的长度，则`substr()`返回一个空字符串。
  > - 若`start`为负数,则将该值加上字符串长度后再进行计算（如果加上字符串的长度后还是负数，则从0开始截取）。
  > - 如果`length`为0或为负数，`substr()`返回一个空字符串。如果`length`省略，则将`substr()`字符提取到字符串的末尾。
  >
  > ```js
  > var str = 'abcdefghij';
  > console.log('(1, 2): '   + str.substr(1, 2));   // '(1, 2): bc'
  > console.log('(-3, 2): '  + str.substr(-3, 2));  // '(-3, 2): hi'
  > console.log('(-3): '     + str.substr(-3));     // '(-3): hij'
  > console.log('(1): '      + str.substr(1));      // '(1): bcdefghij'
  > console.log('(-20, 2): ' + str.substr(-20, 2)); // '(-20, 2): ab'
  > console.log('(20, 2): '  + str.substr(20, 2));  // '(20, 2): '
  > ```

## symbol这个新增的基础数据类型有什么用

> symbol()是ES6新增的数据类型，表示唯一不可改变的数据类型，主要是解决属性命名冲突的问题

[Symbol类型有什么用？怎么用？ - 掘金 (juejin.cn)](https://juejin.cn/post/7021148449961672735)

## js脚本异步加载如何实现 有什么区别

> 在script标签中设置defer和async属性，可以让脚本与文档同步解析，在文档解析完成后再执行脚本，defer可以保证脚本的执行顺序而async不能保证；还可以通过动态创建的方式，也就是监听文档加载事件，等到加载完再引入；还可以将js脚本放到文档的底部，最后执行；设置定时器延迟加载脚本

## HTML文档的生命周期都有哪些？

> 主要包括三个事件DOMContentLoaded、onload、beforeUnload和unload，DOMContentLoaded时，已加载完HTML数据生成了DOM树，但是一些样式、图片之类的外部文件仍未加载，可以进行一些初始化接口 ；onload时，加载完所有的文件资源；beforeUnload时，用户准备离开，可以进行一些确认保存数据的操作，unload用户离开时，可以做一些不涉及延迟的操作，比如关闭相关弹出窗口

### 三个重要事件

- `DOMContentLoaded`

  浏览器已经加载完成HTML数据，并且构建了DOM树。但是如样式、图片之类的外部文件有可能仍未加载完成。所以，可以再此时寻找DOM元素，以及初始化接口。

  在遇到`<script>`标签之后，脚本会阻塞  `DOMContentLoaded`，浏览器将等待它们执行结束，因为脚本有可能修改页面的DOM，所以`DOMContentLoaded`是在解析完脚本以后才执行。

  额外说明：

  - 如果在样式后面有一个脚本，那么该脚本必须等待样式表加载完成。原因是，脚本可能想要获取元素的坐标和其他与样式相关的属性。

- `onload`

  浏览器已经加载完全部的HTML以及图片、样式等外部资源。

- `beforeunload` 和 `unload`

  `beforeunload`：用户准备离开，可以提醒用户是否保存了数据，确认要离开页面。

  `unload`：用户马上离开。此时仍可以处理一些操作，如发送统计数据等。

  如果存在`iframe`，父`iframe`会在子`iframe`卸载前卸载。父`beforeunload `=> 子`beforeunload `=> 父`unload `=> 子`unload`

### `DOMContentLoaded`

- `DOMContentLoaded` 事件发生在 `document` 上， 必须使用 `addEventListener` 来监听它。

```javascript
<script>
    document.addEventListener('DOMContentLoaded', function(){
        alert( 'DOMContentLoaded' );
    })
</script>

<script src="/js/01.js"></script>  // alert('01.js')

<script>
    alert('我是script1');
</script>

//弹出顺序： 01.js  /  我是script1 / DOMContentLoaded
复制代码
```

- 浏览器表单的自动填充也是在`DOMContentLoaded`期间执行的。

### `window.onload`

​		`onload`事件触发时，页面资源已经加载完成，因此可以使用 `window.onload`。

### `window.onbeforeunload`

​		在即将离开当前页面（刷新或关闭）时触发。该事件可用于弹出对话框，提示用户是继续浏览页面还是离开当前页面。

```javascript
document.addEventListener("beforeunload", alert('确认离开?'));
```

### `window.onunload`

​		当访问者离开页面时，`window` 对象上的 `unload` 事件就会被触发。我们可以在那里做一些不涉及延迟的操作，例如关闭相关的弹出窗口。

## `typeof `和`instanceof`区别

> 主要包括三个事件DOMContentLoaded、onload、beforeUnload和unload，DOMContentLoaded时，已加载完HTML数据生成了DOM树，但是一些样式、图片之类的外部文件仍未加载，可以进行一些初始化接口 ；onload时，加载完所有的文件资源；beforeUnload时，用户准备离开，可以进行一些确认保存数据的操作，unload用户离开时，可以做一些不涉及延迟的操作，比如关闭相关弹出窗口

`typeof` 操作符返回一个字符串，表示未经计算的操作数的类型,用于检测数据类型

`instanceof` 运算符用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上、

区别：

`typeof`与`instanceof`都是判断数据类型的方法，区别如下：

- `typeof`会返回一个变量的基本类型，`instanceof`返回的是一个布尔值
- `instanceof` 可以准确地判断复杂引用数据类型，但是不能正确判断基本数据类型
- 而`typeof` 也存在弊端，它虽然可以判断基础数据类型（`null` 除外），但是引用数据类型中，除了`function` 类型以外，其他的也无法判断

## for in/for of的区别

> for in是对可枚举属性获取键名
> for of是对可迭代对象获取键值
> 可枚举属性就是属性的enumerable的值为true
> 可迭代对象有Array、Set、Map、String，一个可迭代对象必须实现了iterator这方法并且还需要一个next，，在迭代器内部会不断调用next直到返回done这个属性为true
>
> for of不能遍历对象{}

## map和forEach可以通过break跳出吗

> 不能通过break跳出，会直接报错
> 可以通过try catch捕获错误终止循环

map和forEach本身不可以跳出循环，**使用break会报错**，可以抛出 new throw error() 通过try catch去捕获这个错误才可以终止循环

```js
let list=[1,2,3,4,5,6];
try{
    list.map(item=>{
        if(item===3){
            throw new Error()
        }
        console.log(item)
    })
} catch {}
// 1 2
```

## `js`如何判断数组类型

> Array.isArray()，instanceof，constructor，原型链的方式，Array.prototype.isprototypeOf()，Object.getprototypeOf(),Object.prototype.toString.call(obj)

```js
/*Object.prototype.toString()必须改变this指向是因为，toString内部是获取this指向那个对象的[[Class]]属性值的，如果不改变this指向为我们的目标变量，this将永远指向调用toString的对象*/

Object.prototype.toString = function () {
  console.log(this);
};
const arr1 = [];
Object.prototype.toString(arr1); // 打印(即this指向) Object.prototype
Object.prototype.toString.call(arr1); // 打印(即this指向) arr1
arr1.toString() // 打印(即this指向) arr1
```

- 通过`instanceof`做判断

```js
obj instanceof Array
```

- 通过原型链做判断

```js
obj.__proto__ === Array.prototype;
```

- 通过`ES6`的`Array.isArray()`做判断

```js
Array.isArray(obj);
```

- 通过`Object.prototype.toString.call()`做判断

```js
Object.prototype.toString.call(obj).slice(1) === 'Array';
```

- 通过`Object.getprototypeOf()`做判断

```
Object.getPrototypeOf(obj) === Array.prototype
```

- 通过`Array.prototype.isPrototypeOf()`

```js
Array.prototype.isPrototypeOf(obj)
```

## 操作数组元素的方法有哪些

> pop() push() shift() unshift() sort() splice() slice() key() value() join() reverse() contact()
>
> forEach() map() filter() some() every() reduce()

## *sort排序算法的本质

- `sort()`方法没有参数时，按照Unicode 码进行排序
- 通过给`sort()`的参数返回一个负值可以实现数组`reverse()`效果
- `sort(next,prev)` 参数返回 `next - prev`时，数组是升序，返回`-(next - prev)` 即`prev - next`时，数组是降序
- 通过以上的比较我们还是可以看出`sort()`方法效率还是挺高的，可以直接使用
- 一般情况下，对数组进行排序使用快速排序或者`sort()`,在已知数据规律时才考虑其他排序方式

```
sort方法接受一个“比较函数”作为参数。
如果调用该方法时没有使用参数，将按字母顺序对数组中的元素进行排序，说得更精确点，是按照字符编码的顺序进行排序。要实现这一点，首先应把数组的元素都转换成字符串（如有必要），以便进行比较。
如果想按照其他标准进行排序，就需要提供比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字。比较函数应该具有两个参数 a 和 b，其返回值如下：
若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
若 a 等于 b，则返回 0。
若 a 大于 b，则返回一个大于 0 的值。
```

## 如何拷贝一个对象

> 浅拷贝：解构赋值、Object.assign()、Array.prototype.slice
> 深拷贝：JSON.parse(JSON.stringify())
> 浅拷贝只是复制一个指针，不是复制本身，还是共享同一内存相当于分支
> 深拷贝重新开辟一个内存，复制一个一模一样的对象，修改新对象不会改变原对象，相当于引用

- `Object.assign()`：这是一个浅拷贝方法，可以将一个或多个源对象的所有可枚举属性复制到目标对象中。

```js
javascriptCopy codeconst original = { a: 1, b: 2 };
const copy = Object.assign({}, original);
```

- 解构赋值：这也是一种浅拷贝方法，它可以将源对象的所有属性复制到目标对象中。

```js
goCopy codeconst original = { a: 1, b: 2 };
const copy = { ...original };
```

- `JSON` 序列化/反序列化：这是一种深拷贝方法，可以将对象转换为 `JSON `字符串，再将字符串转换回对象。

```js
javascriptCopy codeconst original = { a: 1, b: 2 };
const copy = JSON.parse(JSON.stringify(original));
```

深拷贝和浅拷贝的区别：

浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存（“分支”）

深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象，是“值”而不是（“引用”）

![img](https://camo.githubusercontent.com/667a0c19bb77ec41c552350d2c8b4c4d1be9662acdd2f931504f214d00b9876f/68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f31353835363136392d323665326534613066633861333962342e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f772f3331302f666f726d61742f77656270)

![img](https://camo.githubusercontent.com/89821ea9eb62e96d8699acc8f24676a542d0ee2d3d8b71816b0f2e96f402c23a/68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f31353835363136392d383862643539373565616661613438382e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f772f3630302f666f726d61742f77656270)

## splice和slice会改变原数组吗？怎么删除数组最后一个元素？

> splice()会改变原数组，slice()不会
> pop()，push()重新声明一个数组 ，slice(-1)，splice(-1)

splice会改变原数组，slice不会

删除最后一个元素：

- 通过pop()
- 通过push()，声明一个新的数组

- 通过splice(-1)
- 通过silce(0,-1)

## 0.1+0.2为什么不等于0.3

> 因为计算机是通过二进制存储数据进行计算，0.1和0.2转换为二进制是两个无限循环的二进制数，转化为十进制就会造成误差

```js
let n1 = 0.1, n2 = 0.2
console.log(n1 + n2)  // 0.30000000000000004
```

这里得到的不是想要的结果，要想等于0.3，就要把它进行转化：

```js
(n1 + n2).toFixed(2) // 注意，toFixed为四舍五入
```

`toFixed(num)` 方法可把 Number 四舍五入为指定小数位数的数字。那为什么会出现这样的结果呢？

**因为计算机是通过二进制存储数据进行计算，0.1和0.2是两个无限循环的二进制数，转为十进制就会造成误差。**

对于这个问题，一个直接的解决方法就是设置一个误差范围，通常称为“机器精度”。对JavaScript来说，这个值通常为2-52，在ES6中，提供了`Number.EPSILON`属性，而它的值就是2-52，只要判断`0.1+0.2-0.3`是否小于`Number.EPSILON`，如果小于，就可以判断为0.1+0.2 ===0.3

```js
function numberepsilon(arg1,arg2){                   
  return Math.abs(arg1 - arg2) < Number.EPSILON;        
}        

console.log(numberepsilon(0.1 + 0.2, 0.3)); // true
```

## `==`和`===`、`Object.is()`有什么区别，

> ==会将两边数据类型转化为相同的数据类型再进行比较
>
> ===不会做强制类型转换，如果数据类型或者值不同直接返回falese
>
> Object.is()基本与===相同，处理了一些特殊情况比如0和-0，两个NaN是相等的

- 使用双等号（==）进行相等判断时，如果两边的类型不一致，则会进行强制类型转化后再进行比较。
- 使用三等号（===）进行相等判断时，如果两边的类型不一致时，不会做强制类型准换，直接返回 false。
- 使用 Object.is 来进行相等判断时，一般情况下和三等号的判断相同，它处理了一些特殊的情况，比如 -0 和 +0 不再相等，两个 NaN 是相等的。

## 解释` requestAnimationFrame/requestIdleCallback`，分别有什么用？

> 两者都是js内置函数，主要处理一些动画和任务的执行
> requestAnimationFrame()是动画帧函数，在下一次动画帧时被调用，可以用于更新动画
> requestIdleCallback()在浏览器空闲的时候执行，接收一个回调函数，可以处理一些数据分析等

`requestAnimationFrame` 和 `requestIdleCallback` 都是 JavaScript 中的内置函数，它们的目的是帮助浏览器更有效地管理动画和任务的执行。

`requestAnimationFrame` **函数用于动画的帧动画**。它告诉浏览器您需要在下一个动画帧中更新动画，以获得最流畅的帧速率。**它接受一个回调函数，该回调函数将在下一个动画帧时被调用，您可以在其中更新动画**。

`requestIdleCallback` **函数用于在浏览器空闲时执行任务**。它告诉浏览器您需要在浏览器空闲时执行任务，而不是在浏览器忙碌时。它接受一个回调函数，该回调函数将在浏览器空闲时被调用，您可以在其中执行不优先级很高的任务，例如**数据分析**等。

总的来说，如果您需要动画效果，可以使用 `requestAnimationFrame`；如果您需要在浏览器空闲时执行任务，则可以使用 `requestIdleCallback`。

## 事件流

> 事件流：事件在发生时会在目标节点和根节点之间按照特定的顺序，进行传播，传播所经过路径上所有节点都会接收到这个事件，这个过程就叫事件流。
> 事件流又有两个模型事件冒泡和事件捕获
> 三个阶段：冒泡阶段、目标阶段、捕获阶段
> 事件冒泡：当目标节点被触发，会由内向外div-body-html-document依次触发祖先节点同类型事件，直到DOM根节点
> 事件捕获：当目标节点触发，会由外向内从DOM根节点开始依次触发子孙节点事件
>
> 可以利用事件冒泡实现一个事件委托，事件委托可以提升页面性能
> 当一个父元素有多个子节点都要绑定一个事件的时候，可以将这个事件绑定在父元素上面，点击子元素时由于事件冒泡，它会触发这个事件

## 谈下事件循环机制（任务调度的模式）

> 事件循环是单线程的js在处理事件的一个循环过程，首先在执行栈中依次执行同步任务，调用后直接出栈，异步任务则先放到宿主环境中可以是浏览器也可以是node，合适的时机再到回调队列中，等到执行栈为空时，再执行回调队列中的异步任务，这个循环过程叫做事件循环
>
> 异步任务又分为微任务和宏任务，微任务是在当前事件循环的末尾，宏任务是放在下一轮事件循环。所有微任务总会在下一个宏任务之前全部执行完毕，宏任务必然是在微任务之后执行的（微任务实质上是宏任务其中的一个步骤）
>
> 宏任务：script（整体代码）、setTimeout、setInterval、I/O、UI交互时间、setImmediate(Node 环境)
>
> 微任务：Promise、MutationObserver、async/await、process.nextTick(Node 环境)

## *node事件循环与浏览器的哪些不一样

> node独有的异步形式：
>
> - I/O操作：异步加载本地文件
> - setImmediate()：与 setTimeout 设置 0ms 类似，在某些同步任务完成后立马执行
> - process.nextTick()：在某些同步任务完成后立马执行

首先，Node.js 是基于服务器的 JavaScript 环境，因此它不需要像浏览器那样处理复杂的 DOM 操作和渲染。它具有更快的 I/O 性能和更大的线程池，从而更好地支持高性能网络服务器。

另外，Node.js 中的事件循环是单线程的，而浏览器中的 JavaScript 则是多线程的。在 Node.js 中，所有的同步任务在主线程上完成，异步任务则由 libuv 库的线程池处理。这种单线程的架构让 Node.js 开发者不用考虑多线程环境下的线程同步问题

## *node require具体实现是什么

1. 先查找缓存：Node.js 会缓存已经加载过的模块，如果缓存中有该模块，则直接返回缓存中的模块。
2. 解析文件路径：如果没有在缓存中找到该模块，Node.js 会尝试解析文件路径。如果是以 `./` 或者 `/` 开头的路径，则认为是相对路径；否则认为是内置模块或者安装的第三方模块。
3. 加载文件：如果文件路径解析成功，Node.js 会读取该文件，并在文件的末尾自动加上一个包装函数。这个函数的作用是在该模块的作用域中执行文件代码。
4. 执行代码：Node.js 会执行文件的包装函数，并返回模块的导出对象。
5. 缓存模块：最后，Node.js 会缓存该模块，以便下次加载更快。

通过这些步骤，我们就可以在 Node.js 中方便地使用 `require` 加载模块。

## *require一个模块他是怎么定位的 

Node.js 在加载模块时会按照以下步骤定位模块：

1. 内置模块：首先 Node.js 会检查模块是否是内置模块，如果是，则直接返回该模块。
2. 文件模块：如果不是内置模块，Node.js 就会寻找该文件是否存在。如果存在，则加载该文件，并返回该模块。
3. 目录模块：如果上面的文件不存在，Node.js 会尝试将该模块视为目录，并尝试加载该目录中的 `package.json` 文件或 `index.js` 文件。

如果以上三个步骤都无法定位到该模块，则 Node.js 会抛出一个错误，表示找不到该模块。

## 前端模块化机制有哪些

> 前端模块化是将应用程序分为多个小的模块进行管理，每个模块互相独立，由自己的作用域，提高可维护性减少代码的冗余
> 常见的由ESMoudle、CommonJS、AMD等

​	前端模块化机制是一种将复杂的应用程序分解成许多小的模块来管理代码的方法。每个模块都是独立的，具有自己的作用域，并且可以被其他模块复用。这样可以保证代码的可读性和可维护性，并且还可以减少代码重复和冗余。

​	主要有CommonJS，AMD 和 ES6 Modules 等

## ES module、commonjs 的区别

> ESM import是对模块的引用，CJS require是对模块的浅拷贝，
> ESM引入的变量无法修改，CJS引入的变量可以修改
> ESM加载资源是异步的，CJS加载资源是同步的所以无法在浏览器中使用

​	ES6 Module和CommonJS模块的区别： 

- CommonJS是对模块的浅拷⻉，ES6 Module是对模块的引⽤，即ES6 Module只存只读，不能改变其值，也就是指针指向不能变，类似const；
- import的接⼝是read-only（只读状态），不能修改其变量值。 即不能修改其变量的指针指向，但可以改变变量内部指针指向，可以对commonJS对重新赋值（改变指针指向），但是对ES6 Module赋值会编译报错。 

​	ES6 Module和CommonJS模块的共同点： 

- CommonJS和ES6 Module都可以对引⼊的对象进⾏赋值，即对对象内部属性的值进⾏改变。 

## 说下 generator 原理

> generator生成器函数 返回一个迭代器（Iterator），可以控制函数执行。该函数执行时不会执行函数体，而是返回一个指向内部状态的指针，等到调用迭代器的next方法才会执行。在函数体中，遇到yeild关键字，函数会暂停，返回yeild后面的值给迭代器，并将指针指向下一个yield关键字，等待下一次next()方法调用
>
> Generator 函数可以多次暂停和恢复执行状态，因此可以用于实现异步编程的控制流，比如异步请求的顺序控制、协程等。

## 说下 Iterator 原理

> Iterator是一个对象，它提供了访问集合（数组、对象、set、map...）中各个元素的方式。
> 内部有一个next方法，该方法返回一个对象，有两个属性value，done,value是当前遍历的值，done用于判断遍历是否结束。
>
> ES6中可以通过Symbol.iterator来创建一个迭代器，该属性定义结合对象的原型上，并返回一个具有next方法的迭代器对象。当使用 for...of 循环遍历集合对象时，就会自动调用该迭代器对象的 next() 方法，直到 done 属性为 true，表示遍历结束

## 有哪些数据格式

> JSON、XML、FormData、CSV...

## JS由哪及部分组成

> ESMAScript核心：规定语法和基本对象
> DOM：文档对象模型，把整个页面规划成由节点层级构成的文档
> BOM：浏览器对象模型，与浏览器窗口打开的文档对象进行交互，用于操作浏览器本身的行为，例如，弹出新窗口、移动、调整或关闭当前窗口

## JS设计模式

> 发布订阅模式其实是一种对象间一对多的依赖关系，当一个对象的状态发生改变时，所有依赖它的对象都将得到状态改变的通知

- 单例模式：一个类只有一个实例，并提供一个访问他的全局节点
- 工厂模式：根据不同参数，返回不同类的实例
- 观察者模式：对象间一对多的依赖关系
- 发布订阅模式：对象间一对多的依赖关系
- 装饰者模式：在不改变原对象的基础上，增加新属性/方法/功能
- 策略模式

## 发布订阅模式和观察者模式的区别

> 发布订阅模式和观察者模式都是对象间一对多的依赖关系，其中发布订阅模式中，发布者和订阅者不直接通信，而是借助一个发布订阅中心进行统一管理，而观察者模式是一种直接依赖关系，观察者可以直接从被观察者中获取数据。

## 数组和类数组的区别

> 首先是数据类型的不同，类数组的数据类型实际上是一个普通object，而数组是Array，类数组不可以调用数组的方法
>
> 类数组转数组
>
> Array.prototype.slice.call(arr)
>
> Array.from(arr)
>
> [...arr]

## 删除对象的方法

1. delete 操作符
2. `obj.key = undefined;`  (等同于`obj[key] = undefined;`)

**这两种方法的区别：**

**1.** delete 操作符会从某个对象上移除指定属性，但它的工作量比其“替代”设置也就是 `object[key] = undefined ` 多的多的多。

并且该方法有诸多限制，比如，以下情况需要重点考虑：

- 如果你试图删除的属性不存在，那么delete将不会起任何作用，但仍会返回true
- 如果对象的原型链上有一个与待删除属性同名的属性，那么删除属性之后，对象会使用原型链上的那个属性（也就是说，delete操作只会在自身的属性上起作用）
- 任何使用 `var` 声明的属性不能从全局作用域或函数的作用域中删除。
  - 这样的话，delete操作不能删除任何在全局作用域中的函数（无论这个函数是来自于函数声明或函数表达式）
  - 除了在全局作用域中的函数不能被删除，在对象(object)中的函数是能够用delete操作删除的。
- 任何用`let`或`const` 声明的属性不能够从它被声明的作用域中删除。
- 不可设置的(Non-configurable)属性不能被移除。这意味着像`Math`, `Array`， `Object`内置对象的属性以及使用`Object.defineProperty()`方法设置为不可设置的属性不能被删除。

**2.** `obj[key] = undefined;` 这个选择不是这个问题的正确答案，因为只是把某个属性替换为`undefined`，属性本身还在。但是，如果你小心使用它，你可以大大加快一些算法。

## Object.freeze()和Object.seal()

```
Object.freeze()方法可以冻结一个对象。一个被冻结的对象再也不能被修改；冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。此外，冻结一个对象后该对象的原型也不能被修改。freeze() 返回和传入的参数相同的对象。
```

**Object.freeze()做了哪些事情？**

设置Object.preventExtension()，禁止添加新属性(绝对存在)
设置writable为false，禁止修改(绝对存在)
设置configurable为false，禁止配置(绝对存在)
禁止更改访问器属性(getter和setter)

```
Object.seal()方法封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。当前属性的值只要可写就可以改变。
```

**Object.seal()做了哪些事情？**

- 设置Object.preventExtension()，禁止添加新属性(绝对存在)
- 设置configurable为false，禁止配置(绝对存在)
- 禁止更改访问器属性(getter和setter)

**对比Object.freeze()和Object.seal()**

使用`Object.freeze()`冻结的对象中的现有属性是不可变的。用`Object.seal()`密封的对象可以改变其现有属性。**Object.seal()封闭比Object.freez()多一个writable:false**

## JS的特点

> 单线程
>
> 面向对象：封装、继承、多态
>
> 弱语言类型：变量类型不需要在声明的时候指定
>
> 跨平台：可以运行在多个操作系统和平台上，不受硬件和操作系统的限制

## JS为什么是单线程

> js作为浏览器的脚本语言，主要是实现用户与浏览器的交互，以及操作dom；如果js被设计为了多线程，一个线程修改dom元素，另一个线程要删除dom元素，浏览器就会一脸茫然，不知所措
>
> 为了利用多核CPU计算能力，HTML5提出web worker标准，允许js脚本创建多个线程，但是子线程完全受主线程控制，且不得操作dom

## 为什么要有异步操作

> js本身是单线程，也就意味折同一时间只能有一段代码片段执行。而浏览器环境中需要等待服务器响应、用户输入点击等事件发生。如果全部使用同步任务执行，那么程序就会出现阻塞，用户体验会非常差

## Promise

> Promise是异步编程的一种解决方案，传统的异步操作需要通过回调函数，如果我们需要发送发网络请求，根据第一次返回的结果发送第二次请求，一层一层的套下去就会造成回调地狱，代码就会看起来特别臃肿而且耦合度高，容易造成bug不好维护。而Promise就支持链式调用解决回调地狱的问题
>
> 缺点：一旦创建就不能中断；当处于pending状态时，无法得知当前是哪个状态；如果不设置回调函数，Promise内部抛出的错误就不会反应到外部

## async/await

> async await是基于Promise，一个语法糖，内部是generator+promise实现 async函数就是将Generator函数的星号（*）替换成async，将yield替换成await

## eval()函数

## null和undefined的区别

## 点击下载本地文件

- window.open(url, '_self')

- a标签有一个download属性  <a download='demo'></a>可以直接下载，属性值为下载的文件名

  封装createAlink方法

  ```js
  function creatAlink(fileUrl, fileName) {
    const anchor = document.createElement('a')
    anchor.href = fileUrl
    anchor.setAttribute('download', fileName)
    anchor.innerHTML = 'downloading...'
    anchor.style.display = 'none'
    document.body.appendChild(anchor)
    setTimeout(() => {
      anchor.click()
      document.body.removeChild(anchor)
      setTimeout(() => {
        self.URL.revokeObjectURL(anchor.href)
      }, 250)
    }, 66)
  }
  ```

  revokeObjectURL
  
  ## 点击复制文本
  
  Clipboard 点击复制
  
  ```
  npm install --save clipboard
  ```
  
  ```js
  clipboard() {
      const clipboard = new Clipboard('.copy-qb', {
          text: () => {
              return this.taskNum
          }
      })
      clipboard.on('success', e => {
          this.$message.success('复制成功！')
          // 清除选中
          e.clearSelection()
          clipboard.destroy()
      })
      clipboard.on('error', () => {
          this.$message.error('复制失败,请手动复制！')
          clipboard.destroy()
      })
  }
  ```

el-autocomplete 输入文字请求没有数据时下拉会闪动

```
<el-autocomplete
    v-model="form.custName"
    :fetch-suggestions="querySearchAsync"  输入文字请求数据的回调
    placeholder="请输入内容"
    @select="handleSelect"                  选择下拉的回调
    :popper-append-to-body="false"          讲下拉元素添加到body中
    :debounce="0"							延时防抖
    popper-class="custClass"/>				下拉的类名，后续获取节点
```

```js
querySearchAsync(queryString, cb) {
    this.custList = []
    const params = {
        keyword: this.form.custName
    }
    // 获取下拉元素
    let oop = document.querySelector('.custClass')
    if (queryString) {
        getGroupList(params).then((res) => {
            res.data.forEach((item) => {
                this.custList.push({
                    value: item.custName,
                    dictValue: item.custCode
                })
            })
            this.custList.length > 0 ? oop.style.display = "block" : ""
            cb(this.custList)
        })
    } else {
        this.custList = []
        cb(this.custList)
    }
},
```

```scss
::v-deep .el-autocomplete-suggestion {
    display:none;
}
```

el table 列宽自适应

## JS位运算

`<<`二进制左移

```
a<<b
9<<3  // 9 * 2^3 = 9 * 8 = 72
9<<2  // 9 * 2^2 = 9 * 4 = 36
```

`>>`二进制右移

```
5>>2
5换算成二进制向右移动两位
返回的符号位与第一个操作位的符号位相同

5 >> 1 相当于除以2舍弃余数
移动一位时才相当于除以2
```

`>>>`无符号二进制右移

```
填充的符号位为0
```

