# Array

## 定义

JavaScript的数组的每一项可以保存任何类型的数据

```
var arr = [35, "大漠", new Date(), function(){}, , null];
arr.length; // 6
```

## 检测

```
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

```
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

```
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

## 方法1


- concat(args)
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

## 角度

- 增加数组项方法：除了直接改变数组项的值和修改数组的length给数组添加数组项方法之外，还可以使用push()、unshift()、concat()和splice()添加数组项
- 删除数组项方法：删除数组项方法有pop()、shift()、slice()和splice()方法
- 改变数组项方法：在数组中主要通过splice()方法来改变数组项
- 查询数组项方法： 查询数组项方法其实就是对数组做查询提取功能，主要使用的方法是slice()方法


## ES6


- Array.from(input,map,context) 

主要用于将类似数组的对象[array-like object]和可遍历对象[iterable]）转为真正的数组

- Array.of

将一组值转换为数组,基本用来代替Array()、new Array()；但[]?

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
- Array.prototype.reduce/Array.prototype.reduceRight 累加器

function callbackfn(preValue,curValue,index,array){}

## 代码

数组去重

```
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

洗牌算法，从最后一个开始随机对换位置。

```
Array.prototype.shuffle = function() {
    var input = this;
    for (var i = input.length - 1; i >= 0; i--) {
        var randomIndex = Math.floor(Math.random() * (i + 1));
        var itemAtIndex = input[randomIndex];

        input[randomIndex] = input[i];
        input[i] = itemAtIndex;
    }
    return input;
};
```

创建一个长度为100的数组，并且每个元素的值等于它的下标

```
Array.from(
  new Array(100),
  (_, idx) => idx
)

'​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​'.split('').map(function (v, i) { return i; });

Array(100).fill('naive').map(function (v, i) { return i; });

Array.from(Array(100).keys());

[...Array(100).keys()]
```
