
# 初入JS函数式编程的世界

**课程内容：**
- functional-program-01-about
- functional-program-02-why-fp
- functional-program-03-function-first-class
- functional-program-04-pure-function
- functional-program-05-currying
- functional-program-06-compose
- functional-program-07-higher-order-function
- functional-program-08-point-free-style

## functional-program-01-about：什么是函数式编程

> 什么是函数式编程，就是把函数本身上升到一等公民的地位，进行编程构建

### 什么是函数

定义：一般的，在一个变化过程中，有两个变量x、y，如果给定一个x值，相应的就确定唯一的一个y，那么就称y是x的函数，其中x是自变量，y是因变量，x的取值范围叫做这个函数的定义域，相应y的取值范围叫做函数的值域。

- 数学中的函数：函数就是从A到B的关系映射 -> `y = f(x)`
- js中的函数：多条语句组成的程序块 -> `var y = function ( x ) { }`

### 面向函数编程

- 面向过程编程：以过程为中心的编程思想，想到什么写什么
- 面向对象编程：从数据结构的角度出发
	- 具有相同特性（数据元素）和行为（功能）的对象的抽象就是类
	- 对象的抽象是类，类的实例是对象
	- 类实际上就是一种数据类型
- 面向函数编程：从算法角度出发，也就是从行为的角度出发，体现的一些编程原则：
	- 不要重复自己（don't repeat yourself）
	- 高内聚低耦合（loose coupling high cohesion）
	- 最小意外原则（Principle of least surprise）
	- 单一责任（single responsibility）

## functional-program-02-why-fp：为什么要学习函数式编程

- 为了更好的模块化
	- 模块化使得开发更快、维护更容易
	- 模块可以重用
	- 模块化便于单元测试和debug
- 掌握函数式编程有助于更好的理解和使用Rxjs、Redux等一些前端类库和框架

## functional-program-03-function-first-class：函数是一等公民

- 函数式编程的基本理念是以函数为核心来组织代码，很自然的，它首先将函数的地位提高，视其为“第一等公民” (first class)。
- 所谓一等公民，是指函数和其他数据类型拥有平等的地位，比如：
	- 函数可以赋值给变量
	- 函数可以被作为实参传递
	- 函数可以被另一个函数返回
	- 函数可以返回另一个函数
	- 函数可以作为形参

```
// 定义
var a = function(x){ return x + 1 }

// 赋值
var res = a( 1 )

// 实参传递
var b = function( a ) {
	return a(1) + 1
}

// 返回另一个函数
var c = function(a, cb){
	return function( c ) {
		cb()
		return c + 2
	}
}

function cb(){ }
```

## functional-program-04-pure-function：纯函数

> 纯函数：一个没有任何副作用，并且返回值只由输入决定的函数

### 两个例子

- slice & splice：slice是一个纯函数，splice是非函数
- add
```
// add 的非纯函数定义
var x = 5;
function add( y ){ return y + x }

// 如何定义一个纯函数
function addPure( x ){
  return function ( y ) {
     return y + x
  }
}
```

> 副作用：如果一个函数，除了返回值之外，还会修改某些其它状态，或者与外部函数等有可观测的交互

## functional-program-05-currying：函数的柯里化

>  只传递函数的一部分参数来调用它，让它返回一个函数去处理剩下的参数

### 接上一部分示例讲解

```

// 柯里化
function addPure( x ){
  return function ( y ) {
     return y + x
  }
}

var addTemp = addPure(5)
var res = addTemp(1) // 6
var res1 = addTemp(10) // 15

// 非柯里化
function addPure( x, y ){
   return y + x
}

// 柯里化结合ES6

const add = x => y => x + y

// redux middlreware
const loggerMiddleware = store => next => action => {
	// do some thing.
}
```

**好处：**
- 参数复用：利用柯里化，我们可以固定住其中的部分参数，在调用的时候，这个参数就相当于已经被记住了，不需要再次传递，也就是我们这里说的参数复用。
- 延迟执行：不断的柯里化，累积传入的参数，最后执行。

> **函数柯里化给我们带来的是：解决问题的一种逻辑思维方式。**

- 性能
- 复杂度

## functional-program-06-compose：函数的组合

- 当函数纯化之后，有一个很鲜明的特点是，这个函数变的可以组合了。我们可以像堆积木一样，把各个我们要用的函数堆起来变成一个更大得函数体。
- 函数可组合之后，要解决的问题就是如何组合。

### 函数嵌套

学会了使用纯函数以及如何把它柯里化之后，我们会很容易写出这样的**嵌套式**代码：

```

// 很清晰
const f = x => x +1
const g = x => x + 2
const h = x => x + 3

var a = h(g(f(x)));
```
如果嵌套太多，代码不好理解，逻辑很难掌控。

### 示例：定义compose函数实现函数的组合使用

```
// 简易compose函数的定义
var compose = function(f, g) {
    return function(x) {
        return f(g(x));
    };
};

// 或者使用es6的箭头函数定义compose
var compose = (f, g) => (x => f(g(x)));

var add = x => x + 1;
var multiple = x => x * 5;

var m = compose(multiple, add)(2);
// add出来的结果：3
// multiple出来的结果：15

// 如果没有compose组合
var n = mutilpe(add(2))
var a = multiple(add(add1(add2(add3(2)))))
```

两个函数组合使用的结果是，从右往左的依次调用了传入compose的两个方法，实现了值的计算

### 不同的类库对compose方法有不同的实现

以redux为例，compose源码：

```
/*
 * from right to left. For example, compose(f, g, h) is identical to doing
 * (...args) => f(g(h(...args))).
 */

export default function compose(...funcs) {
  if (funcs.length === 0) {

    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }

  const last = funcs[funcs.length - 1]
  const rest = funcs.slice(0, -1)
  return (...args) => rest.reduceRight((composed, f) => f(composed), last(...args))
}


const demo = (composed, f) => {
	f(composed);
	return last(...args)
}

compose(fn1, fn2, fn3)()
```

redux调用compose的姿势：

```
// applyMiddleware.js
dispatch = compose(...chain)(store.dispatch)
```


## functional-program-07-higher-order-function：高阶函数

> 以一个函数为参数，同时返回一个函数作为函数的返回值

### 简单示例理解高阶

```
const fn = x => x + 1

// es6
const highOrderFn = x => y => {
	console.log( y )
	return x(y)
}

// es5
highOrderFn( x ) {
	return function(y){
		console.log( y )
		return x(y)
	}
}

const middle = store => next => action => {

}

// 调用
var a = highOrderFn(fn)
var b = a(2)() // 3

```

### 实践：react中的高阶组件

```
class Comp extends Component {
	render(){
		return <h1> compo </h1>
	}
}

// high order component
const Mixins = ExampleComp => class extends Component {
	constructor(props){
		this.state = { name: "demo" }
		super(props)
	}
	fn(){
	}
	render(){
		<ExampleComp
			{...this.state}
			{...this.props}
			fn = {this.fn}
		/>
	}
}

const MyComponent = Mixins(Comp)
```

## functional-program-08-point-free-style：point-free代码风格

> 减少不必要的命名，让代码保持简洁和通用
