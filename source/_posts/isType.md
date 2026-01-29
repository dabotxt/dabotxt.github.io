---
title: 前端判断当前访问设备类型
abbrlink: 4a17b166
cover: https://s1.ax1x.com/2022/05/05/OeYnqe.png
top_img: https://s1.ax1x.com/2022/05/05/OeY4iR.png
tags: TypeScript
categories: TypeScript
date: 2022-04-28 12:00:00
---
### 在utils目录下创建一个isType文件

``` ts
  const ua = window.navigator.userAgent;
  export const isWap = /Android|iPhone|ios|iPad|iPod|BlackBerry|IEMobile/i.test(ua); // 是否是移动端
  export const isWeixin = /MicroMessenger/i.test(ua); // 是否是微信浏览器
  export const isAlipay = /AlipayClient/i.test(ua); // 是否是支付宝浏览器
  export const isAndroid = /Android/i.test(ua); // 是否安卓设备
  export const isIOS = /iphone|ios|ipad|ipod/i.test(ua);
  export const isMainClient = /aijiao100\(newk12/i.test(ua);
  export const isWeibo = /(weibo).*weibo__([\d.]+)/i.test(ua);
  export const isQQ = /qq\/([\d.]+)/i.test(ua);
  export const isQQBrowser = /(qqbrowser)\/([\d.]+)/i.test(ua);
  export const isQzone = /qzone\/.*_qz_([\d.]+)/i.test(ua);
  export const isInApp = isWeixin || isAlipay || isWeibo || isQQ || isMainClient;
  
  // 安卓 chrome 浏览器，很多 app 都是在 chrome 的 ua 上进行扩展的
  export const isOriginalChrome = /chrome\/[\d.]+ Mobile Safari\/[\d.]+/i.test(ua) && isAndroid;
  
  // chrome for ios 和 safari 的区别仅仅是将 Version/<VersionNum> 替换成了 CriOS/<ChromeRevision>
  // ios 上很多 app 都包含 safari 标识，但它们都是以自己的 app 标识开头，而不是 Mozilla
  
  export const isSafari = /safari\/([\d.]+)$/i.test(ua) && /iphone|ios|ipad|ipod/i.test(ua) && ua.indexOf('Crios') < 0 && ua.indexOf('Mozilla') === 0;
```
### 使用方法
``` ts
import { isWap } from './utils/isType';
if (isWap) {
    console.log('当前是移动端')
} else {
    console.log('当前是PC端')
}
```