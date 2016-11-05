# 布局

## 最简布局


```html
<!-- 最简，但main放最后较不友好 -->
<div class="container">
    <div class="left">left</div>
    <div class="right">right</div>
    <div class="main">main</div>
</div>
```

```css
.container{
    overflow: hidden;// 或其他去除浮动方法
}
.left {
    float: left;
}
.main {
    margin-left: 50px;
    margin-right: 100px;
}
.right {
    float: right;
    widht: 100px;
}
```

## 圣杯布局

```html
<div class="container">
    <div class="main">main</div>
    <div class="left">left</div>
    <div class="right">right</div>
</div>
```

```css
.container{
    overflow: hidden;// 或其他去除浮动方法
}
.main{
    width: 100%;
    float: left;
}
.left{
    position: relative;
    left: -100px;
    width: 100px;
    float: left;
    margin-left: -100%;
}
.right{
    position: relative;
    right: -200px;
    width: 200px;
    float: left;
    margin-left: -200px;
}
```

## 双飞翼布局

```html
<div class="container">
    <div class="main">
        <div class="main-wrap">main</div>
    </div>
    <div class="left">left</div>
    <div class="right">right</div>
</div>
```

```css
.container{
    overflow: hidden;
}
.main{
    width: 100%;
    float: left;
}
.left{
    width: 100px;
    float: left;
    margin-left: -100%;
}
.right{
    width: 200px;
    float: left;
    margin-left: -200px;
}
.main-wrap{
    margin: 0 200px 0 100px;
}
```
优点

- 实现了内容与布局的分离，这是渐进式增强布局的思想，从内容出发，不考虑布局。
- main部分是自适应宽度的，很容易在定宽布局和流体布局中切换。
- 任何一栏都可以是最高栏，不会出问题。

缺点

- main需要一个额外的包裹层。

## Nec布局

> [网易nec布局](http://nec.netease.com/library/category/#grid)

```html
<div class="container">
    <div class="left">left</div>
    <div class="main">
        <div class="main-wrap">main</div>
    </div>
    <div class="right">right</div>
</div>
```

```css
.left {
    position: relative;
    float: left;
    width: 50px;
    margin-right: -50px;
}

.main {
    float: left;
    width: 100%;
}

.main-wrap {
    margin: 0 100px 0 50px;
}

.right {
    position: relative;
    float: right;
    width: 100px;
    margin-left: -100px;
}
```

## BS3表单自适应布局

> [Bootstraps表单自适应图标布局](http://v3.bootcss.com/css/#forms-controls-static)

```html
<div class="container">
    <div class="left">left</div>
    <div class="main">
        <div class="main-wrap">main</div>
    </div>
    <div class="right">right</div>
</div>
```

```css
.container {
    display: table;
    // display: inline-table;
}
.left {
    width: 1%;
    white-space: nowrap;
}
.main {
    display: table-cell;
    width: 100%;
}
.right {
    width: 1%;
    white-space: nowrap;
}
```

## Flex布局

```html
<div class="container">
    <div class="left">left</div>
    <div class="main">main</div>
    <div class="right">right</div>
</div>
```

```css
.container {
    display: flex;
}
.left {
    width:50px;
}
.main {
    flex:1;
}
.right {
    width: 100px;
}
```

## 块垂直水平居中

```html
<div class="box">
    <div class="bd">block</div>
</div>
```

```css
.box {
    position: relative;
    width: 100%;
    height: 200px;
}
.bd {
    width: 100px;
    height: 50px;
    line-height: 50px;
    text-align: center;
    // 以下关键
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
}
```

优点：容器内块宽高可不确定；可兼容IE；
