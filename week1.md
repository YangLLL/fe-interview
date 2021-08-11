>[算法题 - 二分查找](#二分查找)

### 二分查找
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