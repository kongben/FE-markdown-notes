## typescript基本用法

### ts的安装

#### node的安装

[nodejs](https://node.js.org/)点击下载安装

#### ts的安装

全局安装`ts`

`npm install typescript -g`

#### ts编译

> test.ts

```typescript
function test(){
    let a:string = 'aa'
    console.log(a)
}
test()
```

使用命令

`tsc test.ts `

就可以将`ts`文件编译成`js`文件，在使用`node test.js`就可以运行。输出`aa`。

### 1.变量的定义

#### 常用静态类型

##### 基础静态类型

```typescript
let a: number //数字
let b: string //字符串
let c: any //任何
let d: symbol //symbol
let e: boolean //布尔
let f: void //无
let g: null //null
let h: undefined //undefined
```

##### 复杂类型

```typescript
//对象类型
const man: {
  name: string,
  age: number,
} = {
  name: "adam",
  age: 18,
};

//数组类型
const boys: string[] = ['adam', 'bob']
const boys: string[] = ['adam', 2] //报错
const age: number[] = [1, 2]

//类类型
class Man {}
const boy: Man = new Man();

//函数类型

//报错 【返回值必须是string】
const bodyfuc: () => string = () => {
    return 11
}

const bodyfuc: () => string = () => {
    return 'adam'
}
```



ts的一个特点就是强类型语言，可以在变量声明阶段，声明变量类型。

使用`:`在变量后声明其类型。此后该变量不能转变为其他类型。

```typescript
let a: number = 1
let b: string
a = 2
b = 2 //报错 【不能将类型“number”分配给类型“string”。】
```

拥有静态类型的一个好处就是编辑器可以给当前变量提供对应类型的方法。

比如在`a`后使用`.`，编辑器能会弹出`toFixed`等`number`类型的方法,方便代码编写。

#### 自定义静态类型

```typescript
interface human{
    name:string,
    isMan:boolean
}

let man:human = {
    name : 'adam',
    isMan:true
}
console.log(man) //{ name: 'adam', isMan: true }
```

使用`interface`接口，来定义自动义静态类型。

#### 总结

+ 基本静态类型分为`string`、`number`、`any`、`symbol`、`boolean`、`null`、`undefined`
+ 复杂类型分为`对象`、`数组`、`类`、`函数`

+ 静态类型，意味着变量声明后，类型不可变，也意味着类型的方法和属性也确定下来。
+ 确定方法属性后，提高程序健壮性，可以使编辑器提供语法提示，提升开发效率。
+ 使用`interface`可以自动义需要的类型

### 2.类型注释和类型推断

#### 类型注释

即先设定变量的类型，在后面进行对应复制操作。

```typescript
let count: number; //类型注释
count = 123;
```

#### 类型推断

即如果TS能推断出变量类型，则可以不写类型注释。

```typescript
const one = 1;
const two = 2;
const three = one + two; //默认number

```
如果推断不出，则会默认给`one`、`two`一个`any`

```typescript
//不规范写法
function getTotal(one, two) {
  return one + two;
}
//规范写法
function getTotal(one:number, two:number) {
  return one + two;
}
```

### 3.函数的定义

##### 定义

上面所谓的规范写法，其实还是又一点问题。

```typescript
//规范写法
function getTotal(one:number, two:number) {
  return one + two;
}
//如果业务逻辑写错
function getTotal(one:number, two:number) {
  return one + two + '';
}
```

返回的就不是`number`，所有需要我们声明函数的时候可以确定返回类型。

```typescript
function getTotal(one: number, two: number): number {
  return one + two;
}
const total = getTotal(1, 2);
```

这样，如果业务代码返回类型出错，`TS`可以立马发现，报错。使代码更加严谨。

##### 返回类型

1. 当没有返回值的时候，使用`void`

   ```typescript
   //bad code
   function sayHello() {
     console.log("hello world");
   }
   //good code
   function sayHello(): void {
     console.log("hello world");
   }
   ```

2. 当无法返回的时候，使用`never`

   ```typescript
   function errorFuntion(): never {
     throw new Error();
     console.log("Hello World");
   }
   
   function forNever(): never {
     while (true) {}
     console.log("Hello JSPang");
   }
   ```

##### 对象参数的类型定义

`js`的函数

```js
function add({ one, two }) {
  return one + two;
}
const total = add({ one: 1, two: 2 });
```

`TS`写法

```typescript
function getTotal({ one, two }: { one: string, two: number }) {
    return one + two;
}
const total = getTotal({ one: 'aa', two: 3 });
```

### 4.类型别名

定义一个对象数组

```typescript
//对象数组类型
const objArr:{name:string,age:number}[] = [{name:'adam',age:12},{name:'adam',age:12}]
```

可以使用`type`定义一个类型的别名，将对象类型抽离出来,简化代码。

```typescript
type Lady = { name: string, age: number };
const objArr:Lady[] = [{name:'adam',age:12},{name:'adam',age:12}]
```

