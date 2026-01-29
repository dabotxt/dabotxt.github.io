---
title: OKX插件登录BTC
abbrlink: 4c3e688d
date: 2024-08-09 15:05:01
tags: BTC
categories: Web3
cover: '/images/okx-banner.png'
top_img: '/images/okx-banner.png'
---
### 由于BTC网络的登录并不像wallet connect EVM网络那样使用现成的包直接可以建立连接器只要你浏览器内部装有OKX插件就可以直接唤起并登录具体方法如下

#### PC端登录方法
```ts
const connectBTC = () => {
    if (typeof (window as any).okxwallet !== 'undefined') {
        const res = await (window as any).okxwallet.bitcoin.requestAccounts();
    } else {
        console.log('OKX Wallet is not installed.');
    }
}
```
#### PC发起交易 目前仅支持BTC主网
```ts
const sendBTC = () => {
    try {
        await (window as any).okxwallet.bitcoin.sendBitcoin(
            acceptAddress, // 接收地址
            6000, // 发送金额 单位：聪
            {
                feeRate: 10 // 指定费率
            }
        );
    } catch (error) {
        console.error(error)
    }
}
```
#### 移动端登录跟PC端登录有显著区别因为移动端浏览器没法装插件钱包需要跳转到OKX的App中再执行登录操作
```ts
const checkOkxWallet = async () => {
    // 检查是否监听到浏览器插件
    if (typeof (window as any).okxwallet !== 'undefined') {
        try {
            const accounts = await (window as any).bitcoin.requestAccounts();
        } catch (e) {
            console.log('connect failed', e);
        }
    } else {
        initOKXWallet()
    }
};
const initOKXWallet = () => {
    // 调转到OKX App的内部浏览器
    const encodedUrl = "https://www.okx.com/download?deeplink=" + encodeURIComponent("okx://wallet/dapp/url?dappUrl=" + encodeURIComponent(location.href));
    window.location.href = encodedUrl;
}
```
#### 移动端OKX使用BTC发送交易
```ts
    const okxSend = async () => {
    const acceptAddress = 'your btc address'
    try {
        const txid = await (window as any).bitcoin.sendBitcoin(
            acceptAddress, // 收款地址
            6000, // 金额单位：聪
            {
                feeRate: 10 // 费率
            }
        );
        console.log(txid, 'tx');
    } catch (error) {
        console.log(error);
    }
}
```
#### 总的来说还是身份方便的，省去了构建UTXO的烦恼