---
title: pinia
abbrlink: b51dc851
date: 2022-05-09 15:59:30
cover: https://s1.ax1x.com/2022/05/09/OG6eoT.png
top_img: https://s1.ax1x.com/2022/05/09/OG6YTK.png
tags: Pinia
categories: Vue
---
### Pinia与Vuex的不同
Pinia是Vue.js团队开发的一个全新的状态管理库，并且Pinia符合Vuex5的提案，已被纳入官方GitHub。  
Vuex： State、Gettes、Mutations(同步)、Actions(异步)  
Pinia： State、Gettes、Actions(同步异步都支持)  
### Pinia的核心特性
Pinia没有Mutations、Actions支持同步和异步、没有模块的嵌套结构（每个store互相独立）、更好的TypeScript支持、Vue2和Vue3都支持。
### 安装
```shell
pnpm add pinia
```
### Pinia初始化设置
main.ts文件
```ts
import { createPinia } from 'pinia'
createApp(App).use(createPinia()).mount('#app')
```
在store目录下面新建一个user.ts
```ts
import { defineStore } from 'pinia'
export const userStore = defineStore('user', {
    state: () => {
        return { 
            count: 1
        }
    },
    getters: {
        getCount() {
            return this.count
        }
    },
    actions: {
        doubleCount() {
            this.count = this.count * 2
        }
    }
})
```
### 访问state
``` vue
<script setup lang="ts">
import { UserStore } from '../store/user'
import { computed } from 'vue'
const user = UserStore()
const count = computed(() => {
  return user.count
})
</script>

<template>
  <van-button type='primary' @click='user.doubleCount'>Double</van-button>
  <span>{{count}}</span>
</template>

<style>
</style>
```
### 异步action
action支持async/await的语法来支持异步处理的场景。
``` ts
import { defineStore } from 'pinia'
export const userStore = defineStore('user', {
    state: () => {
        return { 
            count: 1
        }
    },
    getters: {
        getCount() {
            return this.count
        }
    },
    actions: {
        doubleCount() {
            this.count = this.count * 2
        },
        async login(account, passWord) {
            const { data } = await api.login(account, passWord)
            return data
        }
    }
})
```