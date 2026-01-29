---
title: Vite+Vue3+TypeScript轻量级框架搭建(1)
tags: Vite
categories: Vite
cover: 'https://s1.ax1x.com/2022/05/05/OeUDJA.jpg'
top_img: 'https://s1.ax1x.com/2022/05/05/OeUIWn.png'
abbrlink: 5e9df9d6
date: 2022-05-05 14:54:34
---
### 初始化项目q
```
npm init vite@latest
√ Project name: vite+vue3+ts  // 项目名字
√ Select a framework: » vue  // 框架语言
√ Select a variant: » vue-ts  // 拓展语言
```
### 添加setup name增强插件
安装
```shell
pnpm install vite-plugin-vue-setup-extend -D
```
配置
``` ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueSetupExtend from 'vite-plugin-vue-setup-extend'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), vueSetupExtend()]
})
```
使用
``` vue
<script setup name='abc'>
</script>
```
### 为打包后的文件提供传统浏览器兼容性支持
因为vite不提供传统浏览器兼容,如果有需要请添加此插件
```shell
pnpm install @vitejs/plugin-legacy -D
```
配置
```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueSetupExtend from 'vite-plugin-vue-setup-extend'
import legacy from '@vitejs/plugin-legacy'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
      vue(),
    vueSetupExtend(),
    legacy({
      targets: ['defaults', 'not IE 11'],
      additionalLegacyPolyfills: ['regenerator-runtime/runtime']
    })
  ]
})
```
### vite-plugin-compression 压缩插件
支持gzip等多种压缩算法
```shell
pnpm install vite-plugin-compression -D
```
配置
```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueSetupExtend from 'vite-plugin-vue-setup-extend'
import legacy from '@vitejs/plugin-legacy'
import compressPlugin from 'vite-plugin-compression'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
      vue(),
    vueSetupExtend(),
    legacy({
      targets: ['defaults', 'not IE 11'],
      additionalLegacyPolyfills: ['regenerator-runtime/runtime']
    }),
    compressPlugin({
      verbose: false, // 是否在控制台输出压缩结果
      disable: false, // 是否禁用
      threshold: 10240, // 文件容量大于这个值进行压缩，它将被压缩，单位为b
      algorithm: 'gzip', // 压缩算法 可选 ['gzip','brotliCompress' ,'deflate','deflateRaw']
      ext: '.gz', // 生成的压缩包后缀
    })
  ]
})
```
### vite-plugin-imagemin 图片压缩
安装
```shell
pnpm install vite-plugin-imagemin -D
```
配置
```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueSetupExtend from 'vite-plugin-vue-setup-extend'
import legacy from '@vitejs/plugin-legacy'
import compressPlugin from 'vite-plugin-compression'
import viteImagemin from 'vite-plugin-imagemin'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
      vue(),
    vueSetupExtend(),
    legacy({
      targets: ['defaults', 'not IE 11'],
      additionalLegacyPolyfills: ['regenerator-runtime/runtime']
    }),
    compressPlugin({
      verbose: false, // 是否在控制台输出压缩结果
      disable: false, // 是否禁用
      threshold: 10240, // 文件容量大于这个值进行压缩，它将被压缩，单位为b
      algorithm: 'gzip', // 压缩算法 可选 ['gzip','brotliCompress' ,'deflate','deflateRaw']
      ext: '.gz', // 生成的压缩包后缀
    }),
    viteImagemin({
      gifsicle: {
        optimizationLevel: 7,
        interlaced: false,
      },
      optipng: {
        optimizationLevel: 7,
      },
      mozjpeg: {
        quality: 20,
      },
      pngquant: {
        quality: [0.8, 0.9],
        speed: 4,
      },
      svgo: {
        plugins: [
          {
            name: 'removeViewBox',
          },
          {
            name: 'removeEmptyAttrs',
            active: false
          }
        ]
      }
    })
  ]
})
```