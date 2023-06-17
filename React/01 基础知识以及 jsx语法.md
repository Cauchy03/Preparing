## 一、React 简介

### 1. 关于 React

整几个面试题来认识一下~~

> 什么是 React ？

**React** 是一个用于构建用户界面的 JavaScript 库。

- 是一个将数据渲染为 HTML 视图的开源 JS 库
- 它遵循基于组件的方法，有助于构建可重用的 UI 组件
- 它用于开发复杂的交互式的 web 和移动 UI

> React 有什么特点？

1. 使用虚拟 DOM 而不是真正的 DOM
2. 它可以用服务器渲染
3. 它遵循单向数据流或数据绑定
4. 高效
5. 声明式编码，组件化编码

> React 的一些主要优点？

1. 它提高了应用的性能
2. 可以方便在客户端和服务器端使用
3. 由于使用 JSX，代码的可读性更好
4. 使用React，编写 UI 测试用例变得非常容易

### 2. Hello React

首先需要引入几个 react 包，我直接用的是老师下载好的

- React 核心库、操作 DOM 的 react 扩展库、将 jsx 转为 js 的 babel 库

![react1](https://ljcimg.oss-cn-beijing.aliyuncs.com/img/react1.png)

```jsx
const VDOM = <h1>Hello,React</h1>
ReactDOM.render(VDOM,document.querySelector(".test"))
```

### 3. 虚拟 DOM 和真实 DOM 的两种创建方法

#### 3.1 JS 创建虚拟 DOM

```js
//1.创建虚拟DOM,创建嵌套格式的dom (标签,标签属性,内容)
const VDOM = React.createElement('h1',{id:'title'},React.createElement('span',{},'hello,React'))
//2.渲染虚拟DOM到页面
ReactDOM.render(VDOM,document.querySelector('.test'))
```

#### 3.2 Jsx 创建虚拟DOM

```jsx
//1.创建虚拟DOM
	const VDOM = (  /* 此处一定不要写引号，因为不是字符串 */
    	<h1 id="title">
			<span>Hello,React</span>
		</h1>
	)
//2.渲染虚拟DOM到页面
	ReactDOM.render(VDOM,document.querySelector('.test'))
```

> js 的写法并不是常用的，常用jsx来写，毕竟JSX更符合书写的习惯

## 二、jsx 语法

1. 定义虚拟DOM，不能使用`“”`

2. 标签中混入JS表达式的时候使用`{}`

```jsx
id = {myId.toUpperCase()}
```

3. 样式的类名指定不能使用class，使用`className`

4. 内敛样式要使用`{{}}`包裹

```jsx
style={{color:'skyblue',fontSize:'24px'}}
```

5. 不能有多个根标签，只能有一个根标签

6. 标签必须闭合，自闭合也行

7. 如果小写字母开头，就将标签转化为 html 同名元素，如果 html 中无该标签对应的元素，就报错；如果是大写字母开头，react 就去渲染对应的组件，如果没有就报错

> 关于JS表达式和JS语句： 
>
> JS表达式：返回一个值，可以放在任何一个需要值的地方  a  a+b  demo(a)  arr.map() function text(){}
> JS语句：if(){} for(){} while(){} swith(){} 不会返回一个值

#### 1. 注释

写在花括号里

```jsx
ReactDOM.render(
    <div>
    <h1>小丞</h1>
    {/*注释...*/}
     </div>,
    document.getElementById('example')
);
```

#### 2. 数组

JSX 允许在模板中插入数组，数组自动展开全部成员

```jsx
var arr = [
  <h1>小丞</h1>,
  <h2>同学</h2>,
];
ReactDOM.render(
  <div>{arr}</div>,
  document.getElementById('example')
);
```

### tip: JSX 小练习

根据动态数据生成 `li`

```jsx
const data = ['A','B','C']
const VDOM = (
    <div>
        <ul>
            {
                data.map((item,index)=>{
                    return <li key={index}>{item}</li>
                })
            }
        </ul>
    </div>
)
ReactDOM.render(VDOM,document.querySelector('.test'))
```

