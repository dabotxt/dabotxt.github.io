---
title: 微信支付JSAPI前端调起支付
abbrlink: 4a17b158
cover: https://s1.ax1x.com/2022/04/28/LOHQrF.jpg
tags: WeiXinPay
categories: 微信开发
date: 2022-04-28 12:00:00
---
在微信浏览器里面打开H5网页中执行JS调起支付。接口输入输出数据格式为JSON。
## 注意：WeixinJSBridge内置对象在其他浏览器中无效。

### 1、网页端接口请求参数列表

``` ts
{
  appid: string, // appId为当前服务商号绑定的appid
  timeStamp: string, // 当前的时间
  nonceStr: string, // 随机字符串，不长于32位。
  package: string, // 统一下单接口返回的prepay_id参数值
  signType: string, // 签名类型，默认为MD5，支持HMAC-SHA256和MD5。
  paySign: string // 签名
}
```

以上参数皆是必填项统一由后端接口返回参数

### 2、返回结果值说明

``` ts
 get_brand_wcpay_request:ok // 支付成功
 get_brand_wcpay_request:cancel // 支付过程中用户取消
 get_brand_wcpay_request:fail // 支付失败
```

注：JS API的返回结果get_brand_wcpay_request:ok仅在用户成功完成支付时返回。由于前端交互复杂，get_brand_wcpay_request:cancel或者get_brand_wcpay_request:fail可以统一处理为用户遇到错误或者主动放弃，不必细化区分。

### 3、前端封装方法

``` ts
declare let WeixinJSBridge: any;

/**
 * @description: 通过JSAPI下单API成功获取预支付交易会话标识（prepay_id）后，需要通过JSAPI调起支付API来调起微信支付收银台
 * 注1：WeixinJSBridge内置对象在其他浏览器中无效
 * 注2：此API需要将请求参数进行签名（参与签名的参数为：appId、timeStamp、nonceStr、package，参数区分大小写）
 * @param {*}
 * @return {*}
 */
function onBridgeReady (res: any)
{
  /* eslint-disable-next-line */
  WeixinJSBridge.invoke('getBrandWCPayRequest', {
      // 公众号ID，由商户传入
      'appId': res.appId,
      // 时间戳（转为String类型，兼容IOS “未传timeStamp”问题）
      'timeStamp': String(res.timeStamp),
      // 随机串
      'nonceStr': res.nonceStr,
      // 预支付交易会话标识
      'package': res.package,
      // 微信签名方式
      'signType': res.signType,
      // 微信签名
      'paySign': res.paySign
    },
    function (result: any)
    {
      // 微信团队郑重提示：res.err_msg将在用户支付成功后返回ok，但并不保证它绝对可靠。

      // 支付成功
      if (result.err_msg === 'get_brand_wcpay_request:ok')
      {
        console.log('支付成功');
      }

      // 支付取消
      if (result.err_msg === 'get_brand_wcpay_request:cancel')
      {
        console.log('支付取消');
      }

      // 支付失败
      if (result.err_msg === 'get_brand_wcpay_request:fail')
      {
        console.log('支付失败');
      }
    })
}

/**
 * @description: 调起微信JSAPI支付
 * @param {*}
 * @return {*}
 */
function bootWechatJSAPIPay (res: any)
{
  let document: any;
  if (typeof WeixinJSBridge === 'undefined')
  {
    if (document.addEventListener)
    {
      document.addEventListener('WeixinJSBridgeReady', onBridgeReady, false);
    }
    else if (document.attachEvent)
    {
      document.attachEvent('WeixinJSBridgeReady', onBridgeReady);
      document.attachEvent('onWeixinJSBridgeReady', onBridgeReady);
    }
  }
  else {
    onBridgeReady(res)
  }
}

// 调起微信JSAPI支付
export {
  bootWechatJSAPIPay
}

```

以上typScript方法封装
### 4、前端获取openID方法
``` ts
window.location.replace(`${baseURL}/api/v1/wechat/oauth?authorization=${sign}&returnUrl=${url.href}`)
```
### 5、文件内方法调用

``` ts
import { bootWechatJSAPIPay } from 'src/hooks/pay'
bootWechatJSAPIPay(param)
```
如果文档内容没有解决您的问题，您还可以前往 [微信开放社区](https://developers.weixin.qq.com/community/pay) 寻求帮助