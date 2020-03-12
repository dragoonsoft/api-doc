# DRAGOON SOFT API說明文件（繁體中文)-單一錢包v1.7.4

[TOC]
## 前言
### 文件常規

本指南使用了幾種常規，以強調特定文字與詞組，並著重於特定的資訊。

#### 註解和警告
我們將使用三種視覺上的形式，來強調可能會被遺漏掉的資訊。

:::success
<i class="fa fa-comment fa-fw"></i>***注意:***
> 註解代表某些作業上的提示、捷徑或是其它完成方式。忽略註解並不會帶來太大的負面影響，不過您可能會忽略掉某些能夠較輕鬆完成工作的方式。
:::

:::warning
<i class="fa fa-star fa-fw"></i>***重點:***
>重點方塊會將容易遺漏掉的項目詳細列出。倘若您忽略掉重點方塊，雖然不會造成資料遺失，不過卻會造成工作上的不便與其它影響。
:::

:::danger
<i class="fa fa-exclamation-triangle fa-fw"></i>***警告:***
>任何警告都不該被忽略掉，忽略警告則很有可能會造成介接失敗。
:::

## 注意事項
1. 代理商編號、Game API位址、Game API公開金鑰資訊，請洽Dragoon Soft窗口索取。
2. 實行介接前，請先提供貴司欲連線之IP。（回填於MIF文件內）
3. 介接傳遞內容均為 JSON 格式，編碼為 UTF-8。

## 請求簽章
#### API資料簽章方式

- 所有Game API請求都必須由運營商簽名。
- 所有Wallet API請求都必須由DS簽名。
- 所有Wallet API請求都必須由運營商實作與驗證。

#### 加密方式與流程
- 雙方都會使用 RSA-SHA256 進行簽章校驗。 
- 在介接之前，運營商生成RSA私鑰/公鑰，並將公鑰交由DS。(RSA KEY長度建議為1024、2048)
- DS生成RSA私鑰/公鑰，並將其公鑰發送給運營商。 
- 所有請求的HTTP Body將使用相應的私鑰與RSA-SHA256簽名並編碼為BASE64。 
- Body JSON排序不得更變
- 簽名 X-Ds-Signature 需放在請求標頭(Header)中。 
- 運營商需要使用DS提供的公鑰驗證所有Wallet API請求。 
- DS使用運營商提供的公鑰驗證所有API請求。
- 對於請求Body使用`私有金鑰` 作為密鑰進行`RSA-SHA256` 簽章並進行 Base64 編碼, 將結果作為請求標頭`X-Ds-Signature` 值。



**範例**

```x
POST /v1/member/create HTTP/1.1
Content-Type: application/json
X-Ds-Signature: byMYoU1abC/86EVvEbwNtumfgZnlbGaAwbeJ3qu668RCZRnkFv8B3fCRAzM9rDnDdjommrhK0B3BVmbpdCv6UzyY1avfxE2lbihvrF/LMkY4oKOyL8IJuGBbSLgzpI+hKYzcb+qD/En9ZAH0V4gNXh6RqI/XIgfWhB55zF9pBs4=

{
  "agent": "agent01",
  "account": "player001",
  "token": "1ceda4754e7e413592a6450fb224165f"
}
```
:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
> 傳遞內容須檢查是否為JSON格式。
:::
:::danger
<i class="fa fa-exclamation-triangle fa-fw"></i>***警告:***
> `X-Ds-Signature` 簽章錯誤會導致查詢失敗。
:::

## Game API-訊息代碼說明
### 回傳代碼說明
| code | 說明
| ---  | ---
|0   |  未知錯誤
|1   |  成功
|2   |  重複
|3   |  必填欄位
|4   |  登入失敗
|5   |  API存取失敗
|6   |  找不到資訊
|7   |  請求超時
|8   |  驗證碼錯誤
|9   |  用戶鎖定
|10   |  找不到玩家
|11   |  找不到代理
|12   |  数据类型错误
|1000|  簽章失敗
|1003|  驗證失敗
|1004|  資料庫存取失敗
|1005|  找不到錢包
|1006|  找不到交易
|1007|  處理失敗
|1012|  時間超出範圍

## Game API-可支援語系

| 參數 | 說明
| ---  | ---
| zh_cn    | 簡中
| en_us    | 英文
| vi_na | 越南文
| th_th | 泰文

:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
>在使用未受支援之區域語言的參數上，嘗試進行設定將會失敗。
:::

## Game API-可支援幣別

| 參數 | 說明
| ---  | ---
| CNY |人民幣
| EUR |歐元
| GBP |英鎊
| HKD |港幣
| IDR |印尼盾 (K)
| JPY |日元
| KRW |韓元 (K)
| MYR |馬來西亞令吉
| SGD |新加坡元
| THB |泰銖
| USD |美元
| VND |越南盾 (K)
| MMK |緬甸元 (K)

- `K` 符號為千元計算。

:::success
<i class="fa fa-comment fa-fw"></i>***注意:***
> 幣別是Dragoon Soft依照貴司於MIF文件中填寫的資訊協助設定完成，
> 不需於進行API傳輸時帶入此參數。
:::

## Game API-時間格式
- 格式： `RFC3339` 
- Example: `2018-10-18T05:47:51+05:00`

## Wallet API-錢包操作
營運商需實作錢包操作接口

### Wallet API 訊息代碼說明
回傳代碼說明
| status | 說明
| ---  | ---
|0|未知錯誤. 下注收到會接著執行取消流程. 派彩收到會進入排程, 等待下次派彩. 取消收到會進入排程, 等待下次取消.
|1|成功
|2|會員不存在.
|3|驗證失敗
|4|重複交易
|5|餘額不足
|6|交易編號不存在. 平台收到派彩及取消請求時, 若之前沒有收到同編號的下注單, 需回應此代碼

:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
> 尚未在預期狀態都等同未知錯誤
:::

---

### Wallet API-取餘額
取得使用者餘額

#### 請求網址 URL
```tiddlywiki
https://<url>/api/wallet/balance [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |
| token |String|是| 錢包訪問令牌 |


**範例**

```json=
{
  "agent": "agent01",
  "account": "player001",
  "token":"e278a9ea38da42dd9b84148c2f99a031"
}
```

#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
|balance|Number|使用者餘額 PS:支持至小數二位|
|status|Number|<a href="#Wallet-API-訊息代碼說明" title="Wallet-API-訊息代碼說明" smoothhashscroll="">錢包狀態碼</a>|

**範例**

```json=
{
  "balance": 99999.1,
  "status": 1
}
```
---

### Wallet API-下注請求
使用者下注时调用
运营商需将玩家余额按照下注金额减少，并返回新的使用者余额。

#### 請求網址 URL
```tiddlywiki
https://<url>/api/wallet/bet [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| trans_id |String|是| 交易識別號 PS: 全局唯一 |
| amount |Number|是| 交易金額 PS:支持至小數二位 |
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |
| token |String|是| 錢包訪問令牌 |
| game_id |String|是| 遊戲編號 |
:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
> 操作交易`amount` 金額皆為正數
:::
**範例**

```json=
{
  "trans_id": "9b2bb872-332a-4d8d-87aa-2e18dc4edff8",
  "amount": 8000,
  "agent": "agent01",
  "account": "player001",
  "token": "88406af3628342f19cea6042d0cb2267",
  "game_id": "1001"
}
```

#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
| trans_id |String| 交易識別號 PS:同請求之交易識別號|
|balance|Number|使用者餘額 PS:支持至小數二位|
|status|Number|<a href="#Wallet-API-訊息代碼說明" title="Wallet-API-訊息代碼說明" smoothhashscroll="">錢包狀態碼</a>|

**範例**

```json=
{
  "trans_id": "9b2bb872-332a-4d8d-87aa-2e18dc4edff8",
  "balance": 4000,
  "status": 1
}
```
:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
> 下注時若未收到狀態碼為`1`，將立即發送取消請求。
:::

---

### Wallet API-派彩請求
当使用者派彩时调用。 
预计运营商将按金额增加使用者余额并返回新余额。 

在更改用户余额之前，运营商必须检查是否有重複派彩或者已經取消下注。

#### 請求網址 URL
```tiddlywiki
https://<url>/api/wallet/payout [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| trans_id |String|是| 交易識別號 同下注交易識別 |
| amount |Number|是| 交易金額 PS:支持至小數二位 |
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |
| game_id |String|是| 遊戲編號 |

:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
> 操作交易`amount` 金額皆為正數
:::

:::success
<i class="fa fa-comment fa-fw"></i>***注意:***
> 重试策略：
如果請求時网络出现故障，我们會将請求重试。 
並將請求继续重試，并且將金額返回给使用者。
每次嘗試重試將會遞延間隔時間。
:::

**範例**

```json=
{
  "trans_id": "9b2bb872-332a-4d8d-87aa-2e18dc4edff8",
  "amount": 8000,
  "agent": "agent01",
  "account": "player001",
  "game_id": "1001"
}
```

#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
| trans_id |String| 交易識別號 PS:同請求之交易識別號|
|balance|Number|使用者餘額 PS:支持至小數二位|
|status|Number| <a href="#Wallet-API-訊息代碼說明" title="Wallet-API-訊息代碼說明" smoothhashscroll="">錢包狀態碼</a>|

**範例**

```json=
{
  "trans_id": "9b2bb872-332a-4d8d-87aa-2e18dc4edff8",
  "balance": 4000,
  "status": 1
}
```
:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
> 下注時若未收到狀態碼為`1`，將立即發送取消請求。
:::

---

### Wallet API-取消請求
下注時若未收到狀態碼為`1`，將立即發送取消請求。

#### 請求網址 URL
```tiddlywiki
https://<url>/api/wallet/cancel [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| trans_id |String|是| 交易識別號 同下注交易識別 |
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |
| game_id |String|是| 遊戲編號 |

:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
> 取消操作交易將返還下注金額
:::

:::success
<i class="fa fa-comment fa-fw"></i>***注意:***
> 重试策略：
如果請求時网络出现故障，我们會将請求重试。 
並將請求继续重試，并且將金額返回给使用者。
每次嘗試重試將會遞延間隔時間。
:::

**範例**

```json=
{
  "trans_id": "9b2bb872-332a-4d8d-87aa-2e18dc4edff8",
  "amount": 8000,
  "agent": "agent01",
  "account": "player001",
  "game_id": "1001"
}
```

#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
| trans_id |String| 交易識別號 PS:同請求之交易識別號|
|balance|Number|使用者餘額 PS:支持至小數二位|
|status|Number| <a href="#Wallet-API-訊息代碼說明" title="Wallet-API-訊息代碼說明" smoothhashscroll="">錢包狀態碼</a>|

**範例**

```json=
{
  "trans_id": "9b2bb872-332a-4d8d-87aa-2e18dc4edff8",
  "balance": 4000,
  "status": 1
}
```
## Game API-使用者
### 建立使用者

由API建立使用者

#### 請求網址 URL
```tiddlywiki
https://<url>/v1/member/create [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
|channel|String|是|渠道碼(MIF表單內提供)|
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |


**範例**

```json=
{
  "channel": "53346874",
  "agent": "agent01",
  "account": "player001"
}
```

#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
|code|Number|<a href="#Game-API-訊息代碼說明" title="Wallet-API-訊息代碼說明" smoothhashscroll="">狀態代碼</a>|
|message|String|狀態訊息|

**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "success"
  }
}
```
:::warning
<i class="fa fa-star fa-fw"></i>***重點:***
>會員帳號長度限制為4~32字元，不需前綴。
>字元限制 0~9 , a~z ,A~Z ,- ,_
:::


### Game API-使用者登入遊戲
取得玩家啟動遊戲連結

#### 請求網址 URL
```tiddlywiki
https://<url>/v1/member/login_game [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
|channel|String|是|渠道碼(MIF表單內提供)|
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |
|token|String|是|錢包操作令牌|
| game_id |String|是|  遊戲編號 |
| group_id |String|否| 分組編號 |
| lang |String|是| 由可支援語系提供 |
| oper |String|否| 額外選填參數 |
| backurl |String|否| 離開或結束遊戲 轉跳至該網址 |
| is_demo |Boolean|否| 是否試玩 |
| btn |String|否| 請參考下表 不帶入時預設為顯示|

#### btn 參數對照表
|離開按鈕|注單按鈕|帶入參數|
|---|---|---|
|隱藏|顯示|0,1|
|顯示|顯示|1,1|
|顯示|隱藏|1,0|
|隱藏|隱藏|0,0|


#### 回傳參數 Response Parameters
|參數名稱|型別|說明|
|---|---|---|
|code|Number|<a href="#Game-API-訊息代碼說明" title="Game-API-訊息代碼說明" smoothhashscroll="">狀態代碼</a>|
|message|String|狀態訊息|
|url|String|玩家使用的遊戲連結|

**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "success"
  },
  "url": "https://game_server_url/h5/1001/?token=d01e14944dcd4a0e9e207f4438bb37da&lang=zh_cn&oper=ds&backurl=https://backurl.com"
}
```

### 使用者登出
由API執行使用者登出方法
#### 請求網址 URL
```tiddlywiki
https://<url>/v1/member/logout [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
|channel|String|是|渠道碼(MIF表單內提供)|
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |

**範例**

```json=
{
  "channel": "53346874",
  "agent": "AG000001",
  "account": "Username002"
}
```
#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
|code|Number|狀態代碼|
|message|String|狀態訊息|

**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "success"
  }
}
```

## Game API-下注紀錄

### 定義結構參考 Structure 

#### Record 定義結構
|參數名稱|型別|說明|
|---|---|---|
|id|String|下注編號|
|bet_at|Number|下注時間|
|finish_at|Number|結算時間|
|agent|String|代理帳號|
|member|String|玩家帳號|
|game_id|String|遊戲編號|
|game_serial|String|遊戲流水號
|game_type|Number|遊戲類型|
|round_id|Number|回合ID|
|bet_amount|Number|下注金額|
|payout_amount|Number|游戏赢分(未扣除手续费)|
|valid_amount|Number|有效金額|
|status|Number|下注狀態|
|fee_amount|Number|手續費|
|jp_amount|Number|彩金金額|

#### Record Status 下注狀態
|代碼|說明|
|---|---|
|1|正常|
|2|退款|
|3|拒絕投注|
|4|注單作廢|
|5|取消|

#### Time 定義結構
|參數名稱|型別|說明|
|---|---|---|
|start_time|Number|開始日期 ex: 2018-10-19T08:54:14+01:00|
|end_time|Number|結束日期 ex: 2018-10-20T11:47:08+01:00|

### 取得玩家下注紀錄
查詢代理商所屬玩家，時間區間內的下注紀錄。
#### 請求網址 URL
```tiddlywiki
https://<url>/v1/record/get_bet_records [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
|channel|String|是|渠道碼(MIF表單內提供)|
|finish_time|Time|否|依照結算時間查訊注單 |
|index|Number|否|開始頁碼 默認: 0 |
|limit|Number|否|查詢上限 默認: 1000 最大: 5000|

**範例**

```json=
{
  "channel": "53346874",
  "finish_time": {
    "start_time": "2018-10-19T08:54:14+01:00",
    "end_time": "2018-10-20T11:47:08+01:00"
  },
  "index": 0,
  "limit": 10
}
```

#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
|total|Number|總筆數|
|rows|Record[ ]|下注紀錄陣列|

**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "success",
    "timestamp": "2018-11-12T04:03:53.174604283Z"
  },
  "total": "8870",
  "rows": [
    {
      "id": "1",
      "bet_at": "2018-10-18T05:47:51Z",
      "finish_at": "2018-10-18T05:47:51Z",
      "agent": "AG0000001",
      "member": "MB03",
      "game_id": "1001",
      "game_serial": "4604283",
      "game_type": 1,
      "round_id": "1",
      "bet_amount": "1200",
      "payout_amount": "0",
      "valid_amount": "1200",
      "status": 1 ,
      "fee_amount": "10" ,
      "jp_amount": "10" 
    },
    {
      "id": "2",
      "bet_at": "2018-10-18T05:47:51Z",
      "finish_at": "2018-10-18T05:47:51Z",
      "agent": "AG0000001",
      "member": "MB03",
      "game_id": "1001",
      "game_serial": "38427123",
      "game_type": 1,
      "round_id": "1",
      "bet_amount": "3600",
      "payout_amount": "0",
      "valid_amount": "3600",
      "status": 1 ,
      "fee_amount": "10" ,
      "jp_amount": "10" 
    }
  ]
}
```



### 取得玩家詳細下注紀錄頁面連結
查詢代理商所屬玩家的詳細下注紀錄之連結。
#### 請求網址 URL
```tiddlywiki
https://<url>/v1/record/get_bet_detail_page [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
|channel|String|是|渠道碼(MIF表單內提供)|
|game_id|String|是|遊戲編號 |
|game_serial|String|是|遊戲流水號 |
|round_id|String|是|遊戲回合id |
| lang |String|是| 現支援`zh_cn`及`en_us` |

**範例**

```json=
{
  "channel": "53346874",
  "game_id": "2001",
  "game_serial": "23134242",
  "round_id": "87122421352",
  "lang": "zh_cn"
}
```

#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
|code|Number|狀態代碼|
|message|String|狀態訊息|
|url|String|詳細注單位置|

```json=
{
  "result": {
    "code": 1,
    "msg": "success"
  },
  "url": "https://<record.page>/bet_detail/?token=3fd0e97e-198b-4d34-9ab0-cd3f82f79ba3&lang=zh_cn"
}
```

:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
>詳細下注記錄連結時效為10分鐘，逾時將會失效。
:::


### 取得玩家歷史下注紀錄頁面連結
查詢玩家的歷史下注紀錄之連結。
#### 請求網址 URL
```tiddlywiki
https://<url>/v1/member/bet_history [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
|channel|String|是|渠道碼(MIF表單內提供)|
| agent |String|是| 代理商帳號 |
| account |String|是| 使用者帳號 |
| game_id|String|是|遊戲編號 |
| lang |String|是| 由可支援語系提供 |

**範例**

```json=
{
  "channel": "53346874",
  "agent": "AG0000001",
  "account": "MB03" ,
  "game_id": "2001",
  "lang": "zh_cn"
}
```

#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
|code|Number|狀態代碼|
|message|String|狀態訊息|
|url|String|詳細注單位置|

```json=
{
  "result": {
    "code": 1,
    "msg": "success"
  },
  "url": "https://<record.page>/auth/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0IjoiODc3MjZjOTg3M2NlNGRmNWI4YTM4MTdiZTc2YjU5NTQifQ.qzLt51idNxxsZV4nXfO0wui-moVDeRdolhlNNdZp7go?g=1&gid=1001&lang=en_us"
}
```

:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
>詳細下注記錄連結時效為5分鐘，逾時將會失效。
:::

# 修訂版本
> ###### `N` 新增、 `U` 更新、`D` 刪除
#### v1.7.4
##### 2020/3/12
`U` 更新參數**使用者登入遊戲** 是否隱藏離開、注單按鈕 btn

#### v1.7.3
##### 2020/3/03
`N` 新增**取得玩家歷史下注紀錄頁面連結**
`U` 更新參數**使用者登入遊戲** 是否試玩 is_demo

#### v1.7.2
##### 2019/10/25
`N` 新增 **使用者登出遊戲**

#### v1.7.0
##### 2019/08/25
`N` 新增**使用者登入遊戲**取得玩家啟動遊戲連結
`D` 刪除**進入遊戲**玩家跳轉啟動遊戲
`D` 刪除**使用者登入**由API執行使用者登入方法

#### v1.6.0
##### 2019/05/01
`U` 進入遊戲-更新離開遊戲之跳轉欄位
`D`刪除**遊戲列表**

#### v1.5.1
##### 2019/04/23
`D` 刪除**取得玩家詳細下注紀錄頁面連結** 參數名稱 ~~bet_id~~
`U` 新增**取得玩家詳細下注紀錄頁面連結** 參數名稱 game_serial 

#### v1.5.0
##### 2019/04/18
`N` 新增**取得玩家詳細下注紀錄頁面**連結方法

#### v1.4.1
##### 2019/03/27
`U` 建立使用者-更新會員帳號長度由8-16變更為**6-16**字元，並支持底線_、負號-
`U` 進入遊戲-新增廠商判別(選填)
#### v1.4
##### 2019/03/20
`U` 遊戲列表-更新**2019/03/20上線之9款遊戲及1遊戲大廳**
#### v1.3.1 
##### 2019/03/12
`U` 下注紀錄定義-更新**payout_amount**說明



