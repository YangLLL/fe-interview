>[flex布局](#flex布局)  
>[css样式优先级](#css样式优先级)   
>[js事件循环](#js事件循环)  
>[实现继承的几种方式](#实现继承的几种方式)  
>[React组件生命周期](#react组件生命周期)  
>[算法题 - 二分查找](#二分查找)  

---
## flex布局
flexbox是一种一维布局模型（Grid是二维布局），flex布局的作用是设置容器内的项目怎么排列，分配空间。  
flex布局有两条轴线，主轴和交叉轴，主轴由 flex-direction 定义，交叉轴和主轴垂直。
容器通过设置 `display: flex | inline-flex` 来使用 flex 布局

flex布局的常用属性：
- flex
- flex-direction
- flex-wrap
- flex-flow
- justify-content
- align-items
- align-content

#### flex
flex的作用是设置容器内的项目，如何缩放来适应容器内的可用空间  
flex是 `flex-grow`，`flex-shrink`，`flex-basis` 的简写，flex元素是在 flex-basis 的基础上缩放的。

```
flex: <flex-grow> <flex-shrink> <flex-basis>
```
flex取值：
- initial: 相当于 `flex: 0 1 auto`，不会拉伸，可以收缩防止溢出  
- auto: 相当于 `flex: 1 1 auto`，flex 元素在需要的时候既可以拉伸也可以收缩  
- none: 相当于 `flex: 0 0  auto`，不可伸缩  
- 一个数字`<number>`：相当于 `flex: <number> 1 0`，`<flex-shrink>` 的值被假定为1，`<flex-basis>` 的值被假定为 0 。
`flex: 0`，相当于 `flex: 0 1 0`,  

#### flex-grow
设置 flex 增长系数，处理 flex 元素在主轴上增加空间的问题。  
如果所有的兄弟元素都有相同的 flex-grow 系数，那么所有的项目平分剩余空间，如果值不同，根据不同的 flex-grow 系数定义的比例来进行分配  

取值：
- 数字`<number>`：正数，负值无效，默认为0。设置为 0 不会超过 flex-basis 的尺寸

#### flex-shrink
设置 flex 元素的收缩规则，处理 flex 元素在主轴上收缩的问题。  
flex-shrink 只有在 flex 元素总和超出主轴的时候才会生效 

取值：
- 数字`<number>`：正数，不能设置负值。值越大，收缩的比例越大。

#### flex-basis
指定了 flex 元素在主轴方向上的初始大小，在这个初始值的基础上进行收缩  
取值：
- <'width'>：可以是固定值（平常width可取的值都可以，比如max-content），也可以是相对于父弹性盒容器主轴尺寸的百分数。默认为 auto，也就是元素内容的尺寸大小  
- content：基于 flex 的元素的内容自动调整大小

#### flex-directoin：设置主轴的方向  

#### flex-wrap：是否换行

#### justify-content：沿主轴的对齐方式

#### align-items：在交叉轴方向上的对齐方式
---

## css样式优先级
首先 `!important` 优先级最高  
然后 内联样式优先级 高于 外部样式  
然后 ID 选择器 > 类选择器 = 属性选择器 = 伪类选择器 > 标签选择器 = 伪元素选择器

---
## js事件循环

JavaScript 是单线程的，一个时间只能做一件事情，有多个任务要排队执行。
JS的任务分为同步任务和异步任务，同步任务在主线程上运行，会形成一个执行栈。

主线程外，事件触发程序还管理着一个任务队列，只要异步任务有了结果，就在任务队列中放入一个事件回调，一旦执行栈中的任务执行完毕，就会读取任务队列，将可执行的异步任务添加到执行栈中执行。

异步任务分为宏任务和微任务，每个宏任务都关联着一个微任务队列，在执行当前任务的过程中，如果有微任务产生，就会将微任务放入微任务队列中，等到当前的宏任务执行完成后，会先去查看微任务队列中是否有待执行的任务，如果有，就按顺序执行，直到微任务都执行完毕，再去任务队列中读取下一个任务，开始下一次的事件循环。


---
## 实现继承的几种方式
#### 原型继承  
借助原型实现继承，将子类的原型对象指向父类的实例
```
function Parent() {
    this.name = ['Parent']
}

Parent.prototype.getName = function() {
    return this.name
}

function Child() {}
// 直接使用Child.prototype = Parent.prototype Child的实例访问不到父类构造函数的属性 this.name
// child1.name 为 undefined
// 子类的原型指向父类的实例
Child.prototype = new Parent()

var child1 = new Child()
child1.name.push('child1')
child1.name // ["Parent", "child1"]

var child2 = new Child()
child2.name // ["Parent", "child1"]
```
父类构造函数中的引用类型（比如对象/数组），会被所有子类实例共享。其中一个子类实例进行修改，会导致所有其他子类实例的这个值都会改变

#### 构造函数继承
通过修改父类构造函数的 this 实现继承。
```
function Parent() {
    this.name = ['Parent']
}

function Child() {
    Parent.call(this)
}

var child = new Child()
child.name.push('child')
child.name // ["Parent", "child"]
var child2 = new Child()
child2.name //["Parent"]
```
这种方式解决了原型链继承中构造函数引用类型共享的问题，同时可以向构造函数传参（通过call传参）

缺点：
所有方法都定义在构造函数中，每次都需要重新创建（对比原型链继承的方式，方法直接写在原型上，子类创建时不需要重新创建方法）

#### 组合继承
结合原型继承和构造函数继承
```
function Parent() {
    this.name = ['Parent']
}
Parent.prototype.getName = function() {
    return this.name
}
function Child() {
    Parent.call(this)
}
Child.prototype = new Parent()
Child.prototype.constructor = Child

var child1 = new Child()
child1.name.push('child1')
child1.getName() // ["Parent", "child1"]

var child2 = new Child()
child2.getName() // ["Parent"]
```
同时解决了构造函数引用类型的问题，同时避免了方法会被创建多次的问题

缺点：
父类构造函数被调用了两次。同时子类实例以及子类原型对象上都会存在name属性。虽然根据原型链机制，并不会访问到原型对象上的同名属性，但总归是不美。

#### 组合继承优化，寄生组合式继承

```
function Parent() {
    this.name = ['Parent']
}
Parent.prototype.getName = function() {
    return this.name
}
function Child() {
    Parent.call(this)
}
Child.prototype = Object.create(Parent.prototype)
Child.prototype.constructor = Child

var child1 = new Child()
var child2 = new Child()

child1.name.push('child1')
child1.getName() // ["Parent", "child1"]
child2.getName() // ["Parent"]

// 修改子类构造函数
Child.prototype.getAge = function() {
    return 20
}
child1.getAge() // 20

var parent = new Parent()
parent.getAge() // Uncaught TypeError: parent.getAge is not a function
```

#### E6 继承
使用 es6 的 extends 实现继承，extends 本质上是 原型继承 的语法糖
```
class Parent {
    constructor() {
        this.name = 'parent'
    }
    getName() {
        return this.name
    }
}

class Child extends Parent {
    constructor(){
        super()
        this.age = 20
    }
}

var child = new Child()
child.name = 'child'
child.getName() //"child"
child.age //20 

var child2 = new Child()
child2.name //"parent"
```
## react组件生命周期

### class 组件的生命周期

#### 挂载阶段
- constructor
  获取初始化 props 和 state

- componentWillMount
  在组件首次渲染之前调用，用的比较少，首次渲染之前的逻辑可以放 constructor 中处理

- render
  组件渲染更新

- componentDidMount
  组件首次渲染之后调用，只会调用一次，可以在这里发请求

#### 更新阶段
- componentWillReceiveProps(nextProps)
  props 变化之后调用

- shouldComponentUpdate(nextProps, nextState)
  判断是否更新组件，返回 true 重新渲染，返回 false 不更新

- componentWillUpdate()
  组件更新之前调用

- render

- componentDidUpdate()
  组件更新之后调用

#### 卸载阶段
- componentWillUnmount
  组件销毁之前调用，可以做一些清理工作，如清除定时器，事件监听等

#### 新加的生命周期函数
- static getDerivedStateFromProps(nextProps, prevState)
  会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。
  它应该返回一个对象来更新 state，如果返回 null 则不更新任何内容。
  用来替代 componentWillReceiveProps，componentWillReceiveProps只会在父组件重新渲染的时候触发，getDerivedStateFromProps方法每次渲染之前都会触发（包括 props 和 state 更新)。

- getSnapSotBeforeUpdate()
  在最近一次渲染输出（提交到 DOM 节点）之前调用。使组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期方法的任何返回值将作为参数传递给 componentDidUpdate()。
  在 render 之后，DOM 更新之前调用，可以替代 componentWillUpdate

#### 过时的生命周期函数
- componentWillMount：组件挂载之前调用，基本用不到，初始化 state 的操作可以在 constructor 中处理
- componentWillReceiveProps：会在已挂载的组件接收新的 props 之前被调用
- componentWillUpdate：当组件收到新的 props 或 state 时，会在渲染之前调用，初始渲染的时候不会调用

### Hooks的生命周期

使用 useEffect 可以在函数组件中执行副作用操作。useEffect 会在每次渲染之后调用（第一次渲染，和后面更新都会调用），相当于 componentDidMount 和 componentDidUpdate
```
useEffect(() => {
  // ...副作用操作
}, dependency)
```
默认函数组件没有生命周期，使用 useEffect 可以模拟生命周期。
- 如果只在挂载的时候执行一次，dependency 就传空数组 []，表示不依赖任何状态，props 和 state 变化不会重新执行useEffect
- 模拟 componentDidUpdate：
    1. dependency 不传任何参数，那每次更新都会重新执行 useEffect 中的代码
    2. dependency 传入依赖的 props 或者 state 属性，当前依赖变了之后，才会执行 useEffect 中的代码
- 如果要清除一些副作用，可以在 useEffect 中返回一个函数，React 会在执行清楚操作的时候调用这个函数。模拟 componentWillUnMount 生命周期，但是和 componentWillUnMount 不完全相同

```
useEffect(() => {
    console.log('开始监听') // state,props改变时会执行
    
     // 并不完全等同于 componentWillUnmount，props发生变化也会执行结束监听。
     // 返回的函数会在下一次 effect 执行之前被执行，不是只有被销毁的时候执行，update的时候也会执行
    return () => {
        console.log('结束监听') 
    }
})
```


## 二分查找
给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。
```
var search = function(nums, target) {
    let min = 0
    let max = nums.length - 1
    let mid = Math.ceil(max / 2)
    while(min <= max) {
        if(target === nums[mid]) {
            return mid
        }else if(target > nums[mid]){
            min = mid + 1
        } else {
            max = mid - 1
        }
        mid = Math.ceil(min + (max - min) / 2)
    }
    return -1
};
```


