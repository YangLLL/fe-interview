>[http和https](#http和https)  
>[作用域和闭包](#作用域和闭包)   
>[promise的实现原理](#promise的实现原理)  
>[数组的方法有哪些，es6新增的方法有哪些？怎么实现数组去重](#数组的方法有哪些，es6新增的方法有哪些？怎么实现数组去重)  
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

Promise 是通过发布/订阅模式来实现的。通过 new Promise 创建实例，在 then() 中会收集回调， 异步操作完成后调用 resolve/reject 再执行回调。  
如果传入的 executor 是同步任务，在执行 then 的时候，已经先执行了 resolve/reject，就直接执行传入的回调函数

## 数组的方法有哪些，es6新增的方法有哪些？怎么实现数组去重

数组常用的方法：pop、push、shift、unshift、splice、slice、map、find、filter、concat、forEach、findIndex、indexOf、reduce、reverse、every、some、join

slice和splice 的区别：

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
