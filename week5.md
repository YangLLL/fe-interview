>[浏览器打开一个tab，开了几个进程？](#浏览器打开一个tab开了几个进程)  
>[vue项目优化](#vue项目优化)   
>[react合成事件，原生事件和合成事件一起触发谁先执行？](#react合成事件原生事件和合成事件一起触发谁先执行)  
>[redux工作流程，如何实现时间旅行？](#redux工作流程如何实现时间旅行)  
>[react fiber是什么？](#react-fiber是什么)  
>[算法题 - 寻找两个正序数组的中位数](#寻找两个正序数组的中位数)


## 浏览器打开一个tab，开了几个进程？
Chrome浏览器包括：1个浏览器（Browser）主进程、1个 GPU 进程、1个网络（NetWork）进程、多个渲染进程和多个插件进程。

打开1个页面至少需要打开四个进程：1个网络进程、1个浏览器进程、1个GPU进程以及1个渲染进程。如果打开的页面有运行插件的话，还需要再加上1个插件进程。

Chrome会为每个tab创建一个渲染进程。  
浏览器渲染进程包括多个线程，主要包括以下几个线程：
- GUI渲染线程：负责页面渲染
- JS引擎线程：执行js代码，和GUI渲染线程是互斥的。如果js引擎线程在执行，GUI渲染线程就会被挂起，等js执行完毕之后，再继续渲染
执行js代码，和GUI渲染线程是互斥的。如果js引擎线程在执行，GUI渲染线程就会被挂起，等js执行完毕之后，再继续渲染
- 事件触发线程：用来控制事件循环。
- 定时器触发线程：setInterval 和 setTimeout 所在的线程，计时完毕后会将对应的事件添加到事件队列中，等 js 引擎空闲时处理。
- 异步http请求线程：处理网络请求，如果有回调，在网络请求执行完之后，将回调放入事件队列中，等 js 引擎空闲时处理。

## vue项目优化
### 1. 代码优化
- v-if 和 v-show 区分使用场景
- v-for遍历必须加 key
- 路由懒加载
- 服务端渲染 SSR
- 第三方插件按需引入

### 2. webpack层面的优化
* 对图片进行压缩：先引入npm install image-webpack-loader 
* --save-dev，然后在 webpack.config.js 中配置
* 减少 ES6 转为 ES5 的冗余代码

### 3. web 的技术优化
- 开启 gzip 压缩
- 浏览器缓存
- CDN 的使用
- 使用 Chrome Performance 查找性能瓶颈

## react合成事件，原生事件和合成事件一起触发谁先执行？
```
// 事件的最后一个参数是event, event不是调用的时候传入的，是可以直接获取到的
clickHandler(id,title,event) { 
    console.log(id,title,event)
}
```
react 的事件是合成事件，react 16 所有的事件都被挂载到 document 上，react 17 是挂在到 root 上。  
react 的事件中获取到的 event 不是原生 js 事件对象，是 SyntheticEvent 合成事件对象，可以通过 event.nativeEvent 获取原生事件对象。合成事件模拟出来 DOM 事件所有能力。

合成事件原理：  
触发一个事件后，冒泡到顶层 document上，react事件是统一绑定到document上的，document会统一成react event，然后再去派发事件。通过target知道是那个元素触发的。

为什么要合成事件机制：  
- 更好的兼容性和跨平台。自己实现了一套事件逻辑，减少了使用dom事件的逻辑（，还需要dom 触发、监听事件）。迁移到其他平台之后只需要改dom相关的那一套
- 全部挂载到document上，减少内存消耗，避免频繁解绑。减少 dom 事件的依赖，减少了事件绑定、解绑的的次数，只在 document 上绑定一次，事件挂载的越多，消耗越大，减少内存消耗。
- 方便事件的统一管理（如事务机制）

react的transition事务机制实现原理：  
就是给目标函数添加一系列的 前置 和 后置函数，对目标函数进行功能增强或者代码环境保护。

## redux工作流程，如何实现时间旅行？
view里面调用dispatch(action)去发起一个action，store会自动调用reducer去处理action，reducer接收老的state和action，返回新的state给store，然后store去更新view里面的数据，触发视图刷新。如果有中间件，dispatch(action)的时候，会先调用中间件的方法，再去调用redux的dispatch方法执行正常流程。

如何实现时间旅行：


## react fiber是什么？
react 的更新分为两个阶段：
- reconciliaction 阶段 - 执行 diff 算法，纯 js 计算
- commit 阶段 - 将 diff 结果渲染 DOM

fiber优化：
将 reconciliation 阶段进行任务拆分（commit 无法拆分），拆分成多个任务片段  
拆任务后， DOM需要渲染时暂停执行，空闲时恢复  
window.requestIdleCallback 可以知道 dom 需要渲染

## 寻找两个正序数组的中位数

```
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number}
 */
var findMedianSortedArrays = function(nums1, nums2) {
    let nums = []
    const len = nums1.length + nums2.length
    if(nums1.length === 0 || nums2.length === 0) {
        nums = nums1.concat(nums2)
    } else if(nums1[nums1.length - 1] <= nums2[0]) {
        nums = nums1.concat(nums2)
    } else if(nums1[0] >= nums2[nums2.length - 1]) {
        nums = nums2.concat(nums1)
    } else {
        while(nums1.length !== 0 || nums2.length !== 0) {
            if(nums1.length === 0) {
                nums = nums.concat(nums2)
                break
            } else if(nums2.length === 0) {
                nums = nums.concat(nums1)
                break
            } else if(nums1[0] < nums2[0]) {
                nums.push(nums1.shift())
            } else {
                nums.push(nums2.shift())
            }
        }
    }

    if(len % 2 === 0) {
        return (nums[len / 2 - 1] + nums[len / 2]) / 2
    }
    return nums[Math.floor(len / 2)]

};
```