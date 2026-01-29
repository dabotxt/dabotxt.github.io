---
title: Wagmi返回合约完整错误日志
abbrlink: c49bf0df
date: 2024-08-09 14:44:36
tags: Web3.js
categories: Web3
cover: 'https://s1.ax1x.com/2022/05/10/OtyPTx.jpg'
top_img: 'https://s1.ax1x.com/2022/05/10/OtyvUP.jpg'
---
### 使用Wallet Connect + Wagmi 集成EVM登录交易等操作遇到的一些问题
#### 使用useWriteContract发起合约交易直接打印错误日志返回的信息不完整官方文档也没有给到完整的Error信息返回处理，搜也搜不到，去wagmi的discord下面找到了用户自己写的一个解析Wagmi的方法如下
```ts
import { BaseError } from "wagmi";

const { data: hash, isPending, writeContractAsync, isSuccess: hashSuccess } = useWriteContract()

const massComputed = () => {
    writeContractAsync({
        address: contractAddress,
        abi: abi,
        functionName: 'contractFunction',
        gas: gasLimit
    })
        .then((res: any) => {
            console.log(res);
        })
        .catch((error: any) => {
            if (error) {
                const shortMessage = (error as BaseError)?.shortMessage;
                console.log(shortMessage);
            }
        })
}

```
### 目前react版本的解析方式是这样的vue版本目前没有好的办法，谁让这个框架是老外发明的呢，兼容性做的有点差