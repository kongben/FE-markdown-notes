## typescript基本用法

### 0.ts的安装

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

### 5.interface接口

除了用类型别名`type`抽象代码，还可以使用`interface`达到同样目的。

```typescript
interface Lady{
    name:string,
    age:number
}
const objArr:Lady[] = [{name:'adam',age:12},{name:'adam',age:12}]
```

##### 接口和类型别名的区别

和类型别名的区别如下：

1. `interface`只能为对象，而`type`可以为其他类型诸如 string。

2. `interface`可以设置非必填项（这个特性导致`interface`比`type`更常用）

   ```typescript
   interface Lady{
       name:string,
       age?:number
   }
   const objArr:Lady[] = [{name:'adam',age:12},{name:'adam'}]
   ```

3. `interface`允许加入任意值

   ```typescript
   interface Lady{
       name:string,
       age?:number
       [propname:string]:any
   }
   const objArr:Lady[] = [{name:'adam',age:12},{name:'adam'},{name:'dd',sex:1}]
   ```

4. `interface`可以添加方法

   ```typescript
   interface Lady {
       name: string,
       say(): string
   }
   const objArr: Lady[] = [{
       name: 'adam', say() {
           return '112'
       }
   }, {
       name: 'adam', say() {
           return '112'
       }
   }]
   ```

### 6.类

#### 基本用法

```typescript
class Lady {
    name: string
    constructor(name: string) {
        this.name = name
    }
    getName(): string {
        return this.name
    }
}

let ab = new Lady('adam')

```

#### 访问类型

```typescript
class Man{
    public name:string = 'adam'
    private age:number =1
    protected sex:number = 1

    getAge():number{
        return this.age
    }
}
class Lady extends Man{
    say(){
        this.name // adam
        this.age // error
        this.sex // 1
    }
}

let a = new Man()
a.name // adam
a.age // error
a.sex //error
```

|          | public | private | Protected |
| :------: | :----: | :-----: | :-------: |
| 外部调用 | ✅       | ❌       | ❌ |
| 子类调用 | ✅ | ❌ | ✅ |

#### 继承

`extends`实现子类与父类的继承。

```typescript
class Man {
    name: string
    constructor(name: string) {
        this.name = name
    }
    getName(): string {
        return this.name
    }
}
class Lady extends Man {
    age: number
    constructor(name: string, age?: number) {
        super(name); //使用super调用父类构造函数
        this.age = age | 1
    }
}
let ab = new Lady('adam',23)
ab.age // 23
```

#### 重写

子类可以重写父类的方法。

```typescript
class Man {
    say(){
        return 'name'
    }
  }
class Lady extends Man{
    say(){
        return 'lady'
    }
}
let a = new Lady()
a.say() //lady
```

#### Getter和setter

```typescript
class Man {
    constructor(private _age:number){}
    get age(){
        return this._age
    }
    set age(age:number){
      this._age=age
    }
  }
  
  const dajiao = new Man(28)
  console.log(dajiao.age) // 28
  dajiao.age=25
  console.log(dajiao.age) //25
```

Get和Set分别时类里的属性钩子，当属性被读取和赋值时调用。

类的Static

通过`static`声明的属性和方法，可以不通过`new`来调用，实现静态调用。

```typescript
class Man {
    static say(){
        return 'name'
    }
  }
  
Man.say() //name
```

#### 只读属性

使用`readonly`关键词可以将类中属性设为只读，实例化后不可修改。

```typescript
class Man {
    public readonly name:string
    constructor(name:string){
        this.name = name
    }
  }

let a = new Man('adma')
a.name = 'hah' //error
```

#### 抽象类

抽象类的作用是规范子类的方法，规定这些方法的调用方式和返回值，在语言层面上保证编译通过与否，编写更具有泛用性的逻辑。

```typescript
abstract class Animal{
    abstract say():string
}
class Man extends Animal {
    say(){
        return 'hah'
    }
  }
class Dog extends Animal{
    say(){
        return 'wang wang wang'
    }
}
class Cat extends Animal{
    say(){
        return 'miao miao miao'
    }
}
```

#### 联合类型与类型保护（待续）

```typescript
interface Waiter {
  anjiao: boolean;
  say: () => {};
}

interface Teacher {
  anjiao: boolean;
  skill: () => {};
}

function judgeWho(animal: Waiter | Teacher) {
  if (animal.anjiao) {
    (animal as Teacher).skill();
  }else{
    (animal as Waiter).say();
  }
}
```

### 7.泛型

## 