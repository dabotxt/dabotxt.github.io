---
title: Web3.js安装使用方法
abbrlink: 71ab26c1
date: 2022-05-10 15:52:10
cover: https://s1.ax1x.com/2022/05/10/OtyPTx.jpg
top_img: https://s1.ax1x.com/2022/05/10/OtyvUP.jpg
tags: Web3.js
categories: Web3
---
### web3.js 库是一系列模块的集合，服务于以太坊生态系统的各个功能，如：
web3-eth 用来与以太坊区块链及合约的交互  
web3-shh Whisper 协议相关，进行p2p通信和广播  
web3-bzz swarm 协议（去中心化文件存储）相关  
web3-utils 包含一些对 DApp 开发者有用的方法  
### 引入web3.js
```shell
pnpm add web3
```
### 项目构建web3文件
在utils目录下新建useWeb3.ts
```ts
import { UserStore } from 'store/modules/user'
export function useWeb3() {
    // 获取钱包地址
    async function getAccount(): Promise<any> {
        return new Promise((async (resolve, reject) => {
            await isMetamask()
                .then((res: string) => {
                    // 获取钱包地址授权
                    window.ethereum.request({method: 'eth_requestAccounts'})
                        .then((res: string[]) => {
                            resolve(res)
                        })
                        .catch((res: string[]) => {
                            reject(res)
                        })
                })
                .catch((res: string) => {
                    reject(res)
                })
        }))
    }
    function isMetamask(): Promise<string> {
        return new Promise((resolve, reject) => {
            // 这一步骤是判断当前访问浏览器是否安装MetaMask
            if (typeof window.ethereum !== 'undefined') {
                resolve('MetaMask is installed!')
            } else {
                reject('MetaMask is uninstalled!')
            }
        })
    }
    return {
        getAccount
    }
}
```
### 配合store使用，将钱包地址存储到store里面并显示出来
store.ts文件
```ts
import { defineStore } from 'pinia'
import { useWeb3 } from 'src/utils/useWeb3'
import { store } from '../index'
interface UserState {
  walletAddress: string | null
}
export const useUserStore = defineStore({
  id: 'app-user',
  state: (): UserState => ({
    walletAddress: null
  }),
  getters: {
    getWalletAddress(): string | null {
        // 将store里存储的钱包地址Return出去
      return this.walletAddress
    }
  },
  actions: {
    setWalletAddress(walletAddress: any | null) {
        // 获取钱包地址后存储到store里面
      this.walletAddress = walletAddress
    },
    async getWallet () {
        // 获取小狐狸授权，并获取钱包地址
      const { getAccount } = useWeb3()
      const account = await getAccount()
      this.setWalletAddress(account[0])
    }
  }
})
export function UserStore() {
  return useUserStore(store)
}
```
### 项目文件使用方法
``` vue
<script setup lang="ts">
import { UserStore } from 'store/modules/user'
import { computed } from 'vue'
const user = UserStore()
const getAddress = async () => {
  await user.getWallet()
}
const walletAddress = computed((): string | null => {
  return user.getWalletAddress
})
</script>

<template>
  <ElButton @click="getAddress" type="primary">连接钱包</ElButton>
  <div>钱包地址：{{walletAddress}}</div>
</template>

<style scoped>
</style>
```