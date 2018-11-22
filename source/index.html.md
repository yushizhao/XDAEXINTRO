---
title: XDAEX Trading and Market Data API Specification ^XDAEX 交易及行情API规范

# language_tabs: # must be one of https://git.io/vQNgJ
#   - json: JSON 

search: false
---

# 1. Quick Start ^快速入门
The exchange provides two interface modes for API users:

* REST mode: Use the "request/reply" method to insert orders, cancel orders, query orders, query instruments, query assets, query trades, query market data, etc.
* WebSocket mode: Use the "publish/subscribe" method to publish order confirmation, execution reports, market data, etc.

交易所为 API 用户提供了两种接口模式：

* REST 模式：采用“请求/应答”方式，完成创建订单、撤销订单、查询订单、查询合约、查询资产、查询成交、查询行情信息等。
* WebSocket 模式：采用“发布/订阅”方式，推送订单确认、成交回报、行情信息等。

To make it easier to get started, here are the general steps for writing a trading program with the API:  (The details of each call can be found in subsequent chapters.)

为方便用户快速理解，以下列出了使用本 API 编写交易程序的常规步骤：（各个调用的详细内容可以查看后续对应的章节）

## 1.1. Preparation ^准备工作
### 1.1.1. API-KEY Management ^API-KEY 管理

API users need their own API-KEYs to complete the authentication. Details on how to apply for and re-generate the API-KEY are noted below:

* The API-KEY needs to be set via the "User Center--API Interface" of the exchange website by users. When appling for the API-KEY, users choose an authentication method between "public-private key pair method" and "message authentication code method", which are referred to as "PUB-PRIV" and "HMAC" in this specification, respectively. 
* When a user choose "PUB-PRIV", in addition to an API-KEY, a public-private key pair is generated. In that key pair, the private key is stored locally; Only the public key is uploaded to the server. Please ensure to keep the private key safe and backup properly. <b>(Do not disclose the private key as this is closely related to the security of your account.)</b>
* When a user choose "HMAC", in addition to an API-KEY, a secret key is generated. Both the user and the server keep a copy of the secret key. Please ensure to keep it safe and backup properly. <b>(Do not disclose the secret key as this is closely related to the security of your account.)</b>
* If the API user's private key or secret key is lost or stolen, the corresponding API-KEY can be removed in the "User Center--API Interface" of the exchange website by the API user. After that, a new API-KEY can be appiled for.

API 用户需要使用自己的 API-KEY 完成身份认证。 API-KEY 的申请和更换：

* API-KEY 需由 API 用户在交易所网站的“用户中心-- API 接口”中设置。在申请 API-KEY 时，用户从“公钥-私钥模式”和“消息验证码模式”中选择一种授权方式。在后文中，它们分别被称为 “ PUB-PRIV ” 和 “ HMAC ”。
* 当用户选择“ PUB-PRIV ”时，除 API-KEY 之外，会对应生成一对公私钥。其中的私钥在用户本地保存；只有公钥会被传送到服务器。请用户妥善保管和备份该私钥。<b>（该私钥与您的账户安全密切相关，请勿泄露！）</b>
* 当用户选择“ HMAC ”时，除 API-KEY 之外，会对应生成一个密钥。用户和交易所各持有一份该密钥的副本。<b>（该密钥与您的账户安全密切相关，请勿泄露！）</b>
* 若用户的私钥或密钥发生丢失、被盗用等异常情况，可在交易所网页的“用户中心-- API 接口”中删除对应的 API-KEY ，再申请新的 API-KEY 。

For security reasons, an API-KEY can be blacklisted by the administrator. If the API-KEY is blacklisted, the API user can send an email to support@xdaex.com and apply to be removed from the blacklist.

由于安全原因，某 API-KEY 可能被管理员加入黑名单。若 API-KEY 处于黑名单中， API 用户可发送邮件至 support@xdaex.com ，申请从黑名单中移除。

### 1.1.2. Query Info and Subscribe to WebSocket Flow ^查询信息和订阅 WebSocket 流
Before trading, it is recommended to call the following REST interface to query the server time, instruments, asset balances and other related information to ensure that the user's trading program is in the correct status:

* Get server time: Call the /marketData/time interface. The system time is in UTC (Coordinated Universal Time). The time difference between Hong Kong and UTC is +8, aka. UTC+8.
* Get instruments information: Call the /marketData/instrument interface to get the requirement for the decimal precision and the maximum value of the price and volume of the instrument.
* Get the user's asset balances: Call the /account/assets interface.
* Subscribe to WebSocket public and private message flow. (Ignore this step if not use WebSocket to receive published messages).

在开始交易前，建议调用以下 REST 接口，查询服务器时间、合约、用户资产余额等信息，以确保用户交易程序处于正确的状态：

* 获取服务器时间：调用 /marketData/time 接口。系统时间采用 UTC。香港时间与 UTC 的时差为 +8 ，也就是 UTC+8 。
* 获取合约信息：调用 /marketData/instrument 接口，得到对指定合约的价格和数量的小数精度、最大取值的要求。
* 获取用户资产余额：调用 /account/assets 接口。
* 订阅 WebSocket 公有流、私有流（如果不使用 WebSocket 接收推送的消息，则忽略本条）。

## 1.2. Trading ^交易
* Call the REST interface /order/insert to insert order, the user can specify the <font color="#dd0000" face = "black">orderLocalID</font> in the order. The orderLocalID is an order local ID assigned by the user rather than the exchange. It is recommended to ensure that the number is incremental and unique.

* Get the order confirmation and execution reports
    + If you are using WebSocket to receive messages, you will receive a message published through the WebSocket private flow:
        - order_return message: Indicates the order has been confirmed by the exchange. The message contains the <font color="#dd0000" face = "black">orderSysID</font> which is assigned to the order by the exchange.
        - order_insert_rsp message: Indicates that the order failed to be confirmed by the exchange.
    + If you are not using WebSocket to receive messages, call the REST interface /order/getorderbyLocalID by using orderLocalID to query the order confirmation. If the order has been confirmed by the exchange, the results will contain the <font color="#dd0000" face = "black">orderSysID</font> which is assigned to the order by the exchange. If the order failed to be confirmed by the exchange, the query result will be null.


* 调用 REST 接口 /order/insert 创建订单（以下简称：下单），用户可以在订单中自行指定 <font color="#dd0000" face = "black">orderLocalID</font> 。orderLocalID 是由用户（而非交易所）分配的本地订单ID。建议应确保 orderLocalID 的增长性和唯一性。
* 获取订单确认和成交回报：
    + 如果使用 WebSocket 接收消息，则会接收到通过 WebSocket 私有流推送的消息：
        - order_return 消息：表示订单已经被交易所确认。该消息中含有交易所为该订单分配的系统订单ID <font color="#dd0000" face = "black">orderSysID</font> 。
        - order_insert_rsp 消息：表示订单没能通过交易所的确认。
    + 如果不使用 WebSocket 接收消息，则需要调用 REST 接口 /order/getOrderByLocalID , 使用 orderLocalID 查询订单确认信息。如果订单已经被交易所确认，则查询结果中会含有交易所为该订单分配的系统订单ID <font color="#dd0000" face = "black">orderSysID</font> ；否则查询结果为 null 。

 * The order can be canceled by the following two methods:
    + Call the /order/cancelbyLocalID interface to cancel the order based on the orderLocalID assigned to the order by the user rather than the exchange.
    + Call the /order/cancel interface to cancel the order based on the orderSysID assigned to the order by the exchange.

* 若需撤销订单（以下简称：撤单），可采取以下两种方式：
    + 根据由用户（而非交易所）分配的本地订单ID orderLocalID 调用 /order/cancelByLocalID 接口撤销订单。
    + 根据交易所为该订单分配的系统订单ID orderSysID 调用 /order/cancel 接口撤销订单。

Note: For related information about API access management, rate limit and test/preview environment, please refer to the relevant contents in later sections. There are 'sample codes' in some programming languages for your reference at the end of this document.

注意：关于 API 接入管理、流量控制和测试环境等内容，请查阅后面的有关章节。在本文档的最后，有多种编程语言的“示例代码”供您参考。

## 1.3. Test Environment And Preview Environment<br \>&emsp;^测试环境和预览环境
In addition to the production environment, where users interact with the real market, the exchange provides other two environments:

* Test Environment: In order to protect users' assets, it is highly recommended that API users complete the necessary system tests in the test environment before accessing the production environment for trading. 
  - Domain name: api-test.xdaex.com
* Preview Environment: To help users prepare for API updates, conduct compatible tests for instance, all API updates take effect in the preview environment first. Then, after a preannounced period of time, the test environment and the production environment will be updated.
  - Domain name: api-preview.xdaex.com

在与真实市场交互的生产环境之外，交易所还为 API 用户们提供了两种环境：

* 测试环境：为了保护用户资产，建议 API 用户在测试环境中完成必要的系统测试后，再接入生产系统进行交易。
  - 域名：api-test.xdaex.com
* 预览环境：每次 API 功能迭代时会先对预览环境进行升级，以便于用户尽早开展兼容性测试等升级准备工作，并将在约定的一段时间后同步更新测试环境和生产环境。
  - 域名：api-preview.xdaex.com

API users can send an email to support@xdaex.com to apply for accessing the test environment and the preview environment.

API 用户可以发送邮件至 support@xdaex.com ，申请使用测试环境和预览环境。

# 2. REST Interface Description<br \>&emsp;^REST接口说明
## 2.1. HTTP Request URL<br \>&emsp;^HTTP 请求 URL
The REST interface requests a URL as: https://xdaex.com/APITrade/v1/account/assets
This request URL contains the following main components:

* Protocol: https
* Domain name: xdaex.com (To access the test\preview environment, please replace it with the corresponding domain name.)
* Interface name: APITrade
* Version: v1
* Path: Different functions need to be accessed via different paths. For example: query the user's asset balance via /account/assets.

REST 接口请求 URL 形如： https://xdaex.com/APITrade/v1/account/assets
该 URL 中主要包括以下组成部分：

* 协议: https
* 域名: xdaex.com （如访问测试或预览环境，则需替换成指定的域名）
* 接口名称: APITrade
* 版本: v1
* 路径: 使用不同功能要访问不同的路径，例如：查询用户资产余额对应的路径是 /account/assets

## 2.2. HTTP Request Message Header<br \>&emsp;^HTTP 请求消息头

- The HTTP request message header of the REST interface must contain the following:
- REST 接口 HTTP 请求消息头（Header）中必须包含以下内容：

| Properties <br />属性 | Description <br />描述                                       | Example <br />示例                 |
| --------------------- | ------------------------------------------------------------ | ---------------------------------- |
| API-KEY               | API access identification, as described in the 'API-KEY management' <br /> API 接入身份标识，详见“ API-KEY 管理”  | －－－                             |
| API-TIMESTAMP         | A timestamp at the moment of initiating the request (UTC) <br /> 请求发起时的时间戳（ UTC ） | "1420674445201"                    |
| API-SIGNATURE         | A digital signature or a message authentication code derived from the hash of the request content <br /> 基于请求内容的 Hash 值得出的数字签名或消息验证码 | Described as below <br /> 详见下面 |
| AUTH-TYPE             | The authorization method determined in the API-KEY application <br /> 申请当前 API-KEY 时所决定的授权方式 | "PUB-PRIV" \ "HMAC"                  |
| Content-Type          | Content-Type defined by MIME (Multipurpose Internet Mail Extentions) <br /> MIME （ Multipurpose Internet Mail Extentions ）中定义的 Content-Type | "application/json"                 |

- Steps to get an API-SIGNATURE with AUTH-TYPE "PUB-PRIV"
  - Combine data: data = timestamp + HttpMethod + version + path + jsonBody
    - timestamp is the timestamp（UTC）in the HTTP request message header.
    - HTTP Method can be GET or POST, all in uppercase.
    - version + path, such as: /v1/account/assets
      - The jsonBody is in JSON format. If the HTTP Method is GET, the jsonBody must be empty.
      - GET request example: data = "1539324192349GET/v1/account/assets"
      - POST request example: data = "1539324192349POST/v1/order/cancelByLocalID{"orderLocalID":"1234567891234"}"
  - Use SHA256 algorithm to get the hash of the data, then convert the hash into a hexadecimal string in lowercase: hashedData = SHA256(data) 
    - Example:
      - Input:
        - data = "1539324192349GET/v1/account/assets"
      - Output:
        - hashedData = "5ec9dc37816d865230b0e93bfd845a3c5fdef7dddf675b53e91d9259b6fa2a23"
  - Use the private key to sign the hash of the data (Algorithm: ECC, Elliptic Curve: secp256k1): sign (hashedData)
    - Example:
      - Input:
        - hashedData = "5ec9dc37816d865230b0e93bfd845a3c5fdef7dddf675b53e91d9259b6fa2a23"
        - privateKey = "VAoASo72TbEYsasQAD64nHlZVyBglPw13kfvlqM1j5Z="
      - Output:
        - signedData = {"r":"egMRmhmMNL9N94eKxj0xCDgh115u54cWPHNQEKt4nL4=", "s":"QlgLvEbzRqIBVEsusI7gbDnVtpqVxHWBQw2CdgBrpUU=","v":27}
- 获得 AUTH-TYPE 为 "PUB-PRIV" 的 API-SIGNATURE 的步骤
  - 拼装原文：data = timestamp + HttpMethod + version + path + jsonBody
    - timestamp 就是HTTP请求消息头中的时间戳（UTC）
    - HTTP Method 可以是 GET 或 POST，全部为大写格式
    - version + path， 如：/v1/account/assets
    - jsonBody 采用了 json 格式，如果是 GET 请求，则 jsonBody 为空
      - GET 请求范例：data = "1539324192349GET/v1/account/assets"
      - POST 请求范例：data = "1539324192349POST/v1/order/cancelByLocalID{"orderLocalID":"1234567891234"}"
  - 用 SHA256 算法对原文计算 hash ，得到小写16进制字符串： hashedData = SHA256(data)
    - 范例：
      - 输入：
        - data = "1234567891234GET/v1/account/assets"
      - 输出：
        - hashedData = "5ec9dc37816d865230b0e93bfd845a3c5fdef7dddf675b53e91d9259b6fa2a23"
  - 用私钥对 hash 结果签名（算法：ECC，椭圆曲线：secp256k1 ）: sign(hashedData)
    - 范例：
      - 输入：
        - hashedData = "5ec9dc37816d865230b0e93bfd845a3c5fdef7dddf675b53e91d9259b6fa2a23"
        - privateKey = "VAoASo72TbEYsasQAD64nHlZVyBglPw13kfvlqM1j5Z="
      - 输出：
        - signedData = {"r":"egMRmhmMNL9N94eKxj0xCDgh115u54cWPHNQEKt4nL4=", "s":"QlgLvEbzRqIBVEsusI7gbDnVtpqVxHWBQw2CdgBrpUU=","v":27}
- Steps to get an API-SIGNATURE with AUTH-TYPE "HMAC"
  - Combine data: data = timestamp + HttpMethod + version + path + jsonBody
    - timestamp is the timestamp (UTC) in the HTTP request message header.
    - HTTP Method can be GET or POST, all in uppercase.
    - version + path, such as: /v1/account/assets
      - The jsonBody is in JSON format. If the HTTP Method is GET, the jsonBody must be empty.
      - GET request example: data = "1539324192349GET/v1/account/assets"
      - POST request example: data = "1539324192349POST/v1/order/cancelByLocalID{"orderLocalID":"1234567891234"}"
  - Use SHA256 algorithm to get the hash of the data, then convert the hash into a hexadecimal string in lowercase: hashedData = SHA256(data) 
    - Example:
      - Input:
        - data = "1539324192349GET/v1/account/assets"
      - Output:
        - hashedData = "5ec9dc37816d865230b0e93bfd845a3c5fdef7dddf675b53e91d9259b6fa2a23"
  - Derive the message authentication code from the hash and the secret key (Algorithm: SHA256HMAC): SHA256HMAC (secretKey, hashedData)
    - Example:
      - Input:
        - hashedData = "5ec9dc37816d865230b0e93bfd845a3c5fdef7dddf675b53e91d9259b6fa2a23"
        - secretKey = "0adabfc46fa8062d92a4e8313ffce285efbb70dfcdb1e3d0c415dd17759a8303"
      - Output:
        - signedData = "Gkg0nwKpeQNw7h3hSiNGH1jem2y9M+vILdSDnG3ucSQ="
- 获得对应 AUTH-TYPE 为 "HMAC" 的 API-SIGNATURE 的步骤
  - 拼装原文：data = timestamp + HttpMethod + version + path + jsonBody
    - timestamp 就是HTTP请求消息头中的时间戳（UTC）
    - HTTP Method 可以是 GET 或 POST，全部为大写格式
    - version + path， 如：/v1/account/assets
    - jsonBody 采用了 json 格式，如果是 GET 请求，则 jsonBody 为空
      - GET 请求范例：data = "1539324192349GET/v1/account/assets"
      - POST 请求范例：data = "1539324192349POST/v1/order/cancelByLocalID{"orderLocalID":"1234567891234"}"
  - 用 SHA256 算法对原文计算 hash ，得到小写16进制字符串： hashedData = SHA256(data)
    - 范例：
      - 输入：
        - data = "1234567891234GET/v1/account/assets"
      - 输出：
        - hashedData = "5ec9dc37816d865230b0e93bfd845a3c5fdef7dddf675b53e91d9259b6fa2a23"
  - 用密钥结合原文的 hash 计算出消息验证码（算法： SHA256HMAC ）: SHA256HMAC(secretKey, hashedData)
    - 范例：
      - 输入：
        - hashedData = "5ec9dc37816d865230b0e93bfd845a3c5fdef7dddf675b53e91d9259b6fa2a23"
        - secretKey = "0adabfc46fa8062d92a4e8313ffce285efbb70dfcdb1e3d0c415dd17759a8303"
      - 输出：
        - signedData = "Gkg0nwKpeQNw7h3hSiNGH1jem2y9M+vILdSDnG3ucSQ="

## 2.3. Query Server Time<br \>&emsp;^查询服务器时间

> Response:
  
```json
{//example
    "timestamp": "1420674445201" //timestamp (UTC) ^时间戳（UTC）
}
```

Query the server time in UTC (Coordinated Universal Time).

查询服务器时间，采用 UTC 时间。

* Method: GET
* Version: v1
* Path: /info/time

## 2.4. Query API Release Number<br \>&emsp;^查询 API 发行号

> Response:
 
```json
{//example
    "version": "4.1.2"    //API release number ^API 发行号
}
```

Query the API release number that applies to the current environment.

查询适用于当前环境的 API 发行号。

* Method: GET
* Version: v1
* Path: /info/version

## 2.5. Query Instrument<br \>&emsp;^查询合约

> Response:
  
```json
[//example
    {
        "instrumentID": "ETH-BTC",    //Instrument ID ^合约ID
        "tradingAsset": "BTC",        //Trading asset ^交易资产
        "tradingTarget": "ETH",       //Trading target ^交易标的
        "volumePrecision": "3",       //The decimal points for volume ^数量的小数部分位数
        "pricePrecision": "8"         //The decimal points for price ^价格的小数部分位数
    }
]
```

Query instrument information. If instrumentID is not specified, then this returns all valid instrument information.

查询合约信息。若未指定 instrumentID ，则返回全部有效合约的信息。

* Method: GET
* Version: v1
* Path: /referenceData/instrument or
/referenceData/instrument?instrumentID=ETH-BTC&instrumentID=CYB-BTC（Optional）

## 2.6. Query User's Assets<br \>&emsp;^查询用户资产

> Response:
  
```json
[//example
    {
    "accountID": "0000000020",    //User's account ID ^用户的账户ID
    "asset": "BTC",               //Digital asset type ^资产类型
    "balance": "9.96",            //The balance of the digital asset ^该类资产的余额
    "available": "9.91"           //The available balance of the digital asset ^该类资产的可用余额
    },
]
```

Query the user's asset information.

查询用户的资产信息。

* Method: GET
* Version: v1
* Path: /account/assets

## 2.7. Query the User's Rate Limit<br \>&emsp;^查询流量控制

> Response:
   
```json
{//example
    "queryRateLimit": "10",      //Rate limit for query interfaces (times per second) ^查询接口限速值（次/秒）
    "orderRateLimit": "300",    //Rate limit for inserting and canceling order interfaces (times per second) ^下单和撤单接口限速值（次/秒）
},
```

The server will perform rate limiting for the requests sent by API users. Query the user's rate limit:

服务器会对 API 用户提交的请求进行流量控制。查询本用户的流量控制信息：

* Method: GET
* Version: v1
* Path: /referenceData/rateLimit

Note: If there are m order IDs in 1 batch cancel order, the number of orders counted within the rate limit will be m.

注意：1个批量撤单（ batchCancel ）中若包括了m个订单ID，则计入流量指标的数量是m。

## 2.8. Insert Order<br \>&emsp;^创建订单

> Request:
   
```json
{//example
    "instrumentID": "ETH-BTC",             //Instrument ID ^合约ID
    "orderPriceType": "limit",             //Price type, only limit orders are supported now (limit) ^订单价格类型，目前支持限价订单（limit）
    "direction": "buy",                    //Direction: buy/sell ^方向：买/卖
    "limitPrice": "1.23",                  //Price of limit order (can not be empty and needs to be greater than 0.) ^限价订单的价格（不能为空且需大于0）
    "volumeTotalOriginal": "4.56",         //Original total volume (can not be empty and needs to be greater than 0.) ^初始的总数量（不能为空且需大于0）
    "timeCondition": "GTC",                //Time in force conditions, only GTC (Good Till Cancel) is supported now. GFS (Good For Session), IOC (Immediate Or Cancel), etc. will be supported in the future. ^有效期类型，目前支持 GTC（撤销前一直有效），未来计划支持 GFS（当前Session有效）、 IOC（立即成交否则撤销）等。
    "orderLocalID": "15362989689714502"    //order local ID assigned by the user rather than the exchange. It should be incremental and unique. ^由用户（而非交易所）分配的本地订单ID，建议确保其增长性及唯一性。
}
```

> Response:
    
```json
{//example
    "orderLocalID": "15362989689714502",    //orderLocalID is an order local ID assigned by the user rather than the exchange. It should be incremental and unique. ^orderLocalID 是由用户（而非交易所）分配的本地订单ID，建议确保其增长性及唯一性。
}
```

Once the order is confirmed by the exchange, the corresponding assets will be frozen until completely executed.
   
创建订单一旦成功，相应占用资产在订单未成交期间将被冻结。

We currently only support limit orders (orderPriceType: limit).
   
目前支持限价单（ orderPriceType ： limit ）。

The orderLocalID is an order local ID assigned by the user rather than the exchange. The maximum length of orderLocalID is 20. <font color="#dd0000" face="black">In order to facilitate the management of the user's subsequent order, it is strongly recommended for users to ensure that the orderLocalID is incremental and unique, such as: String (timestamp * 10000 + Local self-increment sequence) </font>
   
orderLocalID 是由用户（而非交易所）分配的本地订单ID， orderLocalID 的最大长度为20， <font color="#dd0000" face = "black">为了用户后续订单管理的便利，强烈建议用户要保证 orderLocalID 的增长性及唯一性，例如采用： String(时间戳 * 10000 + 本地自增序列) </font>。

Calling this interface only returns whether or not the exchange has received the order. The relevant information of the order will be published via the WebSocket private flow. If WebSocket not used, the information can be queried by the below REST interface - 'Order Query'.
   
本接口调用后仅返回交易所是否收到了用户的下单请求。该订单的相关信息会通过 WebSocket 私有流推送。如果不使用 WebSocket ，也可以通过后面的“查询订单”接口获得相关信息。

* Method: POST
* Version: v1
* Path: /order/insert

## 2.9. Cancel Order<br \>&emsp;^撤销订单

> Request:
  
```json
{//example
    "orderLocalID": "15362989689714502"    //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
}
```

> Response:
   
```json
//example
{
    "orderLocalID": "15362989689714502",    //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
    "message": "received"                   //Status: "received"/"failed". "received" - the exchange has received a cancellation request, but this does not mean that the order has been canceled. ^状态：“ received 已收到”/“ failed 已失败”。“ received ” - 表示交易所已收到了撤单请求，但不代表已经撤单完毕。
}
```

The order that is confirmed by the exchange, but not completely executed, can be canceled in 2 ways: either by orderLocalID or by orderSysID.

已被交易所确认的订单，在未被完全成交之前可以撤销，有两种撤单的方式：根据本地订单ID（ orderLocalID ）撤单、根据系统订单ID（ orderSysID ）撤单。

* Cancel the order by orderLocalID: The orderLocalID is an order local ID assigned by the user rather than the exchange. If the orderLocalIDs are repeated due to any problems on the user's side, there will be multiple orders with identical orderLocalID. <font color="#dd0000" face="black">In this case, only one of these orders would be canceled by this request</font> . 
<br />
根据本地订单ID（ orderLocalID ）撤单： orderLocalID  是由用户（而非交易所）分配的本地订单ID。若由于用户自身的问题导致 orderLocalID 重复，则会存在多个订单具有相同的 orderLocalID ，<font color="#dd0000" face = "black">此时本接口仅会撤销其中的1笔订单</font>。

  + Method: POST
  + Version: v1
  + Path: /order/cancelByLocalID 

> Request:
  
```json
{//example
    "orderSysID": "1412943752000004"    //order system ID assigned by the exchange ^由交易所分配的系统订单ID
}
```

> Response:
   
```json
{//example
    "orderSysID": "1412943752000004",    //order system ID assigned by the exchange ^由交易所分配的系统订单ID
    "message": "received"                //Status: "received"/"failed". "received" - the exchange has received a cancellation request, but this does not mean that the order has been canceled. ^状态：“ received 已收到”/“ failed 已失败”。“ received ” - 表示交易所已收到了撤单请求，但不代表已经撤单完毕。
}
```

* Cancel the order by orderSysID: orderSysID is an order system ID assigned by the exchange, who guarantees that the orderSysID is unique. 
<br />
根据系统订单ID（ orderSysID ）撤单： orderSysID 是由交易所分配的系统订单ID，交易所会保证其唯一性。

  + Method: POST
  + Version: v1
  + Path: /order/cancelBySysID

## 2.10. Batch Cancel Order<br \>&emsp;^批量撤销订单

> Request:
   
```json
{//example
    "orderSysIDs": [ "1412943752000004",...]    //The group can contain up to 10 orderSysIDs ^最多可包含10个系统订单ID
}
```

> Response:
   
```json
[//example
    "1412943752000004",...    //The orderSysIDs received by the exchange to cancel ^交易所收到的请求撤单的系统订单ID
]
```

The interface can cancel up to a maximum of <font color="#dd0000" face="black">10</font> orders every time.

本接口每次可以撤销最多<font color="#dd0000" face = "black">10</font>笔订单。

* Method: POST
* Version: v1
* Path: /order/batchCancel

## 2.11. Query Order<br \>&emsp;^查询订单

> Request:
   
```json
{//example
    "orderLocalID": "15362989689714502",    //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
}
```
> Response:

```json
{//example
    "orderSysID": "1412943752000004",      //order system ID assigned by the exchange ^由交易所分配的系统订单ID
    "orderLocalID": "15362989689714502",   //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
    "instrumentID": "ETH-BTC",             //Instrument ID ^合约ID
    "direction": "buy",                    //Direction: buy/sell ^方向：买/卖
    "orderPriceType": "limit",             //Price type, only limit orders are supported now (limit) ^订单价格类型，目前支持限价订单（limit）
    "limitPrice": "0.1",                   //Price of limit order ^限价订单的价格
    "volumeTotalOriginal": "1",            //Original total volume ^初始的总数量
    "volumeTotal ": "0.2",                 //Remaining open volume ^剩余未成交量
    "volumeTraded": "0.8",                 //Total traded volume ^已成交总量
    "timeCondition": "GTC",                //Time in force conditions, only GTC (Good Till Cancel) is supported now. GFS (Good For Session), IOC (Immediate Or Cancel), etc. will be supported in the future. ^有效期类型，目前支持 GTC（撤销前一直有效），未来计划支持 GFS（当前Session有效）、 IOC（立即成交否则撤销）等。
    "orderStatus": "1",                    //Order status: (0-fully executed; 1-partially executed, order still in order book; 2-partially executed, order not in order book; 3-not executed, order still in order book; 4-not executed, order not in order book; 5-fully cancelled; 6-partially executed, partially cancelled) ^订单状态（0-全部成交；1-部分成交，订单还在订单簿中；2-部分成交，订单不在订单簿中；3-未成交，订单还在订单簿中；4-未成交，订单不在订单簿中；5-订单全部被撤销；6-部分成交，部分撤单）
    "actionTimestamp": "1536298968123",    //Timestamp of the last change to the order status^ 状态变化时间戳
    "insertDate": "20180829",              //Date of insertion ^下单日期
    "insertTime": "07:45:10",              //Time of insertion ^下单时间
    "insertTimestamp": "1536298968",       //Timestamp of insertion ^下单时间戳
    "cancelDate": "20180907",              //Date of cancellation ^撤单日期
    "cancelTime": "05:42:57",              //Time of cancellation ^撤单时间
}
```

Similar to cancelling orders, there are two ways to query orders: either by orderLocalID or by orderSysID.

与撤销订单类似，有两种查询订单的方式：分别根据本地订单ID（ orderLocalID ）、系统订单ID（ orderSysID ）查询。

* Query by order local ID (orderLocalID): only <font color="#dd0000" face="black">open orders, which have not been fully executed</font>, will be returned. The orderLocalID is an order local ID assigned by the user rather than the exchange. If the orderLocalIDs are repeated due to any problems on the user's side, there will be multiple orders with identical orderLocalID. <font color="#dd0000" face="black">Only one of these orders will be returned by this interface</font> .
<br />
根据本地订单ID（ orderLocalID ）查询订单：仅返回<font color="#dd0000" face = "black">还未被完全成交</font>的订单。 orderLocalID 是由用户（而非交易所）分配的本地订单ID。若由于用户自身的问题导致 orderLocalID 重复，则会存在多个订单具有相同的 orderLocalID ，<font color="#dd0000" face = "black">此时本接口仅返回其中的1笔订单</font>。

  + Method: POST
  + Version: v1
  + Path: /order/getOrderByLocalID

> Request:
    
```json
{//example
    "startTimestamp": "1420674445",    //Start timestamp of query (optional) ^查询起始时间戳（非必填）
    "endTimestamp": "1420674567",      //End timestamp of query (optional) ^查询结束时间戳（非必填）
    "orderStatus": "active",           //Order status (optional) {(active-order still in order book, corresponding to status 1, 3), (closed-order not in order book, corresponding to status 0, 2, 4, 5, 6), (not specified or an empty string - all status)} ^订单状态（非必填）{(active-订单在订单簿中，对应状态1、3), (closed-订单不在订单簿中，对应状态0、2、4、5、6), (未指定本项或本项为空字符串-对应所有状态)｝。
    "instrumentID": "ETH-BTC",         //Instrument ID (optional) ^合约ID（非必填）
    "orderSysID": "1412943752000004"   //Order system ID (optional) ^系统订单ID（非必填）
}
```

> Response:
   
```json
{//example
    "orderSysID": "1412943752000004",     //order system ID assigned by the exchange ^由交易所分配的系统订单ID
    "orderLocalID": "15362989689714502",  //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
    "instrumentID": "ETH-BTC",            //Instrument ID ^合约ID
    "direction": "buy",                   //Direction: buy/sell ^方向：买/卖
    "orderPriceType": "limit",            //Price type, limit order is supported now (limit) ^订单价格类型，目前支持限价订单（limit）
    "limitPrice": "0.11",                 //Price of limit order ^限价订单的价格
    "volumeTotalOriginal": "1",           //Original total volume ^初始的总数量
    "volumeTotal ": "0.2",                //Remaining open volume ^剩余未成交量
    "volumeTraded": "0",                  //Total traded volume ^已成交总量
    "timeCondition": "GTC",               //Time in force conditions, GTC (Good Till Cancel) is supported now, GFS (Good For Session), IOC (Immediate Or Cancel), etc. will be supported in the future. ^有效期类型，目前支持 GTC （撤销前一直有效），未来计划支持 GFS （当前Session有效）、 IOC （立即成交否则撤销）等。
    "orderStatus": "0",                   //Order status: (0-fully executed; 1-partially executed, order still in order book; 2-partially executed, order not in order book; 3-not executed, order still in order book; 4-not executed, order not in order book; 5-fully cancelled; 6-partially executed, partially cancelled) ^订单状态（0-全部成交；1-部分成交，订单还在订单簿中；2-部分成交，订单不在订单簿中；3-未成交，订单还在订单簿中；4-未成交，订单不在订单簿中；5-订单全部被撤销；6-部分成交，部分撤单）
    "actionTimestamp": "1536298968123",    //Timestamp of the last change to the order status^ 状态变化时间戳
    "insertDate": "20180829",             //Date of insertion ^下单日期
    "insertTime": "07:45:10",             //Time of insertion ^下单时间
    "insertTimestamp": "1536298968",      //Timestamp of insertion ^下单时间戳
    "cancelDate": "20180907",             //Date of cancellation ^撤单日期
    "cancelTime": "05:42:57",             //Time of cancellation ^撤单时间
}
```

* Query by order system ID ( orderSysID ): If orderSysID is not specified, it will query the maximum number of <font color="#dd0000" face="black">100</font> orders, sorted by time of insertion <b>(starting from the most recent order to the oldest order)</b>.
<br />
根据系统订单ID（ orderSysID ）查询订单：如果不指定 orderSysID ，最多返回<font color="#dd0000" face = "black">100</font>条订单，按照下单时间排序<b>（最近的订单排在最前面）</b>。

  - Method: POST
  - Version: v1
  - Path: /order/getOrder

## 2.12. Query Trade<br \>&emsp;^查询成交

> Request:
  
```json
{//example
    "startTimestamp": "1420674445",  //Start timestamp of query (optional) ^查询起始时间戳（非必填）
    "endTimestamp": "1420674445",    //End timestamp of query (optional) ^查询结束时间戳（非必填）
    "instrumentID": "ETH-BTC"        //Instrument ID (optional) ^合约ID（非必填）
}
```

> Response:
    
```json
[//example
    {
        "tradeID": "141334225600003",           //trade ID ^成交ID
        "orderSysID": "1412943752000004",       //order system ID assigned by the exchange ^由交易所分配的系统订单ID
        "orderLocalID": "15362989689714502",    //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
        "instrumentID": "ETH-BTC",              //Instrument ID ^合约ID
        "direction": "buy",                     //Direction: buy/sell ^方向：买/卖
        "price": "1.1",                         //Trade price ^成交价格
        "priceSource": "buy",                   //Source of trade price ^成交价来源
        "volume": "5.23512",                    //Traded volume in the execution ^本次成交的数量
        "fee": "0.1",                           //Fee ^手续费
        "tradeDate": "20180829",                //Date of trade ^成交日期
        "tradeTime": "07:45:10",                //Time of trade ^成交时间
        "tradeTimestamp": "1420674445"          //Timestamp of trade ^成交时间戳
    },
    ...
]
```

Query the maximum number of <font color="#dd0000" face="black">100</font> trades which is sorted by traded time <b>(starting from most recent trade to the oldest trade)</b>.

最多查询 <font color="#dd0000" face="black">100</font> 条成交，按照成交时间排序<b>（最近的成交排在最前面）</b>。

* Method: POST
* Version: v1
* Path: /trade/getTrade

## 2.13. Query Level2 Market Data<br \>&emsp;^查询Level2行情数据

> Response:
    
```json
{//example
    "instrumentID ": "ETH-BTC",      //Instrument ID ^合约ID
    "buy": [["6500.11", "0.45054140"], ["6500.10", "0.35054140"], ...],   //Buy prices and volumes ^买价位
    "sell": [["6500.15", "0.57753524"], ["6500.16", "0.77753524"], ...],  //Sell prices and volumes ^卖价位
    "pubDate": "20180907",           //Publish date ^发布日期
    "pubTime": "09:08:07",           //Publish time ^发布时间
    "pubTimestamp": "1420674445201"  //Publish timestamp ^发布时间戳
}
```

Query Level2 market data for a specific instrument.

查询指定合约的 level2 行情信息。

* Method: GET
* Version: v1
* Path: /marketData/getLevel2?instrumentID=ETH-BTC

Instrument ID must be specified.

必须指定合约ID。

# 3. WebSocket Interface Description<br \>&emsp;^WebSocket 接口说明

The WebSocket protocol, as defined by HTML5, allows the server to push/publish data to the client, which can save more server resources and bandwidth, and can communicate in real time. Through the WebSocket API, the client and the server only need one handshake to create a persistent connection and a two-way data transmission.

HTML5 定义了 WebSocket 协议，允许服务端向客户端推送数据，能更好的节省服务器资源和带宽，并且能够实时地进行通讯。在 WebSocket API 中，客户端和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

## 3.1. WebSocket Request URL<br \>&emsp;^WebSocket 请求 URL

The WebSocket interface request URL is: wss://xdaex.com/APITradeWS/v1/messages.

This request URL contains the following main components:

* Protocol: wss
* Domain name: xdaex.com (To access the test\preview environment, please replace it with the corresponding domain name.)
* Interface name: APITradeWS
* Version: v1
* Path: /messages

WebSocket 接口请求 URL 为: wss://xdaex.com/APITradeWS/v1/messages 。

URL 中主要包括以下组成部分：

* 协议: wss
* 域名: xdaex.com （如访问测试或预览环境，则需替换成指定的域名）
* 接口名称: APITradeWS
* 版本: v1
* 路径: /messages

## 3.2. Private Message Flow<br \>&emsp;^私有消息流

> Example of subscribing request:

```json
{ "type": "subscribe", "channel": {"user":[API-KEY, API-SIGNATURE, AUTH-TYPE]} }
```

> Example of unsubscribing request:

```json
{ "type": "unsubscribe", "channel": {"user":[API-KEY, API-SIGNATURE, AUTH-TYPE]} }
```

API Users can subscribe to the private message flows published by the server. The subscription to the private flow needs the API-KEY for authentication.

API 接入用户可通过订阅方式获取由交易所推送的私有消息流。提交私有流订阅请求时需要通过 API-KEY 进行认证。

- Use the string "WSS/APITradeWS/v1/messages" directly (<font color="#dd0000" face="black">no need to hash the string</font>) to generate a digital signature or a message authentication code. 

- 直接使用字串"WSS/APITradeWS/v1/messages"（<font color="#dd0000" face ="black">不需要对它计算hash </font>）生成数字签名或消息验证码。

- Steps to get an API-SIGNATURE with AUTH-TYPE "PUB-PRIV"

  - Use the private key to sign the string "WSS/APITradeWS/v1/messages" (Algorithm: ECC, Elliptic Curve: secp256k1): sign ("WSS/APITradeWS/v1/messages")
    - Example:
      - Input:
        - data = "WSS/APITradeWS/v1/messages"
        - privateKey = "VAoASo72TbEYsasQAD64nHlZVyBglPw13kfvlqM1j5Z="
      - Output:
        - signedData = {"r":"vFDn9TZI041Xyeklcbnw8Qp20wx2ODfCgqa3dgm5t2s=", "s":"U7FgvfG+xjiZtxXtnKhGCSWwkSuUncsFOPr+M9UuV1k=", "v":28}

- 获得 AUTH-TYPE 为 "PUB-PRIV" 的 API-SIGNATURE 的步骤

  - 用私钥对字串 "WSS/APITradeWS/v1/messages" 签名（算法： ECC ，椭圆曲线： secp256k1 ）: sign("WSS/APITradeWS/v1/messages")
    - 范例：
      - 输入：
        - data = "WSS/APITradeWS/v1/messages"
        - privateKey = "VAoASo72TbEYsasQAD64nHlZVyBglPw13kfvlqM1j5Z="
      - 输出：
        - signedData = {"r":"vFDn9TZI041Xyeklcbnw8Qp20wx2ODfCgqa3dgm5t2s=", "s":"U7FgvfG+xjiZtxXtnKhGCSWwkSuUncsFOPr+M9UuV1k=", "v":28}

- Steps to get an API-SIGNATURE with AUTH-TYPE "HMAC"

  - Derive the message authentication code from the string and the secret key (Algorithm:
    SHA256HMAC): SHA256HMAC (secretKey, "WSS/APITradeWS/v1/messages")
    - Example:
      - Input:
        - data = "WSS/APITradeWS/v1/messages"
        - secretKey = "0adabfc46fa8062d92a4e8313ffce285efbb70dfcdb1e3d0c415dd17759a8303"
      - Output:
        - signedData = "pRXgKcONtvwSCAENxYTIjwBJaZtPa23GLQnm7+xPmhY="

- 获得 AUTH-TYPE 为 "HMAC" 的 API-SIGNATURE 的步骤

  - 用密钥结合字串计算出消息验证码（算法： SHA256HMAC ）: SHA256HMAC(secretKey, "WSS/APITradeWS/v1/messages")
    - 范例：
      - 输入：
        - data = "WSS/APITradeWS/v1/messages"
        - secretKey = "0adabfc46fa8062d92a4e8313ffce285efbb70dfcdb1e3d0c415dd17759a8303"
      - 输出：
        - signedData = "pRXgKcONtvwSCAENxYTIjwBJaZtPa23GLQnm7+xPmhY="

After subscribing to a private flow, the subscriber will receive the following types of messages:

当订阅私有消息流之后，订阅者将收到以下几类消息：

## 3.2.1. Change of Order Status<br \>&emsp;^订单状态变化

> Messages:
    
```json
{//example
    "type": "order_return",                //Message type ^消息类型
    "orderSysID": "1412943752000004",      //order system ID assigned by the exchange ^由交易所分配的系统订单编号
    "orderLocalID": "15362989689714502",   //order local ID assigned by the user rather than the exchange. ^由用户（而非交易所）分配的本地订单ID。
    "instrumentID ": "ETH-BTC",            //Instrument ID ^合约ID
    "direction": "buy",                    //Direction: buy/sell ^方向：买/卖
    "orderPriceType": "limit",             //Price type, limit order is supported now (limit) ^订单价格类型，目前支持限价订单（limit）
    "limitPrice": "502.1",                 //Price of limit order ^限价订单的价格
    "volumeTotalOriginal": "1.34",         //Original total volume ^初始的总数量
    "volumeTotal": "1.33",                 //Remaining open volume ^剩余未成交量
    "volumeTraded": "0.1",                 //Total traded volume ^已成交总量
    "timeCondition": "GTC",                //Time in force conditions, only GTC (Good Till Cancel) is supported now. GFS (Good For Session), IOC (Immediate Or Cancel), etc. will be supported in the future. ^有效期类型，目前支持 GTC（撤销前一直有效），未来计划支持 GFS（当前Session有效）、 IOC（立即成交否则撤销）等。
    "orderStatus": "5",                    //Order status: (0-fully executed; 1-partially executed, order still in order book; 2-partially executed, order not in order book; 3-not executed, order still in order book; 4-not executed, order not in order book; 5-fully cancelled; 6-partially executed, partially cancelled) ^订单状态（0-全部成交；1-部分成交，订单还在订单簿中；2-部分成交，订单不在订单簿中；3-未成交，订单还在订单簿中；4-未成交，订单不在订单簿中；5-订单全部被撤销；6-部分成交，部分撤单）
    "actionTimestamp": "1536298968123",    //Timestamp of the last change to the order status^ 状态变化时间戳
    "insertDate": "20180907",              //Date of insertion ^下单日期
    "insertTime": "05:42:38",              //Time of insertion ^下单时间
    "insertTimestamp": "1536298968",       //Timestamp of insertion ^下单时间戳
    "cancelDate": "20180907",              //Date of cancellation ^撤单日期
    "cancelTime": "05:42:57",              //Time of cancellation ^撤单时间
}
```

When the order status changes (e.g. success of insertion, success of cancellation, execution, etc.), the following message will be published:

当订单状态发生变化时（如：下单成功、撤单成功、成交等），如下消息会被推送：

## 3.2.2. Order Execution Report<br \>&emsp;^订单成交回报

> Messages:
    
```json
{//example
    "type": "trade_return",                //Message type ^消息类型
    "tradeID": "141334225600003",          //trade ID ^成交ID
    "orderSysID": "1412943752000004",      //order system ID assigned by the exchange ^由交易所分配的系统订单ID
    "orderLocalID": "15362989689714502",   //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
    "instrumentID ": "ETH-BTC",            //Instrument ID ^合约ID
    "direction": "buy",                    //Direction: buy/sell ^方向：买/卖
    "price": "400.23",                     //Trade price ^成交价格
    "priceSource": "buy",                  //Source of trade price ^成交价来源
    "volume": "5.23512",                   //Traded volume in the execution ^本次成交的数量
    "fee ": "0.001",                       //Fee ^手续费
    "tradeDate": "20180829",               //Date of trade ^成交日期
    "tradeTime": "09:33:01",               //Time of trade ^成交时间
    "tradeTimestamp": "1420674445201",     //Timestamp of trade ^成交时间戳
}
```

When the order is executed, the following message will be published:

当订单成交时，如下消息会被推送：

## 3.2.3. Failure to Inserting Order<br \>&emsp;^创建订单失败

> Messages：
    
```json
{//example
    "type": "order_insert_rsp",             //Message type ^消息类型
    "orderLocalID": "15362989689714502",    //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
    "respCode": "2012",                     //Failure response code. Please refer to the contents of the above 'Failure Response Table' ^失败应答码，请参见后面的“失败应答表”内容。
    "respMsg": "Invalid volume"             //Failure response message. Please refer to the contents of the above 'Failure Response Table' ^失败应答消息，请参见后面的“失败应答表”内容。

}
```

When order insertion fails, the following message will be published:

当下单失败时，如下消息会被推送：

## 3.2.4. Failure to Cancelling Order<br \>&emsp;^撤销订单失败

> Messages：

```json
{//example
    "type": "order_cancel_rsp",             //Message type ^消息类型
    "orderSysID": "1412943752000004",       //order system ID assigned by the exchange ^由交易所分配的系统订单ID
    "orderLocalID": "15362989689714502",    //order local ID assigned by the user rather than the exchange ^由用户（而非交易所）分配的本地订单ID
    "respCode": "1005",                     //Failure response code. Please refer to the contents of the above 'Failure Response Table' ^失败应答码，请参见后面的“失败应答表”内容。
    "respMsg": "Internal service error"     //Failure response message. Please refer to the contents of the above 'Failure Response Table' ^失败应答消息，请参见后面的“失败应答表”内容。
}
```

When order cancellation fails, the following message will be published:

当撤单失败时，如下消息会被推送：

## 3.3. Public Message Flow<br \>&emsp;^公有消息流

The following message flows can be subscribed without API-SIGNATURE.

以下消息流不需要 API-SIGNATURE 就可订阅。

## 3.3.1. Ticker<br \>&emsp;^逐笔成交信息

> Subscribe and unsubscribe to ticker requests:
    
```json
{ "type": "subscribe", "channel": {"ticker":["ETH-BTC", "CYB-BTC"]} }

{ "type": "unsubscribe", "channel": {"ticker":["ETH-BTC", "CYB-BTC"]} }
```

> Messages:
   
```json
{//example
    "type": "trade_detail",         //Message type ^消息类型
    "instrumentID ": "ETH-BTC",     //Instrument ID ^合约ID
    "price ": "1.97",               //Trade price ^成交价格
    "priceSource":"buy",            //Source of trade price ^成交价来源
    "volume ": "0.03",              //Traded volume in the execution ^本次成交的数量
    "tradeDate": "20180907",        //Date of trade ^成交日期
    "tradeTime": "10:08:06",        //Time of trade ^成交时间
    "tradeTimestamp": "1420674445"  //Timestamp of trade ^成交时间戳
}

{//example
    "type": "trade_detail",         //Message type ^消息类型
    "instrumentID ": "CYB-BTC",     //Instrument ID ^合约ID
    "price ": "0.01",               //Trade price ^成交价格
    "priceSource":"buy",            //Source of trade price ^成交价来源
    "volume ": "0.03",              //Traded volume in the execution ^本次成交的数量
    "tradeDate": "20180907",        //Date of trade ^成交日期
    "tradeTime": "10:08:06",        //Time of trade ^成交时间
    "tradeTimestamp": "1420674445"  //Timestamp of trade ^成交时间戳
}
```

If you've subscribed to the ticker message flow, the following ticker message will be published when orders are executed:

如果订阅了 ticker 消息流，当有订单被成交时，如下的 ticker 消息会被推送：

## 3.3.2. Level2 Market Data<br \>&emsp;^Level2行情数据

> Requests to subscribe and unsubscribe to level2 market data:
  
```json
{ "type": "subscribe", "channel": {"level2@10":["ETH-BTC"]} }

{ "type": "unsubscribe", "channel": {"level2@10":["ETH-BTC"]} }
```
> Messages:
   
```json
{//example
    "type": "level2@10_snapshot",           //Message type: full snapshot ^消息类型：完整的行情快照
    "instrumentID ": "ETH-BTC",             //Instrument ID ^合约ID
    "buy": [["5500.21", "0.47545240"], ...],  //Buy prices and volumes ^买价位
    "sell": [["5500.25", "0.56544624"], ...], //Sell prices and volumes ^卖价位
    "pubDate": "20180907",                  //Publish date ^发布日期
    "pubTime": "09:07:45",                  //Publish time ^发布时间
    "pubTimestamp": "1420674445201"         //Publish timestamp ^发布时间戳
}

{
    "type": "level2@10_update",             //Message type: incremental update ^消息类型：增量的行情更新
    "instrumentID": "ETH-BTC",              //Instrument ID ^合约ID
    "buy": [["5500.21", "0.47545240"], ...],  //Buy prices and volumes ^买价位
    "sell": [["5500.25", "0"], ...],          //Sell prices and volumes (note: volume is 0) ^卖价位（注意：数量为0）
    "pubDate": "20180907",                  //Publish date ^发布日期
    "pubTime": "09:07:45",                  //Publish time ^发布时间
    "pubTimestamp":"1420674445201"          //Publish timestamp ^发布时间戳
}
```

Different subscription topics correspond to different depths of market data (price levels), as below.

行情的不同深度（价位档数）对应不同的订阅主题：

Topic <br />主题|Price Levels <br />价位档数| Snapshot Frequency (per second) <br />快照频率（每秒）
-------- |---------------------|-----------
level2   |5                    |2
level2@10|10                   |2
level2@20|20                   |2
level2@50|50                   |2

If you subscribe to level2 market data message flow, at first the full snapshot of level2 market data will be published and then only incremental updates of level2 market data are sent.

如果订阅了 level2 行情消息流，首次先推送完整的行情快照，之后就仅推送增量的行情更新。

## 3.4. Heartbeat Mechanism<br \>&emsp;^心跳机制

In order to implement the heartbeat mechanism, the WebSocket subscriber should sends a 'ping' message to the server once per <b>15</b> seconds and then the server replies to the subscriber with a 'pong' message.

为了实现心跳机制，WebSocket 订阅方每<b>15</b>秒向服务器发送一次"ping" 消息，然后服务器会向订阅方回复一个 "pong" 消息。

# 4. Sample Code ^示例代码
## 4.1. REST Interface ^REST接口
* Java
    +  [Java SDK](https://github.com/XDAEX/API/raw/master/java/xdaex-trading-sdk-1.0.jar)
    +  [Java Sample](https://github.com/XDAEX/API/wiki/REST_sample_java)
* Python
    +  [Python Sample](https://github.com/XDAEX/API/wiki/REST_sample_python)
* Golang
    +  [Golang Package](https://github.com/XDAEX/API/tree/master/golang/eccutils)
    +  [Golang Sample](https://github.com/XDAEX/API/wiki/REST_sample_golang)
* C++
    +  [C++ header](https://github.com/XDAEX/API/raw/master/cpp/eccutils.hpp)
    +  [C++ Sample](https://github.com/XDAEX/API_Docs/wiki/REST_sample_cpp)

## 4.2. WebSocket Subscription ^WebSocket订阅
* [JavaScript](https://github.com/XDAEX/API/wiki/WebSocket_subscription_sample_javascript)
* [Golang](https://github.com/XDAEX/API/wiki/WebSocket_subscription_sample_golang)
* [C++](https://github.com/XDAEX/API/wiki/WebSocket_subscription_sample_cpp)

# 5. Request-Response Code Table<br \>&emsp;^请求应答编码对照表
## 5.1. Success Response ^成功应答
The HTTP response code is 200 for all success responses which may contain other information about the response. Please refer to the details of the specific interface.

对于成功的请求，HTTP 协议状态返回码均为 200 ，同时包含具体 Response 信息，具体可详见各类业务接口。

## 5.2. Failure Response ^失败应答
Failure modes are described by respcode and respmsg in the failure response.

对于失败的请求，在其应答中通过 respCode 和 respMsg 说明失败的原因。

respCode <br /> 应答码 |             respMsg <br />应答消息     |      Note <br />备注
-------- |----------------------------------------|-----------
1000|Failed to validate signature                 |--- <br /> 签名验证失败
1001|Request timeout (30s)                        |--- <br /> 已过期的请求（超过30秒）
1002|API-KEY not found                            |--- <br /> API-KEY 未找到
1003|Locked account                               |User account is locked <br /> 用户账号被锁定
1004|Too many requests                            |--- <br /> 接口访问频次超限
1005|Internal service error                       |--- <br /> 内部服务错误
1006|API-KEY blacklisted                          |--- <br /> API-KEY 已被禁用
1007|Unexpected property found in request body    |--- <br />请求参数属性有误
1008|Missing API-TIMESTAMP in HTTP request header |--- <br /> HTTP 请求消息头中缺少 API-TIMESTAMP
1009|Missing API-KEY in HTTP request header       |--- <br /> HTTP 请求消息头中缺少 API-KEY
1010|Missing API-SIGNATURE in HTTP request header |--- <br /> HTTP 请求消息头中缺少 API-SIGNATURE
1011|Missing valid AUTH-TYPE in HTTP request header  |--- <br /> HTTP 请求消息头中未提供有效的 AUTH-TYPE
2001|Order price decimal precision error          |The decimal points of the order price exceeded the limit (Please query the price decimal precision limit of the corresponding instrument via REST interface /marketData/instrument) <br /> 订单价格的小数部分位数超限（请通过 REST 接口 /marketData/instrument 查询对应合约的价格精度要求）
2002|Order volume decimal precision error         |The decimal points of the order volume (size) exceeded the limit (Query the volume decimal precision limit of the corresponding instrument via REST interface /marketData/instrument) <br /> 订单数量（ volume/size ）的小数部分位数超限（请通过 REST 接口 /marketData/instrument 查询对应合约的数量精度要求）
2003|Maximum orderLocalID field length: 20        |--- <br /> orderLocalID 长度超限（20）
2004|orderSysID or orderLocalID not exist         |--- <br /> orderSysID 或 orderLocalID 不存在。
2005|Maximum number of batch order cancellations: 10 |The quantity of batch cancel order exceeded the limit (10) <br /> 批量撤单数量超限（10条）
2006|instrumentID does not exist                  |--- <br /> 合约ID不存在
2007|Operation not supported by current instrument status |--- <br /> 当前合约状态禁止此项操作
2008|Client not found                             |--- <br /> 客户找不到
2009|Order field error                            |--- <br /> 订单字段错误
2010|Account not found                            |--- <br /> 找不到账号
2011|Insufficient trading asset balance           |--- <br /> 资产不足
2012|Invalid volume                               |--- <br /> 不合法的数量（ volume/size ）
2013|Invalid order type                           |--- <br /> 不支持的订单类型
2014|Order already fully executed                 |--- <br /> 该订单已经全部成交
2015|Order already canceled                       |--- <br /> 该订单已经撤销
2016|orderSysID can not be empty                  |--- <br /> 系统订单号不能为空
2017|Order direction (sell or buy) error          |--- <br /> 订单方向（ Sell or Buy ）错误
2018|orderLocalID can not be empty                |--- <br /> orderLocalID 不能为空
2019|Order volume should be less than 10000000    |--- <br /> 订单数量应小于10000000
2020|Order price should be less than 10000000     |--- <br /> 订单价格应小于10000000
2021|Order price type error                       |Order price type error. Only limit order (orderPriceType: limit) is supported now. <br /> 订单价格类型错误。目前支持限价订单（ orderPriceType:  limit ）。
2022|TimeCondition error                          |TimeCondition error. Only GTC (Good Till Cancel) is supported now. <br /> 订单有效期类型错误，目前支持 GTC（撤销前一直有效）。

For any questions related to the above, please sending an email to support@xdaex.com.

若 API 用户对于以上仍有疑问，请发送邮件至 support@xdaex.com 。

---
Version <br />版本 |Revision Date <br />修订日期|Change Log <br />修订摘要
----- |-----------|--------------------------------------------
3.1|2018/09/14|Update document structure. ^调整文档结构。
3.2|2018/09/15|Optimize document structure. ^优化文档结构。
4.0|2018/10/9|Optimize API, merge into a bilingual version. ^优化接口，合并成双语版本。
4.1|2018/10/15|Improve English content. ^改进英文内容。
4.2|2018/11/8|Add an authentication method "HMAC"; Add descriptions for the preview environment; Add an API to query API Release Number. ^增加“ HMAC ”授权方式；增加关于预览环境的描述；增加查询 API 发行号的 API。
---
