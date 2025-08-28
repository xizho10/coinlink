# Coinlink Open API 文档

- [Coinlink API](#coinlink-api)
- [接口规范](#接口规范)
- [1.用户接口](#1用户接口)
  - [1.1 注册接口](#11-注册接口)
  - [1.2 登录接口](#12-登录接口)
  - [1.3 查询用户钱包信息](#13-查询用户钱包信息)
  - [1.4 链上 Hash 充值](#14-链上-hash-充值)

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




