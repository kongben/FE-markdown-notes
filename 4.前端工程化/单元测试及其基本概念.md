### 什么是单元测试？

> 单元测试(Unit Test)作为持续集成实现中的一环，位于金字塔模型的底部，目标是证明代码的某个单元（被测试的主体）能按照预期工作，这样我们在开发过程早期就能发现问题。

上面是官方解释，说人话就是：

盖房子的话必须所有的用料都是合格的，比如钢筋、砖头、水泥等，就要每一条每一块没一公斤都要合格，要不然盖起来的房子就可以某个地方有问题。

### 为什么需要单元测试？

在程序中，函数是构成的最小颗粒，只有没有函数都是健壮的，才能保证整体程序的质量。而单元测试就是对函数进行自动化的测试，来确保代码输出的是想要的结果。

### 单元测试的好处

1. 在联调前对代码有信心
2. 出现的问题都在单测中覆盖避免重复犯错，这样每次修改都有信心
3. 避免新人之后的更改无意中 break 一些事情，对别人的更改也有信心
4. 重构时候有信心

### 常见的单元测试库

1. jest，功能最全的测试运行器，facebook出品。需要的配置最少，内置了**jsDOM**和**断言**库，开箱即用。
2. Mocha，读作摩卡。另一个比较流行的JavaScript测试框架，需要自己配置包括断言库之类的其他工具。

### 单元测试使用方法及基本概念

以mocha为例，讲讲单元测试中涉及的基本概念和基本使用方法。

#### 官方文档

[document](https://mochajs.org/)

#### Mocha 的安装与配置

全局安装Mocha

```bash
npm install -g mocha
```

初始化项目及项目中安装Mocha

```bash
npm init
npm install --save-dev mocha
```

在**package.json**中加入下面脚本：

```json
"scripts": {
    "test": "mocha"
}
```

目录结构：

![img](https://pic3.zhimg.com/80/v2-a8b8d4e82215e25433bd61d7593d1b6e_1440w.jpg)

***测试文件放在test目录下，mocka运行时会执行test目录下的所有.test.js文件。**

我们以最简单的addSum为例。写一个测试用例。

>src/index.js

```js
function addSum(a,b){
	return a + b;
}
```

> test/index.test.js

```js
let addsum = require('../src/index')
let expect = require('chai').expect

describe('测试index.js',function(){
 describe("测试sum函数",function(){
it('两数之和', function() {
       if(addNum(1,2)!==3){
         throw new Error("两数相加结果不为两个数字的和")；
       }
    });
})
```

解析一下以上代码的语法：

测试脚本里面应该包括一个或多个describe块，每个describe块应该包括一个或多个it块。

describe块称为"测试套件"（test suite），表示一组相关的测试。

describe是一个函数，第一个参数是测试套件的名称（"测试index.js"），第二个参数是一个实际执行的函数。
it块称为"测试用例"（test case），表示一个单独的测试，是测试的最小单位。它也是一个函数，第一个参数是测试用例的名称（"两数之和"），第二个参数是一个实际执行的函数。

在上面的例子中，测试addNum函数，如果运行错误就抛出异常

此时运行

```bash
npm test
```

![测试结果-通过](https://pic4.zhimg.com/80/v2-f8a9a86937a5b1da3b8d47a0c62457db_1440w.png)

输入1，2的两数之和用例通过（请忽略，not、key等为展示的用例）。

![测试结果-通过](https://pic1.zhimg.com/80/v2-137be0bd38c96f195343ad3d8de00750_1440w.jpg)

测试结果-不通过，这里是oneof用例出现问题（上述代码未展示具体用例逻辑），导致失败。

#### 断言库chai

在上面的Mocha例子中，两数之和测试，测试失败用抛异常来处理，多少有点繁琐，所以就有了断言库的出现。
这里我们介绍一个常用的断言库chai。

断言库可以简单理解为这就是对我们上面抛异常方法的一个封装，当判断失败时会抛出一个异常。

chai基本概念：

+ [官方文档](https://www.chaijs.com/guide/)
+ Chai的基本使用

首先安装chai：

```bash
npm install --save-dev chai
```

在断言库的帮助下，我们可以将**test/index.test.js**代码优化一下：

```js
let addsum = require('../src/index')
let expect = require('chai').expect

describe('测试index.js',function(){
	describe("测试sum函数",function(){
		it('两数之和',function(){
 		// expect(addsum(1,2).to.be.equal(3))
 			expect(addsum(1,2)).equal(3);
 		})
		it('not',function(){
 			expect(1).not.equal(32)
		})
 		it('key',function(){
 			expect({ foo: 1, bar: 2, baz: 3 }).any.key('foo')
  	})
 		it('not',function(){
 			expect(false).to.be.false
  	})
 		it('oneof',function(){
 			expect(3).to.be.oneOf([3,1,2])
  	})
	})
})
```

如果不使用断言库，在具体用例写判断来抛出异常的话整个代码会繁琐很多。

#### Mocha的更多使用

如果想测试单一的测试js，可以在**package.json**用：

```json
"scripts": {
    "test": "mocha test/index.test.js"
}
```

或者多个js

```json
"scripts": {
    "test": "mocha test/index.test.js test/add.test.js"
}
```

当然也可以用通配符测试某个文件夹下所有的js和jsx：

```json
"scripts": {
    "test": "mocha 'test/some/*.@(js|jsx)'"
}
```

#### Mocha与ES6

在上面addNum和我们用的测试用例并非是ES6的语法，如果设计到ES6语法就会报错。

那么让我们把其中的代码都改为ES6的语法。

```js
import {assert} from 'chai'
import addNum from '../src/index'

describe('测试index.js',()=> {
  describe('测试addNum函数', ()=> {
    it('两数相加结果为两个数字的和', ()=> {
      assert.equal(addNum(1,2),3)
    })
  })
}
```

我们需要先安装一下babel：

```text
npm install babel-core babel-preset-es2015 --save-dev
```

然后，在项目目录下面，新建一个.babelrc文件：

```json
{
  "presets": [ "es2015" ]
}
```

接着讲package.json中的脚本改为：

```json
"scripts": {
  "test": "mocha --require babel-core/register"
},
```

其中--compilers参数用来指定测试脚本的转码器，这行命令的意思是运行mocha时先用babel-core/register模块，处理一下.js文件

#### Mocha测试用例执行的超时和高亮

Mocha默认每个测试用例最多执行2000毫秒，如果到时没有得到结果，就报错。以下命令设置超时时间为5000：

```css
mocha -t 5000 index.test.js
```

Mocha默认会高亮显示超过75毫秒的测试用例，以下命令设置高亮判断的临界值：

```css
mocha -s 1000 index.test.js
```

#### Mocha测试的钩子

Mocha在describe块之中，提供测试用例的四个钩子。它们会在指定时间执行。

```js
describe('测试index.js',()=> {
  before(()=>console.info("在本区块的所有测试用例之前执行"))

  after(()=>console.info("在本区块的所有测试用例之后执行"))

  beforeEach(()=>console.info("在本区块的每个测试用例之前执行"))

  afterEach(()=>console.info("在本区块的每个测试用例之后执行"))

  describe('测试addNum函数', ()=> {
    it('两数相加结果为两个数字的和', ()=> {
      assert.equal(addNum(1,2),3)
    })
  })
})
```

### One More Thing

上面的代码只是一个快速理解单元测试和基本用法的guide，如何在工程中使用单元测试呢？

#### Vue Test Utils

Vue官方有很好用的工具库[Vue Test Utils](https://vue-test-utils.vuejs.org/zh/)。

有比较完善的文档和提供了许多很实用的API，特别是同步了DOM更新，使我们在编写用例时，不会造成nextTick满天飞的情况出现。

Vue Test Utils支持以下测试运行器。

1. jest
2. mocha+chai
3. karma

具体可以查看[官方文档](https://vue-test-utils.vuejs.org/zh/guides/#%E8%B5%B7%E6%AD%A5)

也有一个按照官方文档跑起来的demo

[demo](https://github.com/kongben/vue-unitTest-demo)

