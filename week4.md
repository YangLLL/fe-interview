>[浏览器缓存机制](#浏览器缓存机制)  
>[虚拟dom和diff算法](#虚拟dom和diff算法)   
>[vue组件更新渲染过程](#vue组件更新渲染过程)  
>[Egg的洋葱模型实现原理，为什么叫洋葱模型](#egg的洋葱模型实现原理为什么叫洋葱模型)  
>[setState工作原理，同步异步，批量更新](#setstate工作原理同步异步批量更新)  
>[算法题 - 无重复字符最长子串](#无重复字符最长子串)


## 浏览器缓存机制
浏览器在请求之前会查找本地是否有缓存，如果有缓存就用缓存的资源，如果没有再请求。  
浏览器的缓存策略分为强缓存和协商缓存。  
### 强缓存 
强缓存通过 Catch-Control 和 Expires 来设置
- Catch-Control: max-age 设置过期时间，是一个时间段，HTTP1.1使用
- Expires：设置过期时间，设置的是一个时刻，HTTP1.0中使用。如果浏览器的时间和服务器的时间区别很大，缓存命中的判断就可能有问题

### 协商缓存
- **If-None-Match 和 ETag**  
浏览器在向服务端请求资源时，响应头会返回一个ETag，浏览器再次向服务器请求资源的时候，request header 中会带上If-None-Match，值为上次请求服务器返回的ETag值，服务器拿到 ETag值后，和资源文件的ETag值做比较，如果ETag值相同，说明资源没有变化，命中协商缓存。

- **Last-Modified 和 If-Modified-Since**  
Last-Modified是资源文件最后一次修改的时间，会在response header中返回的，浏览器会将这个值保存起来，放到request header中的If-Modified-Since里，服务器在接收到后，会和Last-Modified做比较，如果相同，则命中协商缓存。

这两种方式判断协商缓存有什么区别

### 浏览器缓存过程:
1. 浏览器第一次请求资源，没有缓存，向服务器发送请求，服务器返回200，浏览器将请求到的资源文件和响应头，和请求时间一并缓存起来。
2. 再次请求资源时，会将本次请求和上次请求的时间差和Cache-Control的max-age做对比，如果没有超过max-age的时间，则没有过期，命中强缓存，直接从浏览器缓存中获取资源。HTTP1.0 通过Expires来判断是否过期。如果时间过期了，请求头带上If-None-Match 和 If-Modified-Since 向服务器请求。
3. 服务器收到请求后，将 If-None-Match 带的 ETag，和资源文件的 ETag进行对比，如果相同，说明上次请求后资源没有更新，命中协商缓存，返回304，浏览器从缓存中读取资源。如果ETag不同，服务器处理请求，返回新的资源文件，response header中返回新的ETag，返回状态码 200
4. 如果服务器接收到的请求没有 ETag，则将If-Modified-Since 和资源文件的最近更新时间对比，如果 If-Modified-Since 的时间比上次更新的时间晚，则命中协商缓存，返回304。否则，返回新的资源文件，response header中返回新的 Last-Modified，返回状态码 200


## 虚拟dom和diff算法

### 虚拟dom
虚拟DOM就是一个用来描述真实DOM的javaScript对象

虚拟dom 的作用：
1. 可以减少 JS 操作真实的 dom 带来的性能消耗。真实DOM的属性很多，创建DOM节点开销很大，虚拟DOM只是普通JavaScript对象，描述属性并不需要很多，创建开销很小，操作dom性能消耗大,减少操作dom的范围可以提升性能，使用虚拟 dom 在复杂视图情况下可以提升渲染性能
2. 虚拟DOM可以跟踪状态变化。虚拟DOM可以维护程序的状态,跟踪上一次的状态，通过比较前后两次状态差异更新真实DOM
3. 有跨平台的能力。可以在跨平台框架中使用。

### diff算法
新旧 vnode 对比，找出最小的更新范围，对比的过程就是diff 算法。  
传统的 diff 算法：  
会查找两棵树每一个节点的差异，会运行n1(dom1的节点数)*n2(dom2的节点数)次方去对比,找到差异的部分再去更新

优化后的diff算法：
- 只比较同级，不跨级比较；
- tag 不相同直接删掉重建；
- tag 和 key 都相同则认为是同一个节点，不再深度比较


## vue组件更新渲染过程
首次渲染：
模板编译为 render 函数，在这过程中会触发响应式，监听 data 属性的 getter、setter，执行 render 函数，生成 vnode，然后在执行 patch，将 vnode 渲染到真实的 dom 上。

更新过程：
修改 data，触发 setter，该数据之前已经在 getter 中被监听，重新执行 render 函数，生成新的 vnode，执行 patch 函数，diff 算法会算出最小的差异，更新到 dom 上。

vue 在更新 dom 的时候，是异步执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。页面渲染会将data的修改做整合，多次 data 修改只会渲染一次，减少dom操作，提高性能。
$nextTick() 是等 DOM 渲染完之后再去执行回调，


## Egg的洋葱模型实现原理，为什么叫洋葱模型
egg 继承于 koa，也继承了 koa 中间件的洋葱模型。  
所有的请求经过中间件的时候都会执行两次。每个中间件都接收一个 next 参数，在 next 调用之前的代码会在进入中间件的时候马上执行，next 后面的代码会在后面的中间件全部运行之后再执行。


## setState工作原理，同步异步，批量更新

### setState
1. setState 的值不能直接修改，只能通过 setState 来改变。直接修改值，不会触发重新渲染。
2. state 的更新可能是异步的，也可能是同步的。  
出于性能考虑，React 可能会把多个 setState() 调用合并成一个调用。因为 this.props 和 this.state 可能会异步更新，所以不能依赖他们的值来更新下一个状态。
在事件处理函数内部的 setState 是异步的，调用 setState 后，不能马上拿到最新的值。可以在 setState 的第二个参数中获取最新的值。  
在setTimeout，自定义dom事件中是同步的。

3. 异步更新state，多次更新在更新前会被合并。
如果 setState 传的参数是对象，多次更新会被合并。
```
this.state({
    count: 0
})
// count 执行 1 次 +1
this.setState({count: prevState.count + 1}) 
this.setState({count: prevState.count + 1})
this.setState({count: prevState.count + 1}) 
```
如果传入的是函数，不会被合并
```
// count执行3次+1
this.setState((prevState, props) => {
    return { count: prevState.count + 1 }
})
this.setState((prevState, props) => {
    return { count: prevState.count + 1 }
})
this.setState((prevState, props) => {
    return { count: prevState.count + 1 }
})
```

### setState 的 batchUpdate 机制（批量更新）

batchUpdate机制：
class 中所有的函数，在一开始执行的时候，会设一个变量 isBatchingUpdate，设置为true，然后执行函数，函数执行完成的时候 设置成 false，然后更新组件。

setState 是同步还是异步，取决于是否命中 batchUpdate 机制，如果是 true 就走异步更新，如果是 false 就是同步更新。  
异步更新：  
```
// 在一个 class 组件中，定义一个 increase 方法
increase = () => {
    // 开始，处于 batchUpdate 
    // isBatchingUpdate = true
    this.setState({
        count: this.count + 1,
    })
    // 结束
    // isBatchingUpdate = false
}
```
同步更新：
```
// 在一个 class 组件中，定义一个 increase 方法
increase = () => {
    // 开始，处于 batchUpdate
    // isBatchingUpdate = true
    setTimeout(() => {
        // 此时，isBatchingUpdate 是 false
         this.setState({
            count: this.count + 1,
        })
    })
    // 结束
    // isBatchingUpdate = false
}
```
setTimeout 中的函数是异步执行的，执行的时候 isBatchingUpdate 已经是 false 了。这时候 setTimeout 中的setState是同步执行的，可以在 setState 后马上拿到最新的 count 值。


## 无重复字符最长子串

给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。

```
/**
 * @param {string} s
 * @return {number}
 */
 var lengthOfLongestSubstring = function(s) {
  let max = 0
  const tmpArr = []
  const arr = s.split('')
  for(let i = 0; i < arr.length; i++) {
      if(tmpArr.indexOf(arr[i]) !== -1) {
          max = Math.max(tmpArr.length, max)
          tmpArr.splice(0, tmpArr.indexOf(arr[i]) + 1)
      }
      tmpArr.push(arr[i])
  }
  max = Math.max(tmpArr.length, max)
  return max
};
```
