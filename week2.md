>[http和https](#http和https)  
>[作用域和闭包](#作用域和闭包)   
>[promise的实现原理](#promise的实现原理)  
>[数组的方法有哪些，es6新增的方法有哪些？怎么实现数组去重](#数组的方法有哪些es6新增的方法有哪些怎么实现数组去重)  
>[vue的响应式是怎么实现的？](#vue的响应式是怎么实现的)  
>[算法题 - 两数之和](#两数之和)

## http和https

HTTP 是一个用于传输超媒体文档（如 html）的应用层协议。用于在浏览器和服务器之间传输文档。
HTTP 的特点：
- 简单快速、灵活：每个资源（uri）是固定的，通过一个http协议就可以传输不同类型的数据
- 无状态：不会对请求和响应之间的通信状态进行保存
- 无连接：连接一次就会断开，每次请求都要通过 TCP三次握手和服务器重新建立连接

http 是无状态协议，请求状态不会被保存。使用Cookies可以创建有状态的会话

HTTP在请求过程中使用的是明文，不安全。
HTTPS 是 在HTTP基础上加上加密和认证等机制，比 HTTP 更加安全。HTTP 通信的部分接口使用 SSL 和 TLS 协议代替。

## 作用域和闭包

作用域是函数或者变量的有效作用范围。  
当我们在查找一个变量的时候，会首先从当前作用域查找，如果找到就停止查找，返回变量值。如果没有找到，就在外层嵌套的作用域查找，再没找到继续向上查找，这种嵌套的作用域关系就是作用域链。

当一个函数中嵌套了另一个函数，并且外部函数中返回了内部函数，就形成了闭包。

这样我们在调用外部函数之后，通过返回的内部函数的引用，依然可以访问到外部函数内定义的变量，这就是闭包

闭包的特点：  
变量一直保存在内存中，不会被垃圾回收机制回收。

## promise的实现原理
Promise 是异步编程的一种解决方案。使用 Promise对象，可以将异步操作以同步操作的流程表达出来，避免了层层嵌套回调函数。

Promise有三个状态:
- 待定（pending）: 初始状态，既没有被兑现，也没有被拒绝。
- 已兑现（fulfilled）: 意味着操作成功完成。
- 已拒绝（rejected）: 意味着操作失败。

首先在 new Promise 的时候，要传入一个函数，传入的函数会立即执行。函数有两个参数，resolve 和 reject。  调用 resolve，状态会由 pending 变为 fulfilled，然后执行成功的回调；  调用 reject，状态会由 pending 变成 rejected，执行失败的回调。  
promise还有一个then方法，接收两个参数，onResolved 和 onRejected，分别表示成功和失败的回调。  
创建promise 实例的时候，如果传入函数是异步的，在调用 then 的时候，状态还是 pending，这时会先将成功和失败的回调分别放到一个队列中，等异步任务执行完成，调用 resolve/reject 的时候执行。  
如果传入的函数是同步的，在调用then的时候，状态已经变成了 fulfilled 或 rejected，如果是 fulfilled 状态，就执行成功的回调，如果是 rejected 就执行失败的回调。


## 数组的方法有哪些，es6新增的方法有哪些？怎么实现数组去重

数组常用的方法：pop、push、shift、unshift、splice、slice、map、find、filter、concat、forEach、findIndex、indexOf、reduce、reverse、every、some、join

slice和splice 的区别：
- slice(start, end)：从 start 开始截取到 end，包括start，不包括 end，返回截取出来的元素，原数组不会被改变
- splice(start, deleteCount, item1, item2,...)：用来截取，添加元素。从 start 开始截取多个元素，返回被截取的元素，会影响原数组。

es6新增的方法：Array.from、Array.of、find、findIndex、some、every

对数组 arr 实现去重：  
使用 es 的 set
```
[...new Set(arr)]
```
```
Array.from(new Set(arr))
```

用数组的方法
```
function unionArr() {
  let result = []
  for (let i = 0; i < arr.length; i++) {
    if(result.indexOf(result) !== -1) {
      result.push(arr[i])
    }
  }
  return result
}
```

## vue的响应式是怎么实现的
#### Object.defineProperty()实现响应式
vue2.0是使用 Object.defineProperty() 来实现对 data 的监听。  
在定义 data 的时候，会定义属性的 setter 和 getter 方法。
在渲染的时候，通过 getter 收集依赖，添加到 watcher 中。在修改属性值的时候，触发 setter，通知 watch，使相关组件重新渲染。

如何实现对对象的监听：  
使用递归，如果是对象就继续调用defineProperty，重新定义对象

对数组的监听：  
重新定义数组原型，创建一个新对象，将原型指向原来的数组原型，在这基础上扩展数组的方法，然后将需要被监听的数组的原型赋值为我们自己定义的数组原型。

使用 Object.defineProperty() 的缺点：  
- 深度监听，要一次性递归到底，对象里面的对象也要进行监听，如果对象比较大，一次性计算量会很大
- 不能监听增加，删除属性（设置新的响应式属性要使用 Vue.set 和 Vue.delete 来设置)
- 无法原生监听数组，需要特殊处理


#### Proxy 实现响应式
vue3.0 使用了 Proxy 来实现响应式。原理和 vue2.0 类似，也是重新定义 data，对属性读取、修改、删除 进行拦截，


Proxy实现响应式的优点：
- 深度监听，性能更好：在 get 的时候递归，获取到哪一层，哪一层才变成响应式的，Object.defineProperty() 是在一开始就递归完了
- 可监听 新增、删除 属性
- 可监听数组变化

缺点：  
Proxy 无法兼容所有浏览器，无法 polyfill

## 两数之和
给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。
你可以按任意顺序返回答案。

原题链接：https://leetcode-cn.com/problems/two-sum

```
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    for(let i = 0; i< nums.length - 1; i++) {
        for(let j = i + 1; j < nums.length; j++) {
            if(nums[i] + nums[j] === target) {
                return [i, j]
            }
        }
    }
};
```
使用 map 来解决
```
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    let map = new Map()
    for(let i = 0; i< nums.length; i++) {
        if(map.has(target - nums[i])) {
            return [map.get(target - nums[i]), i]
        } else {
            map.set(nums[i], i)
        }
    }
};
```
