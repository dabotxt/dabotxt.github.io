---
title: flex弹性盒子布局最后一行不满时左对齐实现思路
tags: Css
categories: CSS
cover: 'https://s1.ax1x.com/2022/05/16/OW163t.jpg'
top_img: 'https://s1.ax1x.com/2022/05/16/OW1bvV.png'
abbrlink: 8513d6fd
date: 2022-05-16 11:03:44
---
只需要在弹性盒子的容器加一个伪类元素，大小就是盒子内容的宽高就可以实现。
```vue
<ul>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
</ul>
```
```css
*{
    padding: 0px;
    list-style: none;
}
ul{
    width: 1000px;
    height: 800px;
    border: 1px solid #000;
    display: flex;
    flex-flow: row wrap;
    justify-content: space-between;
}
li{
    width: 300px;
    height: 300px;
    border: 1px solid #456;
}
ul::after{
    width: 300px;
    height: 300px;
    content: '';
}
```