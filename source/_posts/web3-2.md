---
title: web3.js监听用户切换钱包状态
tags: Web3.js
categories: Web3
cover: 'https://s1.ax1x.com/2022/05/10/OtyPTx.jpg'
top_img: 'https://s1.ax1x.com/2022/05/10/OtyvUP.jpg'
abbrlink: eff061d5
date: 2022-05-10 16:17:38
---
### Web3js获取MetaMask钱包并监听切换
官方API方法监听账号切换
```ts
import { UserStore } from 'store/modules/user'
export function useWeb3() {
    async function getAccount(): Promise<any> {
        return new Promise((async (resolve, reject) => {
            await isMetamask()
                .then((res: string) => {
                    console.log(res)
                    window.ethereum
                        .request({method: 'eth_requestAccounts'})
                        .then((res: string[]) => {
                            onEvents()
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
            if (typeof window.ethereum !== 'undefined') {
                resolve('MetaMask is installed!')
            } else {
                reject('MetaMask is uninstalled!')
            }
        })
    }
    async function onEvents () {
        const user = UserStore()
        //  一旦切换账号这里就会执行
        window.ethereum.on("accountsChanged", function(accounts: string[]) {
            if (accounts.length > 0) {
                // 账号切换后重新存储钱包地址
                user.setWalletAddress(accounts[0])
            }
        })
    }
    return {
        getAccount
    }
}
```
### 账号切换后使用新的钱包地址重新获取页面数据
``` vue
<script setup lang="ts">
import { UserStore } from 'store/modules/user'
import { computed, watch } from 'vue'
const user = UserStore()
defineProps<{ msg: string }>()
const getAddress = async () => {
  await user.getWallet()
}
const walletAddress = computed((): string | null => {
  return user.getWalletAddress
})
watch( // 监听到用户钱包地址发生变化，重新执行获取钱包余额事件
  () => user.getWalletAddress,
  (walletAddress) => {
    walletAddress && getBalance()
  }
)
</script>

<template>
  <ElButton @click="getAddress" type="primary">连接钱包</ElButton>
  <h1>{{ msg }}</h1>
  <div>钱包地址：{{walletAddress}}</div>
</template>

<style scoped>
</style>
```