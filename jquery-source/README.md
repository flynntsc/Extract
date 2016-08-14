## ready事件判断

DOM文档加载的步骤

- (1) 解析HTML结构。
- (2) 加载外部脚本和样式表文件。
- (3) 解析并执行脚本代码。
- (4) 构造HTML DOM模型。//ready
- (5) 加载图片等外部文件。
- (6) 页面加载完毕。//load

```
jQuery.ready.promise = function (obj) {
    if(!readyList) {
        readyList = jQuery.Deferred();
        if(document.readyState === "complete") {
            // Handle it asynchronously to allow scripts the opportunity to delay ready
            setTimeout(jQuery.ready);
        } else {
            document.addEventListener("DOMContentLoaded", completed, false);
            window.addEventListener("load", completed, false);
        }
    }
    return readyList.promise(obj);
};
```

## 多库共存处理

```
jQuery.noConflict();
// 使用 jQuery
jQuery("aaron").show();
// 使用其他库的 $()
$("aaron").style.display = ‘block’;
```

- 1.在导入jquery.js的时候，将prototype.js中的$进行保存，var _$ = window.$;
- 2.jquery完全导入之后，此时的window.$已经被替换为jquery中的$
- 3.当执行noConflict函数，此时的window.$ === jQuery 成立，进而window.$被替换成之前保存的_$，即为prototype中的$
- 4.此时即完成了jQuery让出$控制权的功能

```
Var _jQuery = window.jQuery,
    _$ = window.$;

jQuery.noConflict = function (deep) {
    if(window.$ === jQuery) {
        window.$ = _$;
    }
    if(deep && window.jQuery === jQuery) {
        window.jQuery = _jQuery;
    }
    return jQuery;
};
```

## 分离构造器（无new）

```
// 平常的写法
var aQuery = function (sel) {
    this.sel = sel
    return this
}

aQuery.fn = aQuery.prototype = {
    getSel: function () {
        return this.sel
    },
    constructor: aQuery
}

var a = new aQuery('aaa')

```

```
// jQuery实现
## var jQuery = function (selector, context) {
##     return new jQuery.fn.init(selector, context)
## }

## jQuery.fn = jQuery.prototype = {
##     name: 'flyn',
##     init: function (selector, context) {
##         this.selector = selector;
##         return this;
##     },
##     constructor: jQuery
## }

## jQuery.fn.init.prototype = jQuery.fn
```

## 插件接口的设计（extend）

jQuery.extend 调用的时候，this是指向jQuery对象的(jQuery是函数，也是对象！)，所以这里扩展在jQuery上。而jQuery.fn.extend 调用的时候，this指向fn对象，jQuery.fn 和jQuery.prototype指向同一对象，扩展fn就是扩展jQuery.prototype原型对象。

```
jQuery.extend = jQuery.fn.extend = function () {
    var src, copyIsArray, copy, name, options, clone,
        target = arguments[0] || {},
        i = 1,
        length = arguments.length,
        deep = false;

    // 如果第一个参数是布尔类型，则表示是否要深递归，
    if(typeof target === "boolean") {
        deep = target;
        target = arguments[1] || {};
        // 如果传了类型为 boolean 的第一个参数，i 则从 2 开始
        i = 2;
    }

    // Handle case when target is a string or something (possible in deep copy)
    // 如果传入的第一个参数是 字符串或者其他
    if(typeof target !== "object" && !jQuery.isFunction(target)) {
        target = {};
    }

    // extend jQuery itself if only one argument is passed
    // 如果参数的长度为 1 ，表示是 jQuery 静态方法
    if(length === i) {
        target = this;
        --i;
    }

    // 可以传入多个复制源
    // i 是从 1或2 开始的
    for(; i < length; i++) {
        // Only deal with non-null/undefined values
        // 将每个源的属性全部复制到 target 上
        if((options = arguments[i]) != null) {
            // Extend the base object
            for(name in options) {
                // src 是源（即本身）的值
                // copy 是即将要复制过去的值
                src = target[name];
                copy = options[name];

                // Prevent never-ending loop
                // 防止有环，例如 extend(true, target, {'target':target});
                if(target === copy) {
                    continue;
                }

                // Recurse if we're merging plain objects or arrays
                // 这里是递归调用，最终都会到下面的 else if 分支
                // jQuery.isPlainObject 用于测试是否为纯粹的对象
                // 纯粹的对象指的是 通过 "{}" 或者 "new Object" 创建的
                // 如果是深复制
                if(deep && copy && (jQuery.isPlainObject(copy) || (copyIsArray = jQuery.isArray(copy)))) {
                    // 数组
                    if(copyIsArray) {
                        copyIsArray = false;
                        clone = src && jQuery.isArray(src) ? src : [];

                        // 对象
                    } else {
                        clone = src && jQuery.isPlainObject(src) ? src : {};
                    }

                    // Never move original objects, clone them
                    // 递归
                    target[name] = jQuery.extend(deep, clone, copy);

                    // Don't bring in undefined values
                    // 最终都会到这条分支
                    // 简单的值覆盖
                } else if(copy !== undefined) {
                    target[name] = copy;
                }
            }
        }
    }
    return target;
};
```

## 回溯处理的设计

每个jQuery对象都有三个属性：context、selector和prevObject，其中的prevObject属性就指向这个对象栈中的前一个对象，而通过这个属性可以回溯到最初的DOM元素集中

```
addBack()


end: function () {
    return this.prevObject || this.constructor(null);
}

// 构建jQuery对象的时候，通过pushStack方法构建
find: function (selector) {

    //...........................省略................................

    //通过sizzle选择器，返回结果集
    jQuery.find(selector, self[i], ret);

    // Needed because $( selector, context ) becomes $( context ).find( selector )
    ret = this.pushStack(len > 1 ? jQuery.unique(ret) : ret);
    ret.selector = this.selector ? this.selector + " " + selector : selector;
    return ret;
}

pushStack: function (elems) {
    // Build a new jQuery matched element set
    var ret = jQuery.merge(this.constructor(), elems);

    // Add the old object onto the stack (as a reference)
    ret.prevObject = this;
    ret.context = this.context;

    // Return the newly-formed element set
    return ret;
}
```

- 1、首先构建一个新的jQuery对象，因为constructor是指向构造器的，所以这里就等同于调用jQuery()方法了，返回了一个新的jQuery对象；
- 2、然后用jQuery.merge语句把elems节点合并到新的jQuery对象上；
- 3、最后给返回的新jQuery对象添加prevObject属性，我们看到prevObject其实还是当前jQuery的一个引用罢了，所以也就是为什么通过prevObject能取到上一个合集的原因了。

## 理解观察者模式

简单的实现例子：

```
var Observable = {
  callbacks: [],
  add: function(fn) {
    this.callbacks.push(fn);
  },
  fire: function() {
    this.callbacks.forEach(function(fn) {
      fn();
    })
  }
}

// 使用add订阅
Observable.add(function() {
  alert(1)
})

Observable.add(function() {
  alert(2)
})

// 发布
Observable.fire(); // 1, 2
```

## Callbacks
