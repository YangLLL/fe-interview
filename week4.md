>[浏览器缓存机制](#浏览器缓存机制)  
>[虚拟dom和diff算法](#虚拟dom和diff算法)   
>[vue组件更新渲染过程](#vue组件更新渲染过程)  
>[Egg的洋葱模型实现原理，为什么叫洋葱模型](#Egg的洋葱模型实现原理，为什么叫洋葱模型)  
>[setState工作原理，同步异步，批量更新](#setState工作原理，同步异步，批量更新)  
>[算法题 - 无重复字符的最长子串](#无重复字符的最长子串)


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

### 浏览器缓存过程:
1. 浏览器第一次请求资源，没有缓存，向服务器发送请求，服务器返回200，浏览器将请求到的资源文件和响应头，和请求时间一并缓存起来。
2. 再次请求资源时，会将本次请求和上次请求的时间差和Cache-Control的max-age做对比，如果没有超过max-age的时间，则没有过期，命中强缓存，直接从浏览器缓存中获取资源。HTTP1.0 通过Expires来判断是否过期。如果时间过期了，请求头带上If-None-Match 和 If-Modified-Since 向服务器请求。
3. 服务器收到请求后，将 If-None-Match 带的 ETag，和资源文件的 ETag进行对比，如果相同，说明上次请求后资源没有更新，命中协商缓存，返回304，浏览器从缓存中读取资源。如果ETag不同，服务器处理请求，返回新的资源文件，response header中返回新的ETag，返回状态码 200
4. 如果服务器接收到的请求没有 ETag，则将If-Modified-Since 和资源文件的最近更新时间对比，如果 If-Modified-Since 的时间比上次更新的时间晚，则命中协商缓存，返回304。否则，返回新的资源文件，response header中返回新的 Last-Modified，返回状态码 200


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