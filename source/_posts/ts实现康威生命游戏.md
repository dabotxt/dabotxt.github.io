---
title: ts实现康威生命游戏
tags: TypeScript
categories: TypeScript
cover: 'https://s1.ax1x.com/2022/05/05/OeYnqe.png'
top_img: 'https://s1.ax1x.com/2022/05/05/OeY4iR.png'
abbrlink: 8cc9b930
date: 2024-08-12 11:54:00
---
#### 生命游戏中，对于任意细胞，规则如下：
1. 每个细胞有两种状态 - 存活或死亡，每个细胞与以自身为中心的周围八格细胞产生互动（如图，黑色为存活，白色为死亡）
2. 当前细胞为存活状态时，当周围的存活细胞低于2个时（不包含2个），该细胞变成死亡状态。（模拟生命数量稀少）
3. 当前细胞为存活状态时，当周围有2个或3个存活细胞时，该细胞保持原样。
4. 当前细胞为存活状态时，当周围有超过3个存活细胞时，该细胞变成死亡状态。（模拟生命数量过多）
5. 当前细胞为死亡状态时，当周围有3个存活细胞时，该细胞变成存活状态。（模拟繁殖）
6. 可以把最初的细胞结构定义为种子，当所有在种子中的细胞同时被以上规则处理后，可以得到第一代细胞图。按规则继续处理当前的细胞图，可以得到下一代的细胞图，周而复始。

```ts
export class GameOfLife {
    private activeCells: Array<[number, number]>;

    constructor() {
        this.activeCells = []; // 初始化矩阵
    }

    addCell(x: number, y: number): void { // 添加活细胞亮点
        this.activeCells.push([x, y]);
    }

    removeCell(x: number, y: number): void { // 删除活细胞亮点
        this.activeCells = this.activeCells.filter(([cx, cy]) => cx !== x || cy !== y);
    }

    isActive(x: number, y: number): boolean { // 验证该位置是否有存活的细胞
        return this.activeCells.some(([cx, cy]) => cx === x && cy === y);
    }

    private getNeighbors(x: number, y: number): Array<[number, number]> {
        const neighbors: Array<[number, number]> = [];
        for (let dx = -1; dx <= 1; dx++) {
            for (let dy = -1; dy <= 1; dy++) {
                if (dx === 0 && dy === 0) continue;
                neighbors.push([x + dx, y + dy]);
            }
        }
        return neighbors;
    }

    private countActiveNeighbors(x: number, y: number): number {
        return this.getNeighbors(x, y).reduce(
            (count, [nx, ny]) => count + (this.isActive(nx, ny) ? 1 : 0),
            0
        );
    }

    step(): void { // 执行一次规则
        const toEvaluate = new Set<string>();
        const nextState: Array<[number, number]> = [];

        this.activeCells.forEach(([x, y]) => {
            toEvaluate.add(`${x},${y}`);
            this.getNeighbors(x, y).forEach(([nx, ny]) => {
                toEvaluate.add(`${nx},${ny}`);
            });
        });

        toEvaluate.forEach((cell: string) => {
            const [x, y] = cell.split(",").map(Number);
            const isActive = this.isActive(x, y);
            const activeNeighbors = this.countActiveNeighbors(x, y);
            if (
                (isActive && (activeNeighbors === 2 || activeNeighbors === 3)) ||
                (!isActive && activeNeighbors === 3)
            ) {
                nextState.push([x, y]);
            }
        });

        this.activeCells = nextState;
    }
}
```