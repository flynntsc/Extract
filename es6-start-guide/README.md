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



## 15. Iterator和for...of循环

### 概念

Iterator ＝ 遍历器，统一接口机制，依次处理数据结构的所有成员;

Iterator的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是ES6创造了一种新的遍历命令for...of循环，Iterator接口主要供for...of消费

返回一个包含value和done两个属性的对象。value属性是当前成员的值，done属性是一个布尔值，表示遍历是否结束。


### 数据结构的默认Iterator接口

在ES6中，有三类数据结构原生具备Iterator接口：数组、某些类似数组的对象、Set和Map结构。


### 调用Iterator接口的场合

（1）解构赋值

```
// 对数组和Set结构进行解构赋值时
let set = new Set().add('a').add('b').add('c');

let [x,y] = set;
// x='a'; y='b'

let [first, ...rest] = set;
// first='a'; rest=['b','c'];
```

（2）扩展运算符

```
// 例一
var str = 'hello';
[...str] //  ['h','e','l','l','o']

// 例二
let arr = ['b', 'c'];
['a', ...arr, 'd']
// ['a', 'b', 'c', 'd']


// 只要某个数据结构部署了Iterator接口，就可以对它使用扩展运算符，将其转为数组。
let arr = [...iterable];
```
（3） yield*

```
let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};

var iterator = generator();

iterator.next() // { value: 1, done: false }
iterator.next() // { value: 2, done: false }
iterator.next() // { value: 3, done: false }
iterator.next() // { value: 4, done: false }
iterator.next() // { value: 5, done: false }
iterator.next() // { value: undefined, done: true }
```
（4） 任何接受数组作为参数的场合

- for...of
- Array.from()
- Map(), Set(), WeakMap(), WeakSet()（比如new Map([['a',1],['b',2]])）
- Promise.all()
- Promise.race()


### 字符串的Iterator接口

```
var someString = "hi";
typeof someString[Symbol.iterator]
// "function"

var iterator = someString[Symbol.iterator]();

iterator.next()  // { value: "h", done: false }
iterator.next()  // { value: "i", done: false }
iterator.next()  // { value: undefined, done: true }
```

```
// 可以覆盖原生的Symbol.iterator方法
var str = new String("hi");

[...str] // ["h", "i"]

str[Symbol.iterator] = function() {
  return {
    next: function() {
      if (this._first) {
        this._first = false;
        return { value: "bye", done: false };
      } else {
        return { done: true };
      }
    },
    _first: true
  };
};

[...str] // ["bye"]
str // "hi"
```


### Iterator接口与Generator函数

Symbol.iterator方法的最简单实现：Generator函数

```
var myIterable = {};

myIterable[Symbol.iterator] = function* () {
  yield 1;
  yield 2;
  yield 3;
};
[...myIterable] // [1, 2, 3]

// 或者采用下面的简洁写法

let obj = {
  * [Symbol.iterator]() {
    yield 'hello';
    yield 'world';
  }
};

for (let x of obj) {
  console.log(x);
}
// hello
// world
```



### 遍历器对象的return()，throw()

return方法的使用场合是，如果for...of循环提前退出（通常是因为出错，或者有break语句或continue语句），就会调用return方法。如果一个对象在完成遍历前，需要清理或释放资源，就可以部署return方法

```
function readLinesSync(file) {
  return {
    next() {
      if (file.isAtEndOfFile()) {
        file.close();
        return { done: true };
      }
    },
    return() {
      file.close();
      return { done: true };
    },
  };
}
```

throw方法主要是配合Generator函数使用，一般的遍历器对象用不到这个方法，参阅《Generator函数》。



### for...of循环

for...of循环内部调用的是数据结构的Symbol.iterator方法。

for...of循环可以使用的范围包括数组、Set和Map结构、某些类似数组的对象（比如arguments对象、DOM NodeList对象）、后文的Generator对象，以及字符串。

- #### 数组

for...of循环调用遍历器接口，数组的遍历器接口只返回具有数字索引的属性。这一点跟for...in循环也不一样。

```
let arr = [3, 5, 7];
arr.foo = 'hello';

for (let i in arr) {
  console.log(i); // "0", "1", "2", "foo"
}

for (let i of arr) {
  console.log(i); //  "3", "5", "7"
}
```


- #### Set和Map结构

Set结构遍历时，返回的是一个值，而Map结构遍历时，返回的是一个数组

```
var engines = new Set(["Gecko", "Trident", "Webkit", "Webkit"]);
for (var e of engines) {
  console.log(e);
}
// Gecko
// Trident
// Webkit

var es6 = new Map();
es6.set("edition", 6);
es6.set("committee", "TC39");
es6.set("standard", "ECMA-262");
for (var [name, value] of es6) {
  console.log(name + ": " + value);
}
// edition: 6
// committee: TC39
// standard: ECMA-262
```


- #### 计算生成的数据结构

ES6的数组、Set、Map下的entries(),keys(),values()

```
let arr = ['a', 'b', 'c'];
for (let pair of arr.entries()) {
  console.log(pair);
}
// [0, 'a']
// [1, 'b']
// [2, 'c']
```


- #### 类似数组的对象

for...of循环用于字符串、DOM NodeList对象、arguments对象的例子


```
// 字符串
let str = "hello";

for (let s of str) {
  console.log(s); // h e l l o
}

// DOM NodeList对象
let paras = document.querySelectorAll("p");

for (let p of paras) {
  p.classList.add("test");
}

// arguments对象
function printArgs() {
  for (let x of arguments) {
    console.log(x);
  }
}
printArgs('a', 'b');
// 'a'
// 'b'


// 会正确识别32位UTF-16字符
for (let x of 'a\uD83D\uDC0A') {
  console.log(x);
}
// 'a'
// '\uD83D\uDC0A'
```

```
// 使用Array.from方法将其转为数组
let arrayLike = { length: 2, 0: 'a', 1: 'b' };

// 报错
for (let x of arrayLike) {
  console.log(x);
}

// 正确
for (let x of Array.from(arrayLike)) {
  console.log(x);
}
```

- #### 对象

```
var es6 = {
  edition: 6,
  committee: "TC39",
  standard: "ECMA-262"
};

for (let e in es6) {
  console.log(e + ':' + es6[e]);
}
// edition:6
// committee:TC39
// standard:ECMA-262

for (let e of es6) {
  console.log(e);
}
// TypeError: es6 is not iterable

// 解决方法：使用Object.keys生成数组
for (var key of Object.keys(someObject)) {
  console.log(key + ": " + someObject[key]);
}
```

在对象上部署iterator接口的代码，一个方便的方法是将数组的Symbol.iterator属性，直接赋值给其他对象的Symbol.iterator属性。比如，想要让for...of环遍历jQuery对象：

```
jQuery.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
```

另一个方法是使用Generator函数：

```
function* entries(obj) {
  for (let key of Object.keys(obj)) {
    yield [key, obj[key]];
  }
}

for (let [key, value] of entries(obj)) {
  console.log(key, "->", value);
}
// a -> 1
// b -> 2
// c -> 3
```


- #### 与其他遍历语法的比较

  - for 较为麻烦
  - forEach 无法中途跳出
  - for...in (主要是为遍历对象而设计的，不适用于遍历数组)
    - 数组的键名是数字，但是for...in循环是以字符串作为键名“0”、“1”、“2”等等。
    - for...in循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
    - 某些情况下，for...in循环会以任意顺序遍历键名。
   - for...of
     - 有着同for...in一样的简洁语法，但是没有for...in那些缺点。
     - 不同用于forEach方法，它可以与break、continue和return配合使用。
     - 提供了遍历所有数据结构的统一操作接口。 







