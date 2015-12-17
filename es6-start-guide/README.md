## 13. Set和Map数据结构

### Set

构造函数、类似数组、成员唯一没有重复值


```
var s = new Set([1,'1.0',1.0,NaN,NaN,{},{}]);
console.info(s); // [1,'1.0',NaN,{},{}]
// 精确相等运算符 '==='
// 虽然 NaN !== NaN,但Set里算相等
// 注：{} !== {}
```


- 属性
  - Set.prototype.constructor: 构造函数 ＝ Set函数
  - Set.prototype.size: 返回实例成员总数
  
  
- 操作方法
  - add(value): 添加值，返回本身
  - delete(value): 删除值，返回是否成功，Boolean
  - has(value): 是否含有，Boolean
  - clear(): 清空
 
 
 ```
 var s = new Set([1,2]);
 s.add(3).add(2);
 s.size;
 s.has(1);
 s.delete(2);
 s.clear();
 
 // 数组去重
function dedupe(array) {
    return Array.from(new Set(array));
}

dedupe([1,1,2,3]) // [1, 2, 3]
 ```
 
 
 - 遍历操作
  - keys(): 返回键名 === values(): 返回键值 === set本身
  - entries(): 返回键值对
  - forEach(): 回调函数遍历
  
  
  ```
Set.prototype[Symbol.iterator] === Set.prototype.values // true


let set = new Set(['red', 'green', 'blue']);
for (let x of set) {
  console.log(x);
}
// red
// green
// blue


// 去除数组方法二
let arr = [3, 5, 2, 2, 5, 5];
let unique = [...new Set(arr)];
// [3, 5, 2]

// 数组方法map
let set = new Set([1, 2, 3]);
set = new Set([...set].map(x => x * 2));
// 返回Set结构：{2, 4, 6}

// 数组方法filter
let set = new Set([1, 2, 3, 4, 5]);
set = new Set([...set].filter(x => (x % 2) == 0));
// 返回Set结构：{2, 4}


// 方法一
let set = new Set([1, 2, 3]);
set = new Set([...set].map(val => val * 2));
// set的值是2, 4, 6

// 方法二
let set = new Set([1, 2, 3]);
set = new Set(Array.from(set, val => val * 2));
// set的值是2, 4, 6
  ```





### WeakSet

与Set类似，不重复，但只能是对象，不可遍历
  
 
 

