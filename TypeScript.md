```
interface vs type，enum，然后内置的实用类型
```

## 1. 什么是TypeScript？

> TS是一个强类型的JS超集，主要用于类型检查，支持面向对象的概念，如类、接口、继承、泛型等。TS并不直接运行在浏览器，而是需要通过编译器编译成JS运行

## 2. 为什么要使用 TypeScript ? TypeScript 相对于 JavaScript 的优势是什么？

> 类型检查，增加了静态类型，可以在开发人员编写脚本时检测错误。对于大型项目，有时为了优化改进项目，对代码进行小小更改。这些小小的变化可能会产生严重的、意想不到的后果。使用TS工具来重构更容易快捷
>
> 更好的协作，为开发团队创建一个更高效的编码和调试过程

## 3. TypeScript 中 const 和 readonly 的区别？枚举和常量枚举的区别？接口和类型别名的区别？面向对象

> const 和 readonly：const可以防止变量的值被修改，readonly可以防止变量的属性被修改
>
> 枚举和常量枚举：常量枚举只能使用常量枚举表达式，并且不同于常规枚举，它们在编译阶段会被删除。常量枚举成员在使用的地方会被内联进来。之所以可以这么做是因为，常量枚举不允许包含计算成员
>
> 接口和类型别名：两者都可以用来描述对象或函数的类型。与接口不同，类型别名还可以用于其他类型，如基本类型，联合类型，元组

```
type和interface都是用来描述对象或者函数的类型，type还可以描述一些基本类型，联合类型，元组，但interface不行。type只是个类型取个别名，而interface则是类型的声明。多个interface声明还可以自动合并
```

### type和interface的区别

**type的使用**

作用：`type`学名为类型别名，即给其他类型取别名。关键字type可以用来声明接口，type描述基本类型，如string、number、boolean、null、undefined、bigint、symbol

```ts
//接口定义对象
type k = {
    a1: number,
    a2: string,
    a3?: boolean,  //可选属性：即a3属性可有可无。如果有，则为boolean类型
    readonly a4?: number,    //readonly只读属性，只能在创建的时候被赋值
}

const k1: k = {
    a1: 1,
    a2: "222",
    a4: 333     //初始化时被赋值，不会报错
}

//k1.a4=3333   //若初始化时已赋值，又再此赋值给a4，将会报错

//接口定义函数
type Add = (x:number,y:number) => number;

<!--带有属性的函数的声明方式-->
type Add = {                      //1.类型声明用type
       (x:number,y:number) : number
       prop : string 
};

const Add1: Add = (a,b) => {     //Add1的编译将无法通过
       return a*b
}
Add1.prop = 'hello'              //2.声明后给它赋个值，如果不加此句话

console.log(Add1)
```

**interface的使用**

作用：使用关键字interface声明接口，描述对象的属性，用来定义`对象的类型`和`函数的类型`

```ts
//接口定义对象
interface k {                  //定义了一个接口 k
    a1: number,
    a2: string,
    a3?: boolean,
    readonly a4?: number,
}

const k1: k = {               //接着定义了一个变量 k1，它的类型是 k
    a1: 1,
    a2: "222",
    a4: 333
}

//接口定义函数
<!--带有属性的函数的声明方式-->
interface AddFuc { 
        (x: number, y: number): number;
        prop : string
        
}
const AddFuc1: AddFuc = (a,b) => {     //Add1的编译将无法通过
       return a*b
}
AddFuc1.prop = 'hello'  //2.声明后给它赋个值，如果不加此句话

console.log(Add1)
```

> 1、interface只描述对象，type则描述所有数据
>
> 2、type只是别名，interface则是类型声明
>
> ```css
> type A = string    //A不真实存在
> const B = A        //B≠A，B为string类型
> 
> interface D extends Date { }   //D真实存在的类型
> type E = D    //E也为D的类型
> ```
>
> 3、type不可重新赋值
>
> ```typescript
> type A = string   //A初始化为number，其将一直是number类型（类似const）
> A = number   //报错
> ```
>
> 4、`type` 可以用于创建类型别名，即给一个类型起个新名字，而interface不行
>
> ```ts
> type myString = string；
> const a : myString = 'Hi'
> console.log(a)
> ```
>
> 5、`type`可以用于声明联合类型，而interface不行
>
> ```ts
> type luckynumber = number | string 
> ```
>
> 6、`interface`可以用于接口的合并（自动合并），而type不行。`原因是：interface只描述对象，type则描述所有数据。在对象里加个属性，interface不会改变原有的逻辑，是可以扩展的；而type有可能是基本类型，如string类型，其是个整体的值，没有增加属性的可能性，故无法扩展。`当type用于合并接口时，会报 **重复定义** 的警告，因此type无法实现接口的合并
>
> ```ts
> interface  person {
>         name:string
>         age:number
>  } 
>  
> interface  person {
>        sex:string
> } 
> 
> //合并后
> interface  person {
>        name:string
>        age:number
>        sex:string
> } 
> ```
>
> 注意，**合并的属性，其类型必须是唯一的**。可重复声明属性，但类型必须一致，不然会报错。如下情形所示：
>
> ```tsx
> interface  person {
>         name:string
>         age:number
>  } 
>  
> interface  person {
>        name:string    //重复但类型一致，不会报错
>        sex:string
> } 
> ```
>
> ```ts
> interface  person {
>        name:string
>        age:number
>  } 
>  
> interface  person {
>        name:number    //重复但类型不一致，会报错
>        sex:string
> } 
> ```
>
> 总结：
>
> 官方推荐用 `interface`，其他无法满足需求的情况下用 `type`。
>
> 但其实，因为 联合类型 和 交叉类型 是很常用的，所以避免不了大量使用 `type` 的场景，一些复杂类型也需要通过组装后形成类型别名来使用。
>
> 所以，如果想保持代码统一，还是可选择使用 `type`。通过上面的对比，类型别名 其实可涵盖 `interface` 的大部分场景

## 泛型

使用<T>，这里的T是一个抽象类型，一开始不知道具体的数据类型，只有在调用的时候才能确定它的值。

```ts
function identity<T>(arg: T): T {
  return arg;
}
```

其中T代表[Type]，在定义泛型时通常用作第一个类型变量名称。实际上T可以用任何有效名代替。常见的泛型变量代表：

> - K（Key）：表示对象中的键类型
> - V（Value）：表示对象中的值类型
> - E（Element）：表示元素类型

## 内置使用类型

- **never** **代表不可达**，比如函数抛异常的时候，返回值就是 never
- **void** 代表空，可以是 undefined 或 never
- **any** 是任意类型，任何类型都可以赋值给它，它也可以赋值给任何类型（除了 never）
- **unknown** 是未知类型，任何类型都可以赋值给它，但是它不可以赋值给别的类型
- **Tuple** 表示元组类型，它是一个固定长度的数组，其中每个元素可以是不同的类型
- **泛型** 抽象类型

## any 和 unknown 的区别

any 和 unknown 都代表任意类型，但是 unknown 只能接收任意类型的值，而 any 除了可以接收任意类型的值，也可以赋值给任意类型（除了 never）。类型体操中经常用 unknown 接受和匹配任何类型，而很少把任何类型赋值给某个类型变量。