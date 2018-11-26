---
title: Home

# language_tabs: # must be one of https://git.io/vQNgJ
#   - json: JSON 

search: true
---

# Welcome to XDAEX API<br \>欢迎使用 XDAEX API

**Following functions can be implemented easily with API：**

* Query real-time market data
* Query market depth information
* Query total assets and available balance
* Query open orders
* Cancel open orders by volume

**Please read [XDAEX API Introduction](https://github.com/XDAEX/API/wiki/XDAEX_API_Introduction) before using API.**

**Please read [Introduction to Preview Environment](https://github.com/XDAEX/API/wiki/XDAEX_API_Preview_Environment_introduction) for details of XDAEX API preview environment.**

Please sign in xdaex.com and visit "User Center - API Interface" to manage your API-KEY.

Frequently asked questions [FAQ](https://support.xdaex.com/hc/en-us/search?utf8=%E2%9C%93&query=api)

**通过 API 可以快速实现以下功能：**

* 获取市场最新行情
* 获取买卖深度信息
* 查询资产总额和可用余额
* 查询自己当前尚未成交的挂单
* 批量撤单

**操作前请您务必阅读 [XDAEX 程序化交易接入说明](https://github.com/XDAEX/API/wiki/XDAEX_API_Introduction)。**

**想了解XDAEX程序化交易预览环境，请您阅读[预览环境接入说明](https://github.com/XDAEX/API/wiki/XDAEX_API_Preview_Environment_introduction)。**

请登录 xdaex.com 并访问 “个人中心 - API接口” 以管理您的 API-KEY。

常见问题请参考 [FAQ](https://support.xdaex.com/hc/zh-cn/search?utf8=✓&query=api)

# 1. XDAEX API Introduction<br \>&emsp;XDAEX 程序化交易接入说明
* [XDAEX API Introduction ^XDAEX 程序化交易接入说明](https://github.com/XDAEX/API/wiki/XDAEX_API_Introduction)
* [Introduction to Preview Environment ^预览环境接入说明](https://github.com/XDAEX/API/wiki/XDAEX_API_Preview_Environment_introduction)
* [API Letter of Agreement(PDF)](https://github.com/XDAEX/API/raw/master/documents/API_Letter_of_Agreement_EN.pdf) ^[API协议信(PDF)](https://github.com/XDAEX/API/raw/master/documents/API_Letter_of_Agreement_CN.pdf)
* [API Application Information(PDF)](https://github.com/XDAEX/API/raw/master/documents/API_Application_Information_EN.pdf) ^[API申请信息表(PDF)](https://github.com/XDAEX/API/raw/master/documents/API_Application_Information_CN.pdf)

# 2. API Specification<br \>&emsp;API说明文档

* For production environment and test environment ^生产环境与测试环境:

  [API Specification ^API说明文档 v4.2.0](https://yushizhao.github.io/XDAEXAPI/#1-quick-start)

* For preview environment ^预览环境:

  [API Specification ^API说明文档 v4.2.0](https://yushizhao.github.io/XDAEXAPI/#1-quick-start)

# 3. Sample Code<br \>&emsp;示例代码
## 3.1. REST Interface<br \>REST 接口
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

## 3.2. WebSocket Subscription<br \>WebSocket 订阅
* [JavaScript](https://github.com/XDAEX/API/wiki/WebSocket_subscription_sample_javascript)
* [Golang](https://github.com/XDAEX/API/wiki/WebSocket_subscription_sample_golang)
* [C++](https://github.com/XDAEX/API/wiki/WebSocket_subscription_sample_cpp)