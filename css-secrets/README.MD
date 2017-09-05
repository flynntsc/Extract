# Css Secrets

## 7 结构与布局

### 36 自适应内部元素

> [Intrinsic sizing](http://dabblet.com/gist/82eb1575806f6eff9c37)

`min-content`将解析为这个容器内部最大的不可断行元素的高度（即最宽的单词、图片或具有固定宽度的盒元素）

`max-content`类似于`display: inline-block`

```html
<figure>
	<img src="http://temp.im/200x200" alt="">
	<figcaption>
		The Great test The Great test The Great test
	</figcaption>
</figure>
```
```scss
figure {
  max-width: 200px;
  width: min-content;
  margin: auto;
  img {
    max-width: inherit;
  }
}
```

### 37 精确控制表格列宽

> [Taming table column widths](http://dabblet.com/gist/7979af102a991cecfcdf)

只需对`<table>`元素或具有`display:table`样式元素添加`table-layout:fixed`即可。

```scss
table {
    table-layout: fixed;
    width: 100%;
}
```

### 38 根据兄弟元素的数量来设置样式

> [Styling by sibling count](http://dabblet.com/gist/70c434a6e802b062f494)

该例子中`:nth-child()`同样适用于`:nth-of-type()`,后者包含了不同元素类型。

```scss
li:only-child{
  /* 只有一个列表项时的样式 */
}

li:first-child:nth-last-child(1) {
  /* 等同 */
}
```

```scss
/* 定义mixin -> 当列表数量为m项时，命中所有列表项*/
@mixin n-items($m) {
  &:first-child:nth-last-child(#{$m}),
  &:first-child:nth-last-child(#{$m}) ~ & {
    @content;
  }
}

/* 调用时 */
li {
  @include n-items(4) {
    /* 属性与值写在这里 */
  }
}
```

### 39 满幅的背景，定宽的内容

> [Fluid background, fixed content](http://dabblet.com/gist/763229b68fa27c5c1bfa)

```scss
footer {
  padding: 1em; /* 向后兼容 */
  padding: 1em calc(50% - 450px);
  background: #333;
}
```

### 40 垂直居中

> [Vertical centering - absolute positioning method](http://dabblet.com/gist/cd12fac0e18bb27fb62d) 基于绝对定位

```scss
.main {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```
- 有时可能不能选用绝对定位
- 若元素的高度超过了视口，顶部会被裁切掉
- 在某些浏览器中，可能会导致元素有些模糊

> [Vertical centering - Viewport unit method](http://dabblet.com/gist/bf12b39d8f5da2b6e5b6) 基于视口单位

```scss
.main {
  width: 18em;
  padding: 1em 1.5em;
  margin: 50vh auto 0;
  transform: translateY(-50%);
}
```

> [Vertical centering - Flexbox solution](http://dabblet.com/gist/8aa9aa04ee57f479c513) 基于Flexbox

```scss
body{
  display:flex;
  min-height: 100vh;
  margin:0;
}
.main{
  margin: atuo;
}
```

```scss
.main{
  display:flex;
  align-items: center;
  justify-content: center;
  height: 10em;
}
```

### 41 紧贴底部的页脚

> [Sticky footer with fixed height](http://dabblet.com/gist/b10c3eb3b7078711a588) 固定高度

```html
<header>
	<h1>Site name</h1>
</header>
<main>
	<p>Some Things ......</p>
</main>
<footer>
	<p>© 2015 No rights reserved.</p>
</footer>
```

```scss
.main {
  min-height: calc(100vh - 2.5em - 7em); /* 减去页头页脚高度 */
  box-sizing: border-box;
}
```

或者将元素包裹在`#wrapper`里

```scss
#wrapper {
 min-height: calc(100vh - 7em); /* 只需减去页脚高度 */
}
```

> [Sticky footer with flexible height](http://dabblet.com/gist/410e43c60863a8dba193) 自适应高度

```scss
body {
  display: flex;
  flex-flow: column;
  min-height: 100vh;
}
.main {
  flex: 1;
}
```







