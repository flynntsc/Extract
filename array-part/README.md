# Array

## 定义

JavaScript的数组的每一项可以保存任何类型的数据

```js
var arr = [35, "Flyn", new Date(), function(){}, , null];
arr.length; // 6
```

## 检测

```js
// ES6
function isArray(obj) {
    return Array.isArray(obj);
}

// ES5
function isArray(obj) {
	return Object.prototype.toString.call(obj) === '[object Array]';
}
```

## 增删

- arr.push() 末尾添加
- arr.pop() 末尾减1
- arr.unshift() 开头添加（效率差，慎用，可用push + reverse代替）
- arr.shift() 开头减1

## 排序

- arr.sort()

默认调用toString()再按UNICODE进行比较排序。

```js
const arr = [1, 3, 5, 7, 6, 4, 2, 9];

// 大小正/反排序
const compareFn = (a, b) => {
    return a - b; //对调可反序
}
arr.sort(compareFn); // [1,2,3,4,5,6,7,9]

// 随机排列
function randomSort(a, b) {
    return Math.random() - 0.5;
}
arr.sort(randomSort);
```

```js
// 对象数组的排序
function dynamicSort(property) {
    var sortOrder = 1;
    if(property[0] === "-") {
        sortOrder = -1;
        property = property.substr(1);
    }
    return function (a,b) {
        var result = (a[property] <  b[property]) ? -1 : (a[property] >  b[property]) ? 1 : 0;
        return result * sortOrder;
    }
}
console.log(myArray.sort(dynamicSort('age'))); // 按升序排列
console.log(myArray.sort(dynamicSort('-age'))); // 按降序排列
```

- arr.reverse()

## 方法


* concat(args)
  - concat(1,2,3);concat([1,2],[3])
  - 不改变原数组 VS push()改变并返回原数组
  - concat(arr,arr) 降维
- slice(s,e)
  - 不改变原数组
  - s开始位置（包括），e结束位置 （不包括）
  - 正向0，1，2...;反向-1,-2,-3...;
- splice(pos,num,args) 
  - 最强大？删除、插入、替换
  - 更改原数组并返回被删除的数组
  - pos为插入的起始位置，num为要删除的数量，args要插入的数组项
  - num为0即为插入
- arr.indexOf(searchElement[, fromIndex = 0])
  - 查询所在位置num/-1
  - 负数 = arr.length - num
- lastIndexOf() 从后往前查

```js
arr.indexOf(v) === arr.lastIndexOf(v) ? '唯一' : '重复'
```

## 迭代


- forEach()
  - 循环遍历类似for
  - forEach(callback(value, index, array) {})
- every()
  - 测试数组的每个元素是否都符合指定函数条件的存在
  - 自身返回true/false
- some()
  - 测试数组含有符合指定函数条件的元素的存在
  - 自身返回true/false
- filter()
  - 经callback条件判断过滤出新数组
  - callback必然是判断return true
- map()
  - 经过callback筛选出符合条件的并组成新数组

## 最大最小值


- Math.max()
- Math.min()
- Math.min.apply( Math, array )
- Math.max(...arr)

## 换个角度

- 增加数组项方法：除了直接改变数组项的值和修改数组的length给数组添加数组项方法之外，还可以使用push()、unshift()、concat()和splice()添加数组项
- 删除数组项方法：删除数组项方法有pop()、shift()、slice()和splice()方法
- 改变数组项方法：在数组中主要通过splice()方法来改变数组项
- 查询数组项方法： 查询数组项方法其实就是对数组做查询提取功能，主要使用的方法是slice()方法


## ES6


- Array.from(input,map,context) 

主要用于将类似数组的对象[array-like object]和可遍历对象[iterable]）转为真正的数组 === 任何==有length属性==的对象，都可以通过Array.from方法转为数组

```js
Array.from({ length: 3 });
// [ undefined, undefined, undefined ]
// 但[...({ length: 3 })]
```

- Array.of

解决了构造函数方法创建数组时单个数字引起了怪异行为

```
const a = new Array(3);   // (3) [empty × 3] 构造函数方法单个数组会被用于数组长度
const b = Array.of(3);    // [3]
```

- Array.prototype.copyWithin(target, start = 0, end = this.length)

从 start <= zhi < end 复制，并插入到target位置

- Array.prototype.fill(val,start,end)

使用给定的值填充一个数组

- Array.prototype.find

找出第一个符合条件的数组成员

VS filter 所有结果

- Array.prototype.findIndex

返回数组项在数组中的位置

- Array.prototype.keys
- Array.prototype.values
- Array.prototype.entries
- Array.prototype[Symbol.iterator]
- [Array.prototype.reduce](http://www.zcfy.cc/article/reduce-composing-software-javascript-scene-medium-2697.html)/Array.prototype.reduceRight 累加器，最为灵活强大

function callbackfn(preValue,curValue,index,array){}

## 代码

### 排序算法

> [阮一峰js整理之排序算法](http://javascript.ruanyifeng.com/library/sorting.html)

> [十大经典排序算法](https://github.com/hustcc/JS-Sorting-Algorithm)

**冒泡排序** - 最简单但最低效

两两比较，大的放后

```js
function sort(arr) {
    const l = arr.length
    for (let i = 0; i < l; i++) {
        for (let j = 0; j < l - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
            }
        }
    }
    return arr
}
```

**选择排序** - O(n²) 

不占用额外的内存空间，低效

依次跟后面的数比较一轮后，再与最小值对换

```js
function sort(arr) {
    const l = arr.length
    let min
    for (let i = 0; i < l - 1; i++) {
        min = i
        for (let j = i + 1; j < l; j++) {
            arr[min] > arr[j] && (min = j)
        }
        min !== i && ([arr[i], arr[min]] = [arr[min], arr[i]])
    }
    return arr
}
```

**插入排序** - 低效

将数组分成“已排序”和“未排序”，将后面一个元素从“未排序”部分插入“已排序”部分，从而“已排序”部分增加一个元素，“未排序”部分减少一个元素

```js
function sort(arr) {
    const l = arr.length
    let v, j
    for (let i = 1; i < l; i++) {
        v = arr[i]
        for (j = i - 1; j >= 0 && arr[j] > v; j--) {
            arr[j + 1] = arr[j]
        }
        arr[j + 1] = v
    }
    return arr
}
```

**合并排序** - 广泛使用

将数组拆开，分成n个只有一个元素的数组（递归），然后不断地两两合并，直到全部排序完成

```js
function merge(left, right) {
    const res = []
    let il = 0
    let ir = 0
    while (il < left.length && ir < right.length) {
        left[il] < right[ir] ? res.push(left[il++]) : res.push(right[ir++])
    }
    return [...res, ...left.slice(il), ...right.slice(ir)]
}

// 简易版
function sort(arr) {
    const l = arr.length
    if (arr.length < 2) return arr
    const mid = l / 2 | 0
    const left = arr.slice(0, mid)
    const right = arr.slice(mid)
    return merge(sort(left), sort(right))
}
// 空间改进版
function sort2(arr) {
    const l = arr.length
    if (arr.length < 2) return arr
    const mid = l / 2 | 0
    const left = arr.slice(0, mid)
    const right = arr.slice(mid)
    // 目的=>将原先的arr替换成排序后的arr
    // 疑问=>arr=newArr也不行？多占用空间？效率低？
    const newArr = merge(sort(left), sort(right))
    newArr.unshift(0, l)
    arr.splice.apply(arr, newArr)
    return arr
    // 因返回的是一个全新的数组，会多占用空间。
    // 因此，修改上面的函数，使之在原地排序，不多占用空间
    // 不理解ING ？？？
}

// 迭代方式 ？？？
```

**快速排序** - 公认最快

先确定一个“支点”（pivot），将所有小于“支点”的值都放在该点的左侧，大于“支点”的值都放在该点的右侧，然后对左右两侧不断重复这个过程，直到所有排序完成。

```js
function partition(arr, i, j) {
    const pivot = arr[(i + j) / 2 | 0]
    while (i <= j) {
        while (arr[i] < pivot) i += 1
        while (arr[j] > pivot) j -= 1
        if (i <= j) {
            [arr[i], arr[j]] = [arr[j], arr[i]]
            i += 1
            j -= 1
        }
    }
    return i
}

function sort(arr, i = 0, j = arr.length - 1) {
    const k = partition(arr, i, j)
    if (i < k - 1) sort(arr, i, k - 1)
    if (k < j) sort(arr, k, j)
    return arr
}
```

### 数组去重

```js
<!-- 使用forEach()方法和indexOf()方法 -->
Array.prototype.unique1 = function () {
    var newArray = [];
    this.forEach(function (index) {
        if(newArray.indexOf(index) == -1) {
            newArray.push(index);
        }
    });
    return newArray;
}


<!-- 排序后再遍历 -->
Array.prototype.unique2 = function () {
    // 原数组先排序
    this.sort();
    // 构建一个新数组存放结果
    var newArray = [];
    for(var i = 0; i < this.length; i++) {
        // 检查原数中的第i个元素与结果中的最后一个元素是否相同
        // 因为排序了，所以重复元素会在相邻位置
        if(this[i] !== newArray[newArray.length - 1]) {
            // 如果不同，将元素放到结果数组中
            newArray.push(this[i]);
        }
    }
    return newArray;
}


<!-- 对象键值对法,可区分数值与字符串 -->
Array.prototype.unique3 = function () {
    // 构建一个新数组存放结果
    var newArray = [];
    // 创建一个空对象
    var object = {};
    // for循环时，每次取出一个元素与对象进行对比
    // 如果这个元素不重复，则将它存放到结果数中
    // 同时把这个元素的内容作为对象的一个属性，并赋值为1,
    // 存入到第2步建立的对象中
    for(var i = 0; i < this.length; i++) {
        // 检测在object对象中是否包含遍历到的元素的值
        if(!object[typeof (this[i]) + this[i]]) {
            // 如果不包含，将存入对象的元素的值推入到结果数组中
            newArray.push(this[i]);
            // 如果不包含，存入object对象中该属性名的值设置为1
            object[typeof (this[i]) + this[i]] = 1;
        }
    }
    return newArray;
}


<!-- ES6大法 -->
function unique(arr) {
    const seen = new Map()
    return arr.filter((a) => !seen.has(a) && seen.set(a, 1))
}
// or
function unique(arr) {
    return Array.from(new Set(arr))
}
// or
function unique(arr) {
    return [...new Set(arr)]
}
```

### 洗牌算法

从最后一个开始随机与前面的数对换位置

```js
Array.prototype.shuffle = function () {
  const arr = this
  for (let i = arr.length - 1; i >= 0; i--) {
    const j = Math.random() * (i + 1) | 0;
    [arr[j], arr[i]] = [arr[i], arr[j]]
  }
  return arr
}
```

### 创建数组

创建0-99的数组

```js
Array.from(Array(100), (_, i) => i)

Array.from({ length: 100 }, (_, i) => i)

Array.from(Array(100).keys())

'​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​'.split('').map((_, i) => i)

Array(100).fill('').map((_, i) => i)

[...Array(100).keys()]
```

### 降维数组

扁平化多维数组

```js
var arr = [1,3,4,5,[6,[0,1,5],9],[2,5,[1,5]],[5]]

// 奇巧方法
var r1 = arr => arr.toString().split(',')
var r2 = arr => arr.join(',').split(',')

// reduce 尾递归
const r3 = arr => arr.reduce((a, b) => a.concat(Array.isArray(b) ? r3(b) : b), [])
```

### 全排列

> [JS全排列的7种算法总结](http://www.lingchenliang.com/post/134.html)

```js
/*  
全排列（递归链接）算法  
1、设定源数组为输入数组，结果数组存放排列结果（初始化为空数组）；  
2、逐一将源数组的每个元素链接到结果数组中（生成新数组对象）；  
3、从原数组中删除被链接的元素（生成新数组对象）；  
4、将新的源数组和结果数组作为参数递归调用步骤2、3，直到源数组为空，则输出一个排列。  
*/
function perm(arr, res = []) {
    (function fn(arr, temp = []) {
        if (!arr.length) return res.push(temp.join(''))
        for (let i of arr.keys()) {
            fn(arr.slice(0, i).concat(arr.slice(i + 1)), temp.concat(arr[i]))
        }
    })(arr)
    return res
}
```

### 莱文斯坦距离

> [Wiki](http://en.wikipedia.org/wiki/Levenshtein_distance)

>[js的实现](https://rosettacode.org/wiki/Levenshtein_distance#ES5)

>[在Regularjs中的使用](https://leeluolee.github.io/2014/10/21/how-ls-help-you-diff-two-array/)

又称Levenshtein距离，是编辑距离的一种。指两个字串之间，由一个转成另一个所需的最少编辑操作次数。许可的编辑操作包括将一个字符替换成另一个字符，插入一个字符，删除一个字符。

![ld算法](http://upload.wikimedia.org/math/d/4/f/d4f80cafb626ae9d9b8dc748360f61ec.png)

```js
function levenshtein(x, y) {
    const m = x.length
    const n = y.length
    if (x === y) return 0
    if (!m) return n
    if (!n) return m
    let a = [...Array(m + 1).keys()]
    let b = []
    for (let i = 0; i < n; i++) {
        b[0] = i + 1
        for (let j = 0; j < m; j++) {
            const k = y.charAt(i) === x.charAt(j) ? 0 : 1
            b[j + 1] = Math.min(b[j] + 1, a[j + 1] + 1, a[j] + k)
        }
        [a, b] = [b, a]
    }
    return a[m]
}
```
