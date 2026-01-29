---
title: 创建UTXO
tags: BTC
categories: Web3
abbrlink: e5a32ca1
date: 2024-08-08 18:00:20
cover: https://s21.ax1x.com/2024/08/08/pkzactO.png
top_img: https://s21.ax1x.com/2024/08/08/pkzactO.png
---
## BTC创建UTXO并用Joy ID广播
### 安装依赖
```shell
pnpm install bitcoinjs-lib tiny-secp256k1 axios @joyid/bitcoin
```
### 获取剩余UTXO
```ts
const getUtxos = async (address: string) => {
    try {
        const response = await axios.get(`https://blockstream.info/testnet/api/address/${address}/utxo`);
        return response.data;
    } catch (error) {
        console.error('Error fetching UTXO:', error);
        throw error;
    }
};
```
### 创建交易
```ts
    const [txHex, setTxHex] = useState('');
    const createTransaction = async () => {
    const network = bitcoin.networks.testnet; // 使用比特币测试网络
    const senderAddress = address;
    const recipientAddress = 'tb1qp4gpusm5vq7ysa38wa92a6n8ps89hp7ts2907m';
    const amountToSend = 0.0001 * 1e8; // 发送的金额，单位是聪（satoshis）
    // 获取 UTXO（未花费的交易输出）
    const utxos = await getUtxos();

    if (utxos.length === 0) {
        throw new Error('No UTXOs available for the address.');
    }
    const utxo = utxos[0]; // 使用第一个 UTXO，实际应用中可能需要选择最佳 UTXO

    // 检查 txid 是否有效
    if (!/^[0-9a-fA-F]{64}$/.test(utxo.txid)) {
        throw new TypeError('Invalid txid');
    }
    // 创建 PSBT
    const psbt = new bitcoin.Psbt({ network });
    try {
        psbt.addInput({
            hash: utxo.txid,
            index: utxo.vout,
            witnessUtxo: {
                script: bitcoin.address.toOutputScript(senderAddress, network),
                value: utxo.value,
            },
        });
    } catch (error) {
        throw error;
    }
    try {
        psbt.addOutput({
            address: recipientAddress,
            value: amountToSend,
        });
    } catch (error) {
        throw error;
    }

    // 找零地址和找零金额，如果有的话
    const changeAddress = senderAddress;
    const fee = 0.00001 * 1e8; // 示例费用
    const changeValue = utxo.value - amountToSend - fee;

    if (changeValue > 0) {
        try {
            psbt.addOutput({
                address: changeAddress,
                value: changeValue,
            });
        } catch (error) {
            throw error;
        }
    }
    const psbtHex = psbt.toHex();
    setTxHex(psbtHex)
};
```
### 监听到psbtHex创建完成捕获并使用JoyID广播
```ts
import { sendPsbt } from "@joyid/bitcoin";
const sendContract = async () => {
    const tx = await sendPsbt(txHex)
    console.log(tx, 'tx');
}
```