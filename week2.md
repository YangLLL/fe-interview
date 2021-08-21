>[http和https](#http和https)  
>[作用域和闭包](#作用域和闭包)   
>[promise的实现原理](#promise的实现原理)  
>[数组的方法有哪些，es6新增的方法有哪些？怎么实现数组去重](#数组的方法有哪些，es6新增的方法有哪些？怎么实现数组去重)  
>[vue的响应式是怎么实现的？](#vue的响应式是怎么实现的？)  
>[算法题 - 两数之和](#两数之和)

## http和https
---
## 作用域和闭包
---
## promise的实现原理
---
## 数组的方法有哪些，es6新增的方法有哪些？怎么实现数组去重
---
## vue的响应式是怎么实现的？
---

### 两数之和
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
