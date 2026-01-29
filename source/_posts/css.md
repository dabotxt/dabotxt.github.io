---
title: 解决切换路由因高度不够出现滚动条导致页面抖动
tags: Css
categories: CSS
cover: https://s1.ax1x.com/2022/05/16/OW163t.jpg
top_img: https://s1.ax1x.com/2022/05/16/OW1bvV.png
abbrlink: 78cea6d8
date: 2022-05-16 10:45:45
---
现在大多数页面都是采用主题内容水平居中布局，但是，这种布局存在一个问题。现在的浏览器滚动条默认是overflow:auto类型的，也就是说如果内容高度不足一屏，没有滚动条；  
如果超出才会出现滚动条。滚动条也是占据屏幕尺寸的，从没有滚动条到有滚动条会使页面左右抖动一下。  
现有三种解决方法：
### 让垂直滚动条一直显示
```css
body {
    overflow-y: scroll;
}
```
即使内容高度不足一屏也会显示滚动条，比较影响用户体验
### 当内容高度超过一屏时，在浏览器左侧同时加上一个滚动条宽的的padding或margin
```css
.wrap-outer {
    margin-left: calc(100vw - 100%);
}
```
margin换成padding一样可以生效
### 推荐使用兼容写法
```css
html {
    overflow-x: hidden;
    overflow-y: auto;
}
body {
    width: 100vw;
    overflow: hidden;
}
```