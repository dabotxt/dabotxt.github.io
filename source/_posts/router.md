---
title: router
abbrlink: 45d2f225
date: 2022-05-09 17:28:11
tags: Vue
categories: Vue
cover: https://s1.ax1x.com/2022/04/29/LvoXHe.png
top_img: https://s1.ax1x.com/2022/04/29/LvTVEQ.jpg
---
vite现在创建的vue项目已经默认为vue3了，但是在引入vue-router时，可能会有vue-router报错问题，一直提示createRouter为undefined或者其他的关于vue-router报错的问题，主要原始是vue-router版本问题，直接升级到最新版本即可：
```shell
pnpm add vue-router@next
```
### 项目配置
创建router目录新建index.ts
```ts
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'
const routes: Array<RouteRecordRaw> = [
    {
        path: '/',
        name: 'Home',
        component: () => import('../views/page1.vue'),
        meta: {
            title: 'Home'
        }
    },
    {
        path: '/page2',
        name: 'Page2',
        component: () => import('../views/page2.vue'),
        meta: {
            title: 'Page2'
        }
    }
]
const router = createRouter({
    history: createWebHistory(),
    routes
})
export default router
```
### main.ts文件配置
```ts
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import { setupStore } from './store'

async function bootStrap() {
  const app = createApp(App)
  setupStore(app)
  app.use(router).mount('#app')
}
bootStrap()
```
### 路由守卫配置
router目录下创建guard.ts
```ts
import { Router } from 'vue-router'

export const setupRouterGuard = (router: Router) => {
  router.beforeEach((to, from, next) => {
    // @ts-ignore
    document.title = to.meta.title // 路由对应的网页标签title
    next()
  })
}
```
router目录下的index.ts文件配置
```ts
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'
import { setupRouterGuard } from './guard'
const routes: Array<RouteRecordRaw> = [
  {
    path: '/',
    name: 'Home',
    component: () => import('../views/page1.vue'),
    meta: {
      title: 'Home'
    }
  },
  {
    path: '/page2',
    name: 'Page2',
    component: () => import('../views/page2.vue'),
    meta: {
      title: 'Page2'
    }
  }
]
const router = createRouter({
  history: createWebHistory(),
  routes
})
setupRouterGuard(router)
export default router
```