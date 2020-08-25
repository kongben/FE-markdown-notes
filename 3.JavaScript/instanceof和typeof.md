### 重学前端：instanceof和typeof的爱恨纠葛

> 基础知识重新学习，若有不对欢迎指正，感恩。

在JavaScript世界中变量根据数据类型不同分两个阵营，一方是基本数据类型，一方则是对象。为了判断出变量的类型，就分别创造出两个**运算符**，**instanceof**和**typeof**

**`instanceof`** **运算符**用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上。

**`typeof`** 操作符返回一个字符串，表示未经计算的操作数的类型。

### 使用方法

```js
object instanceof constructor
typeof operand //一个表示对象或原始值的表达式 
```

### 返回值

- instanceof根据**否出现在某个实例对象的原型链上**返回`true`or`false`

- typeof 返回规则
  ![image-20200321005819096](/Users/zhouyefei1/Library/Application Support/typora-user-images/image-20200321005819096.png)

### 总结

- `typeof`仅能判断基本数据类型，当碰到引用数据类型只能返回`object`或者`funciton`

- `instanceof`则是用来判断构造函数是否是在实例对象的原型链中，所以可以判断构造函数是否是对象的子类。

  

  