---
title: REST API
date: 2022-10-28 09:50:56
---
# Pay center Api

## Version: 1.0.0

**Contact information:**  
renlikang@dalongyun.com  

### **签名规则**
***

+ **签名算法**
>RSA  
> Key格式：PKCS8  
> 签名算法：SHA256WithRSA  
> 密钥长度：2048  
> 生成公私钥后使用base64编码后输出

+ **公私钥生成**
>首选安装openssl，参考文档：https://www.openssl.org/source/  
> 商户可以通过如下命令生成公私钥：
>
>
> &emsp;&emsp;1.#生成后的公私钥文件为pem格式，然后将换行符替换成空  
> &emsp;&emsp;2.#私钥  
> &emsp;&emsp;3.openssl genrsa 2048 | openssl pkcs8 -topk8 -nocrypt -out private.key.pem   
> &emsp;&emsp;4.#公钥  
> &emsp;&emsp;5.openssl rsa -in private.key.pem -pubout > public.key.pem
>
>
> 执行完上述命令后，会生成两个文件private.key.pem和 public.key.pem，其中private.key.pem为私钥，在请求交易时签名报文，由商户自行保管；public.key.pem 为公钥，需通过Merchant Dashboard平台上传上传给 PayerMax。
public.key.pem的内容如下（示例）：  
> MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAybRQ39Dmt+Re1pVmNHzC
dVdMpSYauLB5Ydb1yiDZBObneSrBwcDDHm3Jbn6/Gpzoo3lgX+kiR0PtYszpybjI
Zo3Xu7rhOE7//VfYvHWHLuxeglaUnsPGc37fVB8FM+HB3AfI+Jpw/HMAFZGHFZZ8
xcGbgW5n4S0d6ExBu/RDjX0ssZeDRWG/NGmuVukmu79RlX2+gmaIth5R+TI+fVL1
tqlQ5bYenTt1iCYUQXjfuNaeRQvxxjgXCtwKkhsg+gRKJRTypDsUxOxq0qRmn7fZ
X8Lw9LJsbiIRG6u4EupSiPeU2rqAELIhLFvR57KTapQ+emS6FTdd651KZK97qYy7
fQIDAQAB

+ **签名**
>将请求报文ASCII排序组成对象后转换成json string后使用商户RSA私钥进行签名，将签名获得的值放入http header中，key为sign

### **创建订单**
***

+ **请求url**

|    环境    |                                  请求URL                                  | 
|:--------:|:-----------------------------------------------------------------------:|
| 测试环境 |  https://developapidev.joyark.com/en/pay-center/recharge-order/create   |
| 生产环境 |     https://api-game.joyark.com/en/pay-center/recharge-order/create     |

+ **请求方式**
>Post

***
+ **header参数**

|   请求参数   | 必选  | 参数类型 |  说明  |
|:--------:| :---: | :------: |:----:|
|   sign   |  是   |  sring   |  签名  |
| Content-Type |  是   |  sring   |  application/json  |

***
+ **请求参数**

|    请求参数    | 必选  |  参数类型  |   长度   |                                        说明                                         |
|:----------:|:---:|:------:|:------:|:---------------------------------------------------------------------------------:|
| timestamp  |  是  | sring  |   32   | 请求时间，符合rfc3339规范，格式：yyyy-MM-dd’T’HH:mm:ss.SSSXXX 事例：2022-01-22T10:00:00.500+08:00 |
|  payType   |  是  | sring  |   30   |                             支付方式： Payermax Airwallex                              |
|   mchId    |  是  |  int   |   64   |                                       平台商户号                                       |
|    good    |  是  | sring  |  100   |                                       商品名称                                        |
|  currency  |  是  | sring  |   3    |                                     币种代码，大写字母                                     |
|   money    |  是  | sring  | (10,2) |                                       支付金额                                        |
| returnUrl  |  是  | sring  |  256   |                                       跳转地址	                                       |
|  country   |  否  | sring  |   3    |                                     国家代码，大写字母                                     |
|   userId   |  否  |  int   |   64   |                                       用户ID                                        |
|  language  |  否  | sring  |   8    |                                      收银台页面语言                                      |
| productId  | 有条件 | string |   64   |                               payType= Payermax 必填                                |
| +products  |  有条件  | Array | Array  |                              payType= Airwallex 必填                                              |
|    code    |  否  | sring  |   64   |                                       商品ID                                        |
|    desc    |  否  | sring  |  100   |                                       商品描述                                        |
|    name    |  否  | sring  |  100   |                                       商品标题                                        |
|  quantity  |  否  |  int   |   16   |                                       商品数量                                        |
| unit_price |  否  | (10,2) |  手机号码  |                                       商品单价                                        |
|    type    |  否  |  int   |   4    |                                       商品类型                                        |

+ **请求示例**
~~~ js
{
"currency": "SGD", 
"good": "test", 
"mchId": 10002, 
"money": "10.00", 
"payType": "payermax", 
"returnUrl": "https://webdev.bifrost.com/v1/pay/payerMaxNotify", 
"timestamp": "2022-10-12T07:40:23.371Z", 
"userId": "123456"
}
~~~

+ **返回参数**
>返回参数


|      返回参数      | 参数类型  |                                        说明                                         |
|:--------------:|:-----:|:---------------------------------------------------------------------------------:|
|   timestamp    | sring | 请求时间，符合rfc3339规范，格式：yyyy-MM-dd’T’HH:mm:ss.SSSXXX 事例：2022-01-22T10:00:00.500+08:00 |
|      code      |  int  |                                        状态码                                        | 200 正常 
|      msg       | sring |                                       错误信息                                        |
|     +data      | array |                                       返回数据                                        |
|    orderNo     | sring |                                       平台订单号                                       |
|      url       | sring |                                       收银台页面                                       |
| thirdOrderSign | sring |                                payType=PayerMax时返回                                |

> 错误返回参数

|   返回参数   | 参数类型 |  说明  |
|:--------:| :------: |:----:|
|   code   |   int    | 状态码  |
|   msg    |  sring   | 错误信息 |
|   timestamp    |  sring   | 请求时间，符合rfc3339规范，格式：yyyy-MM-dd’T’HH:mm:ss.SSSXXX 事例：2022-01-22T10:00:00.500+08:00 |
~~~ js
// 示例
{
    "code": 200,
    "data": {
        "url": "https://cashier-n-uat.payermax.com/index.html#/cashier/home?merchantId=010113835116808&merchantAppId=95aff0f7ffe445e5a5fad276d88c79a1&tradeToken=TOKEN202210270000381115&language=en&token=4334dca7036b4c88909bfd937910da6d&frontCallbackUrl=https%3A%2F%2Fdevelopapidev.joyark.com%2Fen%2Fpay-center%2Forder%2Freturn%3FreturnUrl%3Dhttps%3A%2F%2Fwebdev.bifrost.com%2Fv1%2Fpay%2FpayerMaxNotify%26payStatus%3D1&version=1.0",
        "orderNo": "443.PA.20221027-20151109-10982861-08710506-2290",
        "thirdOrderSign": "003202210270851172290"
    },
    "msg":"SUCCESS",
    "timestamp": "2022-10-27T08:51:19.000+00:00"
}
~~~


### **查询订单**
***

+ **请求url**

|    环境    |                                 请求URL                                 | 
|:--------:|:---------------------------------------------------------------------:|
| 测试环境 |  https://developapidev.joyark.com/en/pay-center/recharge-order/info   |
| 生产环境 |  https://api-game.joyark.com/en/pay-center/recharge-order/info   |

+ **请求方式**
>Post

***
+ **header参数**

|   请求参数   | 必选  | 参数类型 |  说明  |
|:--------:| :---: | :------: |:----:|
|   sign   |  是   |  sring   |  签名  |
| Content-Type |  是   |  sring   |  application/json  |

***
+ **请求参数**

|    请求参数    | 必选  |  参数类型  |   长度   |                                        说明                                         |
|:----------:|:---:|:------:|:------:|:---------------------------------------------------------------------------------:|
| timestamp  |  是  | sring  |   32   | 请求时间，符合rfc3339规范，格式：yyyy-MM-dd’T’HH:mm:ss.SSSXXX 事例：2022-01-22T10:00:00.500+08:00 |
|  orderNo   |  是  | sring  |   30   |                                       平台订单号                                       |
|   mchId    |  是  |  int   |   64   |                                       平台商户号                                       |

+ **请求示例**
~~~ js
{
"mchId": 10002, 
"orderNo": "443.PA.20221027-20151109-10982861-08710506-2290", 
"timestamp": "2022-10-12T07:40:23.371Z", 
}
~~~

+ **返回参数**
>正确返回参数


|      返回参数      |  参数类型  |                                        说明                                         |
|:--------------:|:------:|:---------------------------------------------------------------------------------:|
|   timestamp    | sring  | 请求时间，符合rfc3339规范，格式：yyyy-MM-dd’T’HH:mm:ss.SSSXXX 事例：2022-01-22T10:00:00.500+08:00 |
|      code      |  int   |                                        状态码                                        | 200 正常 
|      msg       | sring  |                                       错误信息                                        |
|     +data      | array  |                                       返回数据                                        |
|    orderNo     | sring  |                                       平台订单号                                       |
|      amount       | string |                                       支付金额                                        |
| payTime | sring  |                                       支付时间                                        |
| status | sring  |          CREATE-创建，PAYING-支付中，IN_PROGRESS-支付成功，FAILED-支付失败，REVERSED-取消订单          |

> 错误返回参数

|   返回参数   | 参数类型 |  说明  |
|:--------:| :------: |:----:|
|   code   |   int    | 状态码  |
|   msg    |  sring   | 错误信息 |
|   timestamp    |  sring   | 请求时间，符合rfc3339规范，格式：yyyy-MM-dd’T’HH:mm:ss.SSSXXX 事例：2022-01-22T10:00:00.500+08:00 |
~~~ js
// 正确
{
    "code": 200,
    "data": {
        "amount": "10.00",
        "orderNo": "443.PA.20221027-20151109-10982861-08710506-2290",
        "payTime": null,
        "status": "REVERSED",
    },
    "msg":"SUCCESS",
    "timestamp": "2022-10-27T08:51:19.000+00:00"
}
~~~


### **异步通知**
***
> 1.同样的通知可能会多次发送给商户系统。商户系统须能够正确的处理重复通知。  
> 2.后台通知交互时，如果平台收到商户的应答不符合规范或超时，会判定本次通知失败，重新发送通知，直到成功为止（在通知一直不成功的情况下，会发一直通知）.  
> 3.在订单状态不明或者没有收到平台支付结果通知的情况下，建议商户主动调用【查询订单】接口确认订单状态。




+ **通知方式**

>Post

***
+ **header参数**

|   请求参数   | 必选  | 参数类型 |  说明  |
|:--------:| :---: | :------: |:----:|
|   sign   |  是   |  sring   |  签名  |
| Content-Type |  是   |  sring   |  application/json  |

***
+ **接口参数**

|    请求参数    | 必选  |  参数类型  |   长度   |                                        说明                                         |
|:----------:|:---:|:------:|:------:|:---------------------------------------------------------------------------------:|
| timestamp  |  是  | sring  |   32   | 请求时间，符合rfc3339规范，格式：yyyy-MM-dd’T’HH:mm:ss.SSSXXX 事例：2022-01-22T10:00:00.500+08:00 |
|  orderNo   |  是  | sring  |   30   |                                       平台订单号                                       |
|   type    |  是  |  int   |   4    |                                          订单类型 1 payment订单 2 subscription订单                                    |
|   status    |  是  |  string   |   64   |                       CREATE-创建，PAYING-支付中，IN_PROGRESS-支付成功，FAILED-支付失败，REVERSED-取消订单                                                       |
|   amount    |  是  |  string   | (10,2) |                                  商户传入的订单金额                                            |
|   currencyCode    |  是  |  string   |   3    |                            订单币种	                                                  |
|   payTime    |  是  | string |   32   |                                     支付时间                                            |


+ **返回**
>成功时，http返回状态码为200