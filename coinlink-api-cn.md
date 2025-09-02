# Coinlink Open API 文档

- [Coinlink API](#coinlink-api)
- [接口规范](#接口规范)
- [1.用户相关接口](#用户相关接口)
  - [1.1 注册接口](#注册接口)
  - [1.2 登录接口](#登录接口)
  - [1.3 查询用户钱包信息](#查询用户钱包信息)
  - [1.4 链上 Hash 充值](#链上-hash-充值)
  - [1.5 查询用户GA状态](#查询用户GA状态)
  - [1.6 上传附件](#上传附件)

- [2.账户安全设置相关接口](#账户安全设置相关接口)
  - [2.1 获取GA秘钥](#获取GA秘钥) 
  - [2.2 获取邮箱验证码](#获取邮箱验证码)  
  - [2.3 绑定GA验证码](#绑定GA验证码)      

- [3.卡申请相关接口](#1用户接口)
  - [3.1 查询支持的卡种](#查询支持的卡种)
  - [3.2 查询当前卡种的kyc信息](#查询当前卡种的kyc信息)
  - [3.3 提交卡申请kyc第一步](#提交卡申请kyc第一步) 
  - [3.4 提交卡申请kyc第二步](#提交卡申请kyc第二步)   
  - [3.5 支付开卡费](#支付开卡费)   
  - [3.6 用户资金相关记录](#用户资金相关记录)   
  - [3.7 卡消费记录查询](#卡消费记录查询) 

---

## Coinlink API

欢迎使用 Coinlink API 文档。本文档为 Coinlink 平台对外开放的 API，涵盖用户注册、认证、钱包及链上交互等核心功能。  
本文档适用于需要对接 Coinlink 服务的合作伙伴与开发者，接口遵循 RESTful 设计规范，所有数据传输采用 `application/json` 格式。  

API 使用步骤：

1. 用户在平台完成注册。  
2. 用户通过邮箱 + 密码 + 谷歌验证码完成登录，获取授权 Token。  
3. 登录后调用钱包接口查询账户信息。  
4. 用户可通过链上交易 Hash 提交充值请求，完成账户资金注入。  
5. 用户申请开卡
6. 用户给卡充值

---

## 接口规范

- **请求协议**：HTTPS  
- **数据格式**：`application/json`  
- **字符集**：UTF-8  
- **认证方式**：除了注册/登录接口其他接口都需要携带 Token，放置于请求头 `authorization` 字段。  

### 统一返回格式

API 返回统一的 JSON 结构：

| 字段 | 类型   | 说明 |
|------|--------|------|
| code | int    | 状态码。0 表示成功，非 0 表示失败 |
| msg  | string | 状态描述信息 |
| result | object | 业务数据，不同接口返回结构不同 |

示例：

```json
{
  "code": 0,
  "msg": "SUCCESS",
  "result": { }
}
```







### 注册接口

- Request:

```text
url：/v1/admin/individual/register

method：POST
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
|  mail  |  String  |    Required     |  注册的邮箱    |
| password  |  String  |    Required     |  注册的密码     |
|  login_type  |  int  |    Required     | 默认传 4(个人用户登录)    |

- Response:

```
{
  "code": 0,
  "msg": "SUCCESS",
  "result": {
    "login_type": 4,
    "username": null,
    "user_type": "05",
    "mail": "frank@gmail.com",
    "role": [],
    "id": 890,
    "has_google_secret": false,
    "list_per": null,
    "list_permission": null,
    "basic_kyc_status": 0,
    "kyc_level": 0,
    "require_kyc_level": 1,
    "limit_amount": 0,
    "ga_enable": 1
  }
}

```

| Parameter      | Type    | Description |
|----------------|---------|-------------|
| login_type     | int     | 登录类型标识 |
| username       | string  | 用户名（可能为空） |
| user_type      | string  | 用户类型代码 |
| mail           | string  | 用户绑定邮箱 |
| role           | array   | 用户角色信息（为空表示没有角色） |
| id             | int     | 用户 ID |
| has_google_secret | bool  | 是否已绑定谷歌验证器 |
| list_per       | object  | 用户权限简要信息（如果有） |
| list_permission| object  | 用户权限明细（如果有） |
| basic_kyc_status | int   | 基础 KYC 状态 |
| kyc_level      | int     | 当前 KYC 等级 |
| require_kyc_level | int  | 业务要求的 KYC 等级 |
| limit_amount   | int     | 可用额度 |
| ga_enable      | int     | 是否开启谷歌验证（1 = 开启，0 = 未开启） |







### 登录接口

- Request:

```text
url：/v1/admin/individual/login

method：POST
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
|  mail  |  String  |    Required     |  注册的邮箱    |
| password  |  String  |    Required     |  注册的密码     |
|  login_type  |  String  |    Required     | 默认传 4(个人用户登录)   |

- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
}
```


| Parameter | Type   | Description |
|------|--------|------|
| code | int    | 状态码。0 表示成功，非 0 表示失败 |
| msg  | string | 状态描述信息 |







### 查询用户钱包信息

- Request:

```text
url：/v1/wallet/user

method：POST
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |

{}

- Response:

```
{
  "code": 0,
  "msg": "SUCCESS",
  "result": {
    "mail": "frank@gmail.com",
    "user_id": 890,
    "settings": {
      "card_fee_rate": "0.06",
      "card_min_amount": "20",
      "card_max_amount": "1000",
      "sepa_fee_rate": "0.015",
      "sepa_min_amount": "10",
      "sepa_max_amount": "10000",
      "transfer_fee_rate": "0",
      "announcement": "ANNOUNCEMENT SGD DFHD HRTJTYJ GFJ FGJ  ========###",
      "ach_fee_rate": "0.015"
    },
    "wallets": [
      {
        "currency": "USD",
        "address": "U4090823684",
        "balance": "0",
        "cust_type": null,
        "frozen_balance": "0",
        "total_balance": "0",
        "method": null
      }
    ],
    "individual_asset_list": [
      {
        "coin_type": "USDT-TRC20",
        "card_no": null,
        "address": "TFBQJH2eFZAY4LJS7Nco7oHeiMkbYqvVag",
        "status": null,
        "address_type": 0,
        "balance": 0
      }
    ]
  }
}
```

| Parameter              | Type   | Description |
|------------------------|--------|-------------|
| mail                   | string | 用户邮箱地址 |
| user_id                | int    | 用户 ID |
| settings.card_fee_rate | string | 卡片手续费率 |
| settings.card_min_amount | string | 卡片最小金额 |
| settings.card_max_amount | string | 卡片最大金额 |
| settings.sepa_fee_rate | string | SEPA 交易手续费率 |
| settings.sepa_min_amount | string | SEPA 交易最小金额 |
| settings.sepa_max_amount | string | SEPA 交易最大金额 |
| settings.transfer_fee_rate | string | 转账手续费率 |
| settings.announcement  | string | 公告信息 |
| settings.ach_fee_rate  | string | ACH 交易手续费率 |
| wallets                | array  | 钱包列表 |
| wallets.currency       | string | 钱包币种 |
| wallets.address        | string | 钱包地址 |
| wallets.balance        | string | 钱包余额 |
| wallets.cust_type      | string | 客户类型（如果有） |
| wallets.frozen_balance | string | 冻结余额 |
| wallets.total_balance  | string | 总余额 |
| wallets.method         | string | 钱包操作方式（如果有） |
| individual_asset_list  | array  | 个人资产列表 |
| individual_asset_list.coin_type | string | 资产类型（如 USDT-TRC20） |
| individual_asset_list.card_no  | string | 卡号（如果有） |
| individual_asset_list.address  | string | 地址 |
| individual_asset_list.status   | string | 状态（如果有） |
| individual_asset_list.address_type | int   | 地址类型 |
| individual_asset_list.balance | int   | 资产余额 |










### 链上 Hash 充值

- Request:

```text
url：/v1/account/card/deposit-coinlink-token

method：POST
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
|  coin_type  |  String  |    Required     |  coinType: <br/>支持 Ethereum的USDT/USDC <br/> 支持 Tron 的USDT-TRC20    |
| tx_hash  |  String  |    Required     |  链上hash     |


- Response:

```
{
  "code": 0,
  "msg": "SUCCESS"
}
```

| Parameter | Type   | Description |
|------|--------|------|
| code | int    | 状态码。0 表示成功，非 0 表示失败 |
| msg  | string | 状态描述信息 |











### 查询用户GA状态

- Request:

```text
url：/v1/common/security?mail=&login_type=4

GET
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
|  mail  |  String  |    Required     | 用户的邮箱    |
| login_type  |  int  |    Required     |  默认传4     |


- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": {
        "ga_enable": 1,
        "GABound": false
    }
}
```


| Parameter              | Type   | Description |
|------------------------|--------|-------------|
| ga_enable              | bool  | 用户是否启用GA true|false |
| GABound                | int   | 用户设置GA 已设置=1,0=未设置 |




 



### 上传附件

- Request:

```text
url：/v1/common/file/public

POST
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
|  file  |  MultipartFile  |    Required     | file控件,选择需要上传的文件(jpg,png等)    |


- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": "https://storage.sandbox.railone.io/f631ac4d-1ad0-4a15-96fc-207ae6ec2a312864440414014116381.png"
}
```


| Parameter              | Type   | Description |
|------------------------|--------|-------------|
| result              | String  | 返回文件的https连接 |







### 获取GA秘钥

- Request:

```text
url：/v1/common/security/google-authentication

GET
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
 


- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": {
        "qrCode": "otpauth://totp/frankdevitabc@gmail.com-1756804335078?secret=FQR5M5GWPNX477I5&issuer=Inst",
        "secret": "FQR5M5GWPNX477I5"
    }
}
```


| Parameter | Type   | Description                                      |
|-----------|--------|--------------------------------------------------|
| qrCode    | string | 二维码链接，用于生成一次性密码（TOTP）           |
| secret    | string | TOTP 密钥，用于生成一次性密码                    |




### 获取邮箱验证码

- Request:

```text
url：/v1/common/security/mail

GET
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
 


- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": "fa84a5cf-c4b1-4466-88b8-21350898664d"
}
```


| Parameter | Type   | Description                                      |
|-----------|--------|--------------------------------------------------|
| result    | string | 验证码token,用户指定行为验证           | 





### 绑定GA验证码

- Request:

```text
url：/v1/common/security/google-authentication/v2?mailCodeToken=fa84a5cf-c4b1-4466-88b8-21350898664d&bindingCode=485257&bindingKey=FQR5M5GWPNX4

GET
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
|  mailCodeToken  |  String  |    Required     | 获取的邮箱验证码token    |
|  bindingCode  |  String  |    Required     | GA验证码    |
|  bindingKey  |  String  |    Required     | GA 秘钥    |


- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": "true"
}
```


| Parameter | Type   | Description                                      |
|-----------|--------|--------------------------------------------------|
| result    | bool | 绑定是否成功          | 











### 查询支持的卡种

- Request:

```text
url：/v1/account/card/type

GET
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
 


- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": [
        {
            "id": 1,
            "card_type_id": "82000005",
            "bank_id": "8200",
            "currency_type": "USD",
            "description": "UQ2 Visa virtual Card USD 49372410",
            "card_network": "visa",
            "card_title": "UQ2 Visa virtual Card USD 49372410",
            "virtual_card": true,
            "card_no": "16357795820000059795",
            "status": 1,
            "card_type": 0,
            "card_application_fee": "2.3",
            "card_fee_currency": "USDT-TRC20",
            "deposit_transaction_fee": "1",
            "if_attachments": false,
            "enable": 1,
            "toc_enable": 1,
            "upstream_enable": 1,
            "create_time": null,
            "update_time": 1756613796000,
            "kyc_status": 2,
            "transaction_rules": "{\"min_single_deposit_amount\":\"1\",\"monthly_card_fee\":\"0\",\"transaction_fee\":\"1\",\"transaction_authorization_fee\":\"0.3\",\"failed_transaction_service_fee\":\"0.3\",\"review_time\":\"0.1\",\"supported_platform\":\"\",\"restricted_countries_of_application\":\"\",\"card_img\":\"https://storage.sandbox.railone.io/1c2cb2c4-4528-40d9-a08f-ed2dea8e7e346994537731169487857.png\"}",
            "applied_num": 5000,
            "keywords": ""
        }
    ]
}



```


| Parameter                     | Type    | Description                                                                 |
|-------------------------------|---------|-----------------------------------------------------------------------------|
| id                            | int     | 卡片的唯一标识符                                                           |
| card_type_id                 | string  | 卡片类型的唯一标识符                                                       |
| bank_id                      | string  | 关联银行的唯一标识符                                                       |
| currency_type                | string  | 货币类型，例如 USD                                                         |
| description                  | string  | 卡片的描述信息                                                             |
| card_network                 | string  | 卡片网络类型，例如 Visa                                                    |
| card_title                   | string  | 卡片的标题                                                                 |
| virtual_card                 | bool    | 是否为虚拟卡，true 表示是虚拟卡                                           |
| card_no                      | string  | 卡号                                                                       |
| status                       | int     | 卡片状态，1 表示启用，0 表示禁用                                           |
| card_type                    | int     | 卡片类型，0 可能表示某种特定类型                                          |
| card_application_fee         | string  | 申请此卡片所需的费用                                                       |
| card_fee_currency            | string  | 收费货币类型，例如 USDT-TRC20                                              |
| deposit_transaction_fee       | string  | 存款交易费用                                                               |
| if_attachments                | bool    | 是否有附件，false 表示没有附件                                             |
| enable                       | int     | 是否启用，1 表示启用，0 表示禁用                                          |
| toc_enable                   | int     | 是否启用条款和条件，1 表示启用                                            |
| upstream_enable              | int     | 上游服务是否启用，1 表示启用                                              |
| create_time                  | null    | 创建时间，当前为 null                                                      |
| update_time                  | long    | 最后更新时间的时间戳                                                      |
| kyc_status                   | int     | KYC 状态，2 可能表示某种特定状态                                          |
| transaction_rules             | string  | 交易规则的 JSON 字符串，例如最低单次存款金额和其他费用信息               |
| applied_num                   | int     | 申请的数量                                                                 |
| keywords                     | string  | 关键词，可以为空                                                             |










### 查询当前卡种的kyc信息

- Request:

```text
url：/v1/account/kyc/card?card_type_id=82000005

GET
```

|  Parameter  | Type  | Whether Required |                        Description                         |
| :---------: | :---: | :--------------: | :-------------------------------------------------------- |
|  card_type_id  |  String  |    Required     | 卡种Id    |

- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": [
        {
            "acct_id": 157,
            "acct_no": "36e8f4a7ae264f2b82f335f6e88d286d",
            "acct_name": "36e8f4a7ae264f2b82f335f6e88d286d",
            "first_name": "Ethan",
            "last_name": "Wilson",
            "gender": "male",
            "birthday": 1754265600000,
            "city": "shanghai",
            "state": "Indiana",
            "country": "CN",
            "doc_no": "123456",
            "doc_type": "passport",
            "front_doc": "https://storage.sandbox.railone.io/b3ee711b-6cdc-49c5-b182-96308d03c1fb1362567206565908725.png",
            "back_doc": null,
            "mix_doc": "https://storage.sandbox.railone.io/b3ee711b-6cdc-49c5-b182-96308d03c1fb1362567206565908725.png",
            "country_code": "86",
            "mobile": "1231213112",
            "mail": "aixingjuele@163.com",
            "address": "shanghai123",
            "status": 50,
            "auditor": "joshchow87@gmail.com",
            "reason": null,
            "kyc_info": null,
            "create_time": 1755502609000,
            "update_time": 1756805403000,
            "card_no": null,
            "card_number": null,
            "nationality": "CN",
            "zipcode": "20012-0",
            "maiden_name": "no",
            "bank_id": null,
            "card_type_id": "82000005",
            "active_doc": null,
            "poa_doc": null,
            "response": null,
            "bank_sign_file": null,
            "bank_kyc_resp": null,
            "authority": null,
            "date_of_issue": null,
            "date_of_expiry": null,
            "birth_place": null,
            "frozen_status": 1,
            "card_type": 0,
            "open_card_fee": null,
            "card_fee_order_no": "",
            "list_individual_asset": null,
            "list_credit_asset": null,
            "register_source": null,
            "if_attachments": false,
            "paid_open_card_fee": null,
            "user_id": null
        }
    ]
}


```

| Parameter               | Type    | Description                                           |
|-------------------------|---------|-------------------------------------------------------|
| acct_id                 | int     | 账户的唯一标识符                                     |
| acct_no                 | string  | 账户编号                                             |
| acct_name               | string  | 账户名称                                             |
| first_name              | string  | 名字                                                 |
| last_name               | string  | 姓氏                                                 |
| gender                  | string  | 性别，例如 male 或 female                            |
| birthday                | long    | 生日的时间戳                                       |
| city                    | string  | 城市                                                 |
| state                   | string  | 州名                                                 |
| country                 | string  | 国家代码                                             |
| doc_no                  | string  | 证件号码                                             |
| doc_type                | string  | 证件类型，例如 passport                              |
| front_doc               | string  | 证件正面图像的链接                                   |
| back_doc                | null    | 证件背面图像的链接，当前为 null                     |
| mix_doc                 | string  | 混合文档的链接                                       |
| country_code            | string  | 国家代码，例如 86                                     |
| mobile                  | string  | 手机号码                                             |
| mail                    | string  | 电子邮件地址                                         |
| address                 | string  | 地址                                                 |
| status                  | int     | 状态，例如 50                                       |
| auditor                 | string  | 审核人电子邮件                                       |
| reason                  | null    | 原因，当前为 null                                    |
| kyc_info                | null    | KYC 信息，当前为 null                               |
| create_time             | long    | 创建时间的时间戳                                   |
| update_time             | long    | 最后更新时间的时间戳                               |
| card_no                 | null    | 卡号，当前为 null                                    |
| card_number             | null    | 卡号，当前为 null                                    |
| nationality             | string  | 国籍，例如 CN                                       |
| zipcode                 | string  | 邮政编码                                             |
| maiden_name             | string  | 婚前姓氏                                             |
| bank_id                 | null    | 银行 ID，当前为 null                                 |
| card_type_id            | string  | 卡片类型 ID                                         |
| frozen_status           | int     | 冻结状态，1 表示被冻结                                |
| card_type               | int     | 卡片类型，0 可能表示某种特定类型                    |
| if_attachments          | bool    | 是否有附件，false 表示没有附件                       |
| user_id                 | null    | 用户 ID，当前为 null                                 |
















### 提交卡申请kyc第一步

- Request:

```text
url：/v1/account/kyc/card

POST
```

|    Parameter    |   Type   | Whether Required |                        Description                         |
| :-------------: | :------: | :--------------: | :-------------------------------------------------------- |
|   first_name    |  string  |      Required    | 名字                                                    |
|    last_name    |  string  |      Required    | 姓氏                                                    |
|       city      |  string  |      Required    | 城市                                                    |
|      state      |  string  |      Required    | 州名                                                    |
|     country     |  string  |      Required    | 国家代码                                                |
|     address     |  string  |      Required    | 地址                                                    |
|     zipcode     |  string  |      Required    | 邮政编码                                                |
|      gender     |  string  |      Required    | 性别，例如 male 或 female                               |
|      phone      |  string  |      Required    | 电话号码                                                |
|  country_code   |  string  |      Required    | 国家代码，例如 86                                       |
|      mobile     |  string  |      Required    | 手机号码                                                |
|    doc_type     |  string  |      Required    | 证件类型，例如 passport                                 |
|       mail      |  string  |      Required    | 电子邮件地址                                           |
|      status     |   int    |      Required    | 状态，例如 51                                          |
|  card_type_id   |  string  |      Required    | 卡种 ID                                                |

- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": 157
}


```

| Parameter               | Type    | Description                                           |
|-------------------------|---------|-------------------------------------------------------|
| result                 | int     | 返回了kyc的id                                 |






### 提交卡申请kyc第二步

- Request:

```text
url：/v1/account/kyc/card

POST
```
|    Parameter    |   Type   | Whether Required |                        Description                         |
| :-------------: | :------: | :--------------: | :-------------------------------------------------------- |
|     acct_id     |   int    |      Required    | 账户的唯一标识符                                         |
|     acct_no     |  string  |      Required    | 账户编号                                               |
|    acct_name    |  string  |      Required    | 账户名称                                               |
|   first_name    |  string  |      Required    | 名字                                                  |
|    last_name    |  string  |      Required    | 姓氏                                                  |
|      gender     |  string  |      Required    | 性别，例如 male 或 female                             |
|    birthday      |  string  |      Required    | 生日的 ISO 8601 格式字符串                             |
|      city       |  string  |      Required    | 城市                                                  |
|      state      |  string  |      Required    | 州名                                                  |
|     country     |  string  |      Required    | 国家代码                                              |
|      doc_no     |  string  |      Required    | 证件号码                                              |
|    doc_type     |  string  |      Required    | 证件类型，例如 passport                               |
|    front_doc    |  string  |      Required    | 证件正面图像的链接                                    |
|     back_doc    |  null    |      Optional    | 证件背面图像的链接，当前为 null                       |
|     mix_doc     |  string  |      Required    | 混合文档的链接                                        |
|  country_code   |  string  |      Required    | 国家代码，例如 86                                     |
|      mobile     |  string  |      Required    | 手机号码                                              |
|      mail       |  string  |      Required    | 电子邮件地址                                         |
|     address      |  string  |      Required    | 地址                                                  |
|      status     |   int    |      Required    | 状态，例如 52                                        |
|     auditor      |  string  |      Required    | 审核人电子邮件                                       |
|      reason     |  null    |      Optional    | 原因，当前为 null                                    |
|     kyc_info    |  null    |      Optional    | KYC 信息，当前为 null                                 |
|   create_time    |   long   |      Required    | 创建时间的时间戳                                     |
|   update_time    |   long   |      Required    | 最后更新时间的时间戳                                 |
|      card_no    |  null    |      Optional    | 卡号，当前为 null                                     |
|   card_number    |  null    |      Optional    | 卡号，当前为 null                                     |
|   nationality    |  string  |      Required    | 国籍，例如 CN                                       |
|     zipcode      |  string  |      Required    | 邮政编码                                              |
|   maiden_name    |  string  |      Required    | 婚前姓氏                                             |
|      bank_id    |  null    |      Optional    | 银行 ID，当前为 null                                  |
|   card_type_id   |  string  |      Required    | 卡种 ID                                              |
|   frozen_status  |   int    |      Required    | 冻结状态，1 表示被冻结                                |
|     card_type    |   int    |      Required    | 卡片类型，0 可能表示某种特定类型                    |
|  if_attachments  |  bool    |      Required    | 是否有附件，false 表示没有附件                       |
|      user_id     |  null    |      Optional    | 用户 ID，当前为 null                                  |

- Response:

```
{
    "code": 0,
    "msg": "SUCCESS",
    "result": 157
}


```

| Parameter               | Type    | Description                                           |
|-------------------------|---------|-------------------------------------------------------|
| result                 | int     | 返回了kyc的id                                 |








### 支付开卡费

- Request:

```text
url：/v1/account/card/deposit/open-card-fee

POST
```
|    Parameter    |   Type   | Whether Required |                        Description                         |
| :-------------: | :------: | :--------------: | :-------------------------------------------------------- |
|     card_type_id     |   string    |      Required    | 卡种ID                                        |
 

- Response:

```
{
    "code": 0,
    "msg": "SUCCESS"
}


```

| Parameter               | Type    | Description                                           |
|-------------------------|---------|-------------------------------------------------------|
| code                 | int     |code=0为成功，每个用户每个卡种只能申请一张                          |




