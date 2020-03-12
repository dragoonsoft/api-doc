# DRAGOON SOFT API說明文件（繁體中文)-轉帳錢包v1.3.1

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
1. 代理商編號、API位址、API加密金鑰資訊，請洽Dragoon Soft窗口索取。
2. 實行介接前，請先提供貴司欲連線之IP。（回填於MIF文件內）
3. 介接傳遞內容均為 JSON 格式，編碼為 UTF-8。

## 遊戲列表
:exclamation: 因應遊戲數目增加，統一移動至DRAGOON SOFT-遊戲列表文件，請洽商務或客服索取:exclamation: 

## 訊息代碼說明
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
|1001|  金額錯誤
|1002|  交易序號重複
|1003|  驗證失敗
|1004|  資料庫存取失敗
|1005|  找不到錢包
|1006|  找不到交易
|1007|  處理失敗
|1008|  交易金額不得為負數
|1009|  找不到代理配額
|1010|  代理配額不足
|1011|  代理提款鎖定
|1012|  時間超出範圍
|1013|  超過小數點兩位
|1028|  遊戲未結算

## 可支援語系

| 參數 | 說明
| ---  | ---
| zh_cn    | 簡中
| en_us    | 英文
| vi_vn | 越南文
| th_th | 泰文

:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
>在使用未受支援之區域語言的參數上，嘗試進行設定將會失敗。
:::

## 可支援幣別

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

## 時間格式
- 格式： `RFC3339` 
- Example: `2018-10-18T05:47:51+05:00`

## API加密方式
API資料加密方式

### 加密金鑰資訊
DS平台会提供给 API 接入合作方渠道碼, AES金鑰 和 MD5金鑰。

| Key     | 說明
| ---     | ---
| channel | 渠道碼
| AES_key  | AES 加密 
| sign_key | MD5 簽名

:::danger
<i class="fa fa-exclamation-triangle fa-fw"></i>***警告:***
>DS在任何情況下都不會請合作方提供加密金鑰的資訊,請將這些信息妥善保管。
:::

### 加密金鑰協定
Cipher: AES/256/CBC/PKCS5Padding

### 加密方式與流程

1.  對於請求參數使用`AES_key` 作為密鑰進行`AES`加密並進行 Base64 編碼, 將結果作為請求參數`data`值。
2. 將`data`拼接上`sign_key`後進行MD5簽章,作為請求參數 `sign`的值。
3. 將 DS 提供的渠道碼作為請求參數 `channel` 的值。
4. 提交POST。

#### 請求定義結構
|參數名稱|型別|說明|
|---|---|---|
|channel|String|渠道碼|
|sign|String|參數簽章|
|data|String|加密後的請求參數|

**範例**

```json=
POST /v1/member/create HTTP/1.1
Content-Type: application/json

{
  "channel": "7878778",
  "data": "U2FsdGVkX18agQleI7fP+eJ1l2WmCNZMRajMNRrlkydQRjbHm0LlIBLC1oxdLPWtJgXqcRyg/gEgKDJgpM7sEg==",
  "sign": "75ad8c8d3fc993885f7f8f9470e70227"
}
```
:::warning
<i class="fa fa-star fa-fw"></i>***重要:***
> 1. 傳遞內容須檢查是否為JSON格式。
> 2. MD5 為 32 位 ***小寫*** 字母或數字
:::
:::danger
<i class="fa fa-exclamation-triangle fa-fw"></i>***警告:***
>簽章錯誤會導致查詢失敗。
:::
### 加密程序演示

GO
```go=
package main

import (
	"crypto/md5"
	"encoding/hex"
	"fmt"
	"github.com/mervick/aes-everywhere/go/aes256"
)

// 需要加密的本文
const encryptText = `{"agent":"develop01","account":"mbs000120","random":"123"}`

// 加密金鑰
const AESKey = "1ac5d0205ab0e586ae738c65dc82ceb48318b0cd52a3b27a14f891e3706f2a1c"

// 簽名 sign_key
const signKey = "ZFdeUewhnjSxC2ImsrmLFnOdHlbzQ7a4CQ9oWAF/bNY="

func main() {
	// encryption
	encrypted := aes256.Encrypt(encryptText, AESKey)
	fmt.Println("encrypted:\t" , encrypted)

	// toSign
	toSign := fmt.Sprintf("%s%s", encrypted, signKey)
	fmt.Println("to_sign:\t" ,toSign)

	// sign
	sign := sign(toSign)
	fmt.Println("sign:\t" ,sign)

	// decryption
	decrypted := aes256.Decrypt(encrypted, AESKey)
	fmt.Println("decrypted:\t",decrypted)
}

func sign(toSign string) string {
	h := md5.New()
	h.Write([]byte(toSign))
	cipherStr := h.Sum(nil)
	return hex.EncodeToString(cipherStr)
}

```

Output:
```
encrypted:      U2FsdGVkX1+fF0DPIcrCPtvN5tL3p9AyUfs1oU2vXxMjkhAhgx4W0cceD19BSWRxS7PVlBpgCBXYLgaHKx4dtAObDEMCUTaXA7HqUu9Eyq8=
to_sign:        U2FsdGVkX1+fF0DPIcrCPtvN5tL3p9AyUfs1oU2vXxMjkhAhgx4W0cceD19BSWRxS7PVlBpgCBXYLgaHKx4dtAObDEMCUTaXA7HqUu9Eyq8=ZFdeUewhnjSxC2ImsrmLFnOdHlbzQ7a4CQ9oWAF/bNY=
sign:           63728cc345b3c8a548478e747999ea0c
decrypted:      {"agent":"develop01","account":"mbs000120","random":"123"}
```

Python
```python=
# -*- coding: utf-8 -*-
from aes256.aes256 import aes256
import hashlib

# 需要加密的本文
encryptText = '{"agent":"develop01","account":"mbs000120","random":"123"}'

# 加密金鑰
AESKey = "1ac5d0205ab0e586ae738c65dc82ceb48318b0cd52a3b27a14f891e3706f2a1c"

# 簽名 sign_key
signKey = "ZFdeUewhnjSxC2ImsrmLFnOdHlbzQ7a4CQ9oWAF/bNY="

def sign(toSign):
    m = hashlib.md5()
    m.update(toSign)
    h = m.hexdigest()
    return h

# encryption
encrypted = aes256().encrypt(encryptText, AESKey)
print(encrypted)

# toSign
toSign = encrypted + signKey
print(toSign)

# sign
sign = sign(toSign)
print(sign)

# decryption
decrypted = aes256().decrypt(encrypted, AESKey)
print(decrypted)
```
Output:
```
encrypted:      U2FsdGVkX1+L8EzpL6knCIsCnzKeGR1neQuo44TWS4Q7ID7pVYYsqrAxN8xxHYVOiiUPYC+d9rGC5EikE6mdnYI60LXgTT3qQgm817Fm6eU=
to_sign:        U2FsdGVkX1+L8EzpL6knCIsCnzKeGR1neQuo44TWS4Q7ID7pVYYsqrAxN8xxHYVOiiUPYC+d9rGC5EikE6mdnYI60LXgTT3qQgm817Fm6eU=gYVa2GgdDYbR6R4AFnk5y2aU0sQirNIIoAcpOUh_aZk=
sign:           460b201634c2a7e923ccc8792527cd1d
decrypted:      {"agent":"develop01","account":"mbs000120","random":"123"}
```

PHP
```php=
<?php

// 需要加密的本文
$encryptText = '{"agent":"develop01","account":"mbs000120","random":"123"}';

// 加密金鑰
define("AES_KEY", "1ac5d0205ab0e586ae738c65dc82ceb48318b0cd52a3b27a14f891e3706f2a1c");

// 簽名 sign_key
define("SIGN_KEY", "ZFdeUewhnjSxC2ImsrmLFnOdHlbzQ7a4CQ9oWAF/bNY=");

class AES256
{
    /**
     * Encrypt string
     *
     * @param string|numeric $text
     * @param string $passphrase
     * @return string
     * @throws \Exception
     */
    public static function encrypt($text, $passphrase)
    {
        $salt = openssl_random_pseudo_bytes(8);

        $salted = $dx = '';
        while (strlen($salted) < 48) {
            $dx = md5($dx . $passphrase . $salt, true);
            $salted .= $dx;
        }

        $key = substr($salted, 0, 32);
        $iv = substr($salted, 32, 16);

        return base64_encode('Salted__' . $salt . openssl_encrypt($text . '', 'aes-256-cbc', $key, OPENSSL_RAW_DATA, $iv));
    }

    /**
     * Decrypt string
     *
     * @param string $encrypted
     * @param string $passphrase
     * @return string|numeric
     * @throws \Exception
     */
    public static function decrypt($encrypted, $passphrase)
    {
        $encrypted = base64_decode($encrypted);
        $salted = substr($encrypted, 0, 8) == 'Salted__';

        if (!$salted) {
            return null;
        }

        $salt = substr($encrypted, 8, 8);
        $encrypted = substr($encrypted, 16);

        $salted = $dx = '';
        while (strlen($salted) < 48) {
            $dx = md5($dx . $passphrase . $salt, true);
            $salted .= $dx;
        }

        $key = substr($salted, 0, 32);
        $iv = substr($salted, 32, 16);

        return openssl_decrypt($encrypted, 'aes-256-cbc', $key, true, $iv);
    }
}

$aes = new AES256();
$encrypted=$aes->encrypt($encryptText,AES_KEY);
printf("encrypted:\t" . $encrypted."\n");

$to_sign = $encrypted.SIGN_KEY;
printf("to_sign:\t" . $to_sign."\n");

$sign = md5($to_sign, FALSE);
printf("sign:\t\t" . $sign."\n");

printf("decrypted:\t" . $aes->decrypt($encrypted,AES_KEY)."\n");
```

Output:
```
encrypted:      U2FsdGVkX19pG16xXXCVXNI6fq3mvX5dedfdy5TU1+RTO6/z/76K87eJJsVwFL7jCFVh57Y8meNyhP0MgbcEh9NgY8ri48z9OciFHoCUm9A=
to_sign:        U2FsdGVkX19pG16xXXCVXNI6fq3mvX5dedfdy5TU1+RTO6/z/76K87eJJsVwFL7jCFVh57Y8meNyhP0MgbcEh9NgY8ri48z9OciFHoCUm9A=mnhLwO3Zh0M34d8Pr2CESM2b/jZtvAIvEvu0Af7aXrg=
sign:           c030eed1a822416427717955f31452be
decrypted:      {"agent":"develop01","account":"mbs000120","random":"123"}
```

更多程式語言範例 <a href="https://github.com/mervick/aes-everywhere" target="_self">➜ 请点选链结</a>

## 使用者
### 建立使用者

由API建立使用者方法

#### 請求網址 URL
```tiddlywiki
https://<url>/v1/member/create [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |
| password |String|否| 使用者密碼 |
| random |String|是| 隨機數 |



**範例**

```json=
{
  "agent": "DEMO00001",
  "account": "Username002",
  "password": "2788b26f9845bf9276b0aa45de7",
  "random": 1538231444
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
:::warning
<i class="fa fa-star fa-fw"></i>***重點:***
>會員帳號長度限制為4~32字元，不需前綴。
>字元限制 0~9 , a~z ,A~Z ,- ,_
:::


### 使用者 登入遊戲
取得玩家啟動遊戲連結

#### 請求網址 URL
```tiddlywiki
https://<url>/v1/member/login_game [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| game_id |String|是|  遊戲編號 |
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |
| group_id |String|否| 分組編號 |
| lang |String|是| 由可支援語系提供 |
| oper |String|否| 額外選填參數 |
| backurl |String|否| 離開或結束遊戲 轉跳至該網址 |
| is_demo |Boolean|否| 是否試玩 |
| btn |String|否| 是否隱藏特定按鈕  請參考下表|

#### btn 參數對照表
|離開按鈕|注單按鈕|帶入參數|
|---|---|---|
|隱藏|顯示|0,1|
|顯示|顯示|1,1|
|顯示|隱藏|1,0|
|隱藏|隱藏|0,0|
 不帶入時預設為顯示

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
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |

**範例**

```json=
{
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

### 使用者在線查詢
使用者在線資訊

#### 請求網址 URL
```tiddlywiki
https://<url>/v1/member/login_info [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| agent |String|是| 代理商編號 |
| account |String|是| 使用者帳號 |

**範例**

```json=
{
  "agent": "AG000001",
  "account": "Username002"
}
```
#### 回傳參數 Response Parameters
|參數名稱|型別|說明|
|---|---|---|
|code|Number|狀態代碼|
|message|String|狀態訊息|
|online|Number|1: 在線上 2:不在線上|

**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "SUCCESS"
  },
  "login_info": {
    "online": 2
  }
}
```

### 使用者在線清單
使用者在線清單


#### OnlineMember 定義結構
|參數名稱|型別|說明|
|---|---|---|
| agent |String| 代理商帳號 |
| account |String| 使用者帳號 |
|game_id|String|登入遊戲|
|login_time|String|登入時間 ex: 2019-05-08T12:40:52.036394384Z|



#### 請求網址 URL
```tiddlywiki
https://<url>/v1/member/online_list [POST]
```

#### 回傳參數 Response Parameters

|參數名稱|型別|說明|
|---|---|---|
|code|Number|狀態代碼|
|message|String|狀態訊息|
|list|OnlineMember[]|在線清單 |

**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "SUCCESS"
  },
  "list": [
    {
      "agent": "AG000001",
      "account": "Username002",
      "game_id": "1001",
      "login_time": "2019-05-08T12:40:52.036394384Z"
    },
    {
      "agent": "AG000001",
      "account": "Username001",
      "game_id": "2001",
      "login_time": "2019-05-08T12:28:25.086438394Z"
    },
    {
      "agent": "AG000002",
      "account": "Username055",
      "game_id": "3011",
      "login_time": "2019-05-08T12:41:42.026213394Z"
    }
  ]
}
```
:::warning
<i class="fa fa-star fa-fw"></i>***重點:***
>game_id若顯示為"0000"代表玩家正在登入中，尚未進入任何遊戲；若成功進入遊戲則會顯示遊戲編號。
:::

## 轉帳功能
### 轉帳
提供轉帳至遊戲錢包，廠商必須產生一組不重複『外部交易單號』進行轉帳。
#### 請求網址 URL
```tiddlywiki
https://<url>/v1/trans/transfer [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| serial|String|是|外部交易單號|
| agent |String|是| 代理商帳號 |
| account |String|是| 使用者帳號 |
| amount |String|是| 交易金額 |
| oper_type |Number|是| 交易種類( 存款 : 1 , 提款 :0) |

:::success
<i class="fa fa-comment fa-fw"></i>***注意:***
> serial欄位字串字元限制 0~9 , a~z ,A~Z , _ , - 
> 長度為8~36碼。
> 範例: 03ef6cc0-1800-11e9-aab1-f57beeec6bc0
:::

**範例**

```json=
{
  "serial": "1536140284868",
  "agent": "AG000001",
  "account": "Username002",
  "amount": "10000",
  "oper_type": 1
}

```
#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
|code|Number|狀態代碼|
|message|String|狀態訊息|
|timestamp|String|時間|
|trans_id|String|內部交易單號|
|serial|String|外部交易單號|
|balance|String|餘額|

**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "success",
    "timestamp": "2018-11-12T03:03:53.174604283Z"
  },
  "trans_id": "109668",
  "serial": "1536133443167",
  "balance": "582427.70"
}
```

### 查詢交易單狀態
使用第三方於轉帳時，所提供之交易單號，查詢該筆交易單狀態。
#### 請求網址 URL
```tiddlywiki
https://<url>/v1/trans/verify [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| agent |String|是| 代理商帳號 :new:|
| account |String|是| 使用者帳號 :new:|
| serial|String|是|外部交易單號|

**範例**

```json=
{
  "agent": "develop01c02",
  "account": "user0002",
  "serial": "1531839120967"
}
```
#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
| code |Number|狀態代碼|
| message |String|狀態訊息|
| timestamp |String|時間|
| serial |String|外部交易單號|
| trans_id |String|內部交易單號|
| amount |String|交易金額|


**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "success",
    "timestamp": "2018-11-12T03:03:53.174604283Z"
  },
  "serial": "1531839120967",
  "amount": "10.00",
}
```

### 查詢會員平台餘額
查詢會員當前平台餘額。
#### 請求網址 URL
```tiddlywiki
https://<url>/v1/trans/check_balance [POST]
```
#### 請求參數 Request Parameters
|參數名稱|型別|強制|說明|
|---|---|---|---|
| agent |String|是| 代理商帳號 |
| account |String|是| 會員帳號 |


**範例**

```json=
{
  "agent": "develop01c02",
  "account": "Username002"
}
```
#### 回傳參數 Response Parameters 
|參數名稱|型別|說明|
|---|---|---|
|code|Number|狀態代碼|
|message|String|狀態訊息|
|timestamp|String|時間|
|balance|String|會員平台餘額|

**範例**

```json=
{
  "result": {
    "code": 1,
    "msg": "success",
    "timestamp": "2018-11-12T03:03:53.174604283Z"
  },
  "balance": "449862.90"
}
```

## 下注紀錄

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
|game_serial|String|遊戲流水號|
|game_type|Number|遊戲類型|
|round_id|Number|遊戲回合id|
|bet_amount|Number|下注金額|
|payout_amount|Number|遊戲贏分(未扣除手續費)|
|valid_amount|Number|有效金額|
|status|Number|下注狀態|
|fee_amount|Number|手續費|
|jp_amount|Number|彩金金額|
|detail |String| 下注詳細 |

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
|finish_time|Time|否|依照結算時間查詢注單|
|index|Number|否|開始頁碼 默認: 0 |
|limit|Number|否|查詢上限 默認: 1000 最大: 5000|

:::danger
<i class="fa fa-exclamation-triangle fa-fw"></i>***警告:***
>查詢注單限制
>查詢範圍一小時為限，若超出範圍查詢則會查詢失敗。
:::

**範例**

```json=
{
  "finish_time": {
    "start_time": "2018-10-19T08:54:14+01:00",
    "end_time":   "2018-10-19T08:57:08+01:00"
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
      "game_serial": "324823745",
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
|game_id|String|是|遊戲編號 |
|game_serial|String|是|遊戲流水號 |
|round_id|String|是|遊戲回合id |
| lang |String|是| 由可支援語系提供 |

**範例**

```json=
{
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
| agent |String|是| 代理商帳號 |
| account |String|是| 使用者帳號 |
| game_id|String|是|遊戲編號 |
| lang |String|是| 由可支援語系提供 |

**範例**

```json=
{
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
#### v1.3.1
##### 2020/3/12
`U` 更新參數**使用者登入遊戲** 是否隱藏離開、注單按鈕 btn

#### v1.3.0
##### 2020/3/03
`N` 新增**取得玩家歷史下注紀錄頁面連結**
`U` 更新參數**使用者登入遊戲** 是否試玩 is_demo

#### v1.2.2
##### 2020/2/25
`D` 刪除**隨機數**

#### v1.2.1
##### 2019/12/23
`U` 更新參數**隨機數**型別為String

#### v1.2.0
##### 2019/08/25
`N` 新增**使用者登入遊戲**取得玩家啟動遊戲連結
`D` 刪除**進入遊戲**玩家跳轉啟動遊戲
`D` 刪除**使用者登入**由API執行使用者登入方法

#### v1.1.5
##### 2019/07/08
`U`新增**查詢交易單狀態**必須使用代理帳號與使用者帳號參數
`D`刪除**取得玩家下注紀錄** 棄用 time 依照下注時間查訊注單 

#### v1.1.4
##### 2019/06/19
`U`編修**使用者在線列表**使用者登入中提供所在判定為0000

#### v1.1.3
##### 2019/06/03
`N`新增**使用者登入**groupid分組功能
`D`刪除**遊戲列表**

#### v1.1.2
##### 2019/05/09
`N` 新增**使用者在線清單**查詢功能
`U` 遊戲列表-更新**2019/05/08上線之10款遊戲**

#### v1.1.1
##### 2019/04/23
`D` 刪除**取得玩家詳細下注紀錄頁面連結** 參數名稱 ~~bet_id~~
`U` 編修**取得玩家詳細下注紀錄頁面連結** 參數名稱 game_serial 

#### v1.1.0
##### 2019/04/18
`N` 新增**取得玩家詳細下注紀錄頁面**連結方法

#### v1.0.8
`N` 下注紀錄-新增**遊戲流水號**狀態欄位
#### v1.0.7
##### 2019/04/04
`N` 新增使用者在線查詢
#### v1.0.6
##### 2019/04/03
`U` 建立使用者-更新會員帳號長度由6-16變更為**4-32**字元
`U` 下注紀錄-更新**下注詳細**狀態欄位
`U` 進入遊戲-更新離開遊戲之跳轉欄位
#### v1.0.5
##### 2019/03/27
`U` 建立使用者-更新會員帳號長度由8-16變更為**6-16**字元，並支持底線_、負號-
`U` 進入遊戲-新增廠商判別(選填)
#### v1.0.4
##### 2019/03/20
`U` 遊戲列表-更新**2019/03/20上線之9款遊戲及1遊戲大廳**
#### v1.0.3 
##### 2019/03/12
`U` 下注紀錄定義-更新**payout_amount**說明