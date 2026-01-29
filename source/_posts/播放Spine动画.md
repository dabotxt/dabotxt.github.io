---
title: 播放Spine动画
abbrlink: 35eaf0f1
date: 2024-08-08 18:20:44
tags: Animate
categories: 前端动画
cover: https://zh.esotericsoftware.com/img/core/spine_logo_bright.svg
top_img: https://zh.esotericsoftware.com/img/core/spine_logo_bright.svg
---
### 安装spine官方依赖
```shell
pnpm add @esotericsoftware/spine-player
```
### 播放函数(需将动画文件传到CDN或保存到项目文件夹)
```ts
const playerContainerRef: any = useRef<HTMLDivElement>(null);

const loadFiles = () => {
    const skeletonJsonUrl = `https://xxx/atlas/box-gem/box.json`
    const atlasUrl = `https://xxx/atlas/box-gem/box.atlas`
    const player: any = new SpinePlayer(playerContainerRef.current, {
        jsonUrl: skeletonJsonUrl,
        atlasUrl: atlasUrl,
        animation: 'idle',
        alpha: true,
        showLoading: false
    } as any);
    playerContainerRef.current.style.pointerEvents = 'none';
};

useEffect(() => {
    loadFiles()
}, []);
```
