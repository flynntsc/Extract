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

与Set类似，不重复，但只能是对象，不可遍历,自动垃圾回收

- 方法
 - add(value): 添加新成员
 - delete(value): 删除成员
 - has(value): 是否存在
 
 ```
var a = [[1,2], [3,4]];
var ws = new WeakSet(a);


var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false

ws.delete(window);
ws.has(window);    // false
```

WeakSet的一个用处，是储存DOM节点，而不用担心这些节点从文档移除时，会引发内存泄漏。

```
const foos = new WeakSet()
class Foo {
  constructor() {
    foos.add(this)
  }
  method () {
    if (!foos.has(this)) {
      throw new TypeError('Foo.prototype.method 只能在Foo的实例上调用！')
    }
  }
}
```
上面代码保证了Foo的实例方法，只能在Foo的实例上调用。这里使用WeakSet的好处是，数组foos对实例的引用，不会被计入内存回收机制，所以删除实例的时候，不用考虑foos，也不会出现内存泄漏。
  
### Map
 
#### Map结构的目的和基本用法
 
 Object是’字符串-值‘的对应，Map提供’值-值‘的对应，Map更适合’键值对‘的数据结构；
 
 ```
// Map的键与内存地址绑定
var map = new Map();
map.set(['a'], 555);
map.get(['a']) // undefined

// 内存地址不一样，就视为两个键
var map = new Map();
var k1 = ['a'];
var k2 = ['a'];
map.set(k1, 111).set(k2, 222);
map.get(k1) // 111
map.get(k2) // 222
```

如果Map的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map将其视为一个键

```
let map = new Map();

map.set(NaN, 123);
map.get(NaN) // 123

map.set(-0, 123);
map.get(+0) // 123
```

- 属性和操作方法
  - size
  - set(key,value): 返回Map本身，可链式写法
  - get(key)
  - has(key)
  - delete(key)
  - clear():清除所有
  
 - 遍历方法
   - keys(): 键名
   - values(): 键值
   - entries(): 键名 键值
   - forEach()
   
```
let map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"


for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}

map[Symbol.iterator] === map.entries
// true


// 转为数组对应事例
let map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
// [1, 2, 3]

[...map.values()]
// ['one', 'two', 'three']

[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]

[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]
```

#### 转换
 
(1) Map转数组

```
let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
[...myMap]
// [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
```

(2) 数组转Map

```
new Map([[true, 7], [{foo: 3}, ['abc']]])
// Map {true => 7, Object {foo: 3} => ['abc']}
```

(3) Map转对象

```
function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToObj(myMap)
// { yes: true, no: false }
```

(4) 对象转Map

```
function objToStrMap(obj) {
  let strMap = new Map();
  for (let k of Object.keys(obj)) {
    strMap.set(k, obj[k]);
  }
  return strMap;
}

objToStrMap({yes: true, no: false})
// [ [ 'yes', true ], [ 'no', false ] ]
```

(5) Map转JSON

```
// Map的键名都是字符串
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)
// '{"yes":true,"no":false}'


// Map的键名有非字符串
function mapToArrayJson(map) {
  return JSON.stringify([...map]);
}

let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
mapToArrayJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]'
```

(6) JSON转Map

```
// 所有键名都是字符串
function jsonToStrMap(jsonStr) {
  return objToStrMap(JSON.parse(jsonStr));
}
jsonToStrMap('{"yes":true,"no":false}')
// Map {'yes' => true, 'no' => false}


// 整个JSON就是一个数组
function jsonToMap(jsonStr) {
  return new Map(JSON.parse(jsonStr));
}
jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
// Map {true => 7, Object {foo: 3} => ['abc']}
```


#### WeakMap

只接受对象作为键名（null除外），不接受其他类型的值作为键名，而且键名所指向的对象，不计入垃圾回收机制。

```
var wm = new WeakMap();
var element = document.querySelector(".element");

wm.set(element, "Original");
wm.get(element) // "Original"

element.parentNode.removeChild(element);
element = null;
wm.get(element) // undefined
```

WeakMap只有四个方法可用：get()、set()、has()、delete()

典型使用场合：DOM节点作为键名

```
let myElement = document.getElementById('logo');
let myWeakmap = new WeakMap();

myWeakmap.set(myElement, {timesClicked: 0});

myElement.addEventListener('click', function() {
  let logoData = myWeakmap.get(myElement);
  logoData.timesClicked++;
  myWeakmap.set(myElement, logoData);
}, false);
```

```
// 部署私有属性
let _counter = new WeakMap();
let _action = new WeakMap();

class Countdown {
  constructor(counter, action) {
    _counter.set(this, counter);
    _action.set(this, action);
  }
  dec() {
    let counter = _counter.get(this);
    if (counter < 1) return;
    counter--;
    _counter.set(this, counter);
    if (counter === 0) {
      _action.get(this)();
    }
  }
}

let c = new Countdown(2, () => console.log('DONE'));

c.dec()
c.dec()
// DONE
```
Countdown类的两个内部属性_counter和_action，是实例的弱引用，所以如果删除实例，它们也就随之消失，不会造成内存泄漏









