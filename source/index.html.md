---
title: Home

# language_tabs: # must be one of https://git.io/vQNgJ
#   - json: JSON 

search: true

toc_footers:
    - Introduction Version 3.0
    - November 09, 2018
---

# API Introduction<br \>^程序化交易接入说明
## 1. General Rules ^总则
To apply for HashKey Pro API, please follow the steps below in order to acquire the qualification:

（1）Register and activate your account to become an HashKey Pro user.

（2）Complete at least KYC Level 1 verification and enable account 2FA settings.

（3）Submit the required API application documents.

（4）Perform the connector test in our API staging environment.

（5）Pass the account verification and technical evaluation to acquire full interface qualification.

若您有意申请HashKey Pro平台的程序化交易接入，您需按照以下步骤获得接入资格：

（1）注册并激活，成为HashKey Pro交易所用户；

（2）完成KYC1级认证并开通2FA验证；

（3）递交您的程序化交易资质；

（4）在测试联调环境开展接口测试；

（5）通过资质和技术评鉴，获得接入资格。

## 2. Become an HashKey Pro User<br \>&emsp;^成为 HashKey Pro 交易所用户
Please visit [pro.hashkey.com](pro.hashkey.com). Complete the registration and account activation process following the on-screen instructions.

请您访问 [pro.hashkey.com](pro.hashkey.com)， 按照导航提示完成用户的注册和激活。

## 3. KYC Level 1 Verification and 2FA<br \>&emsp;^完成KYC1级认证与2FA验证
After you have successfully activated your account, you need to complete the KYC level 1 verification and enable the 2FA settings inside the “User Center” section of your account. HashKey Pro Support team will assist in completing your verification timely within business hours.

在您成功激活账户后，您需要在用户中心界面内按照提示进行KYC1级认证，并开通2FA验证。HashKey Pro 运营人员会在其工作时间内及时协助您完成KYC1级认证。

## 4. Apply for API Qualification<br \>&emsp;^申请程序化交易资格
After completing the above steps, you need to send an email request to support@pro.hashkey.com.

To ensure account safety and security, your account information needs to be verified manually. Thus, upon your contact to Support, you will be given and requested to sign and agree to the API Application Form and API Agreement Letter.

When completing these documents, you need to provide few basic information to confirm your account ownership and your aim of using our API service. Also, by using HashKey Pro  services, we want to ensure that you understand the risks, scope and limitations of using our API to any 3rd party software or platform.

Furthermore, in order to facilitate the following staging test, please ensure to check and confirm the IP address that you will indicate in the API Application Form.

完成上述步骤后，需要您发送邮件到support@pro.hashkey.com进行申请。

为了保证账户安全，我们需要人工审核您的账户信息。因此，当您联系客服时，我们会给您发送《API申请信息表》和《API协议信》，需要您签署并同意。

在您填写这些文件时，需要提供一些基本信息来确认您是该账户的所有者及您使用我们API服务的目的。同时，我们想要确保您明白尤其是来自第三方软件或平台的风险，范围和限制。

为了便于您后续的联调测试工作，请您认真核对《API申请信息表》中填写的用以接口测试的请求端IP地址。

## 5. Perform the Staging Test<br \>&emsp;^开展联调测试
To help you get familiar with the HashKey Pro connector system, as well as to maintain your trade rights and other HashKey Pro users, we have prepared a staging environment which has the same function and connector of the actual production version for testing.

为了便于您熟悉HashKey Pro的交易接口系统，同时也为了维护您和其他HashKey Pro用户的交易权益，我们准备了与生产环境版本、功能与接口相同的联调测试环境供您测试。

### 5.1. Test Account and Assets<br \>&emsp;^测试账户和资产
If your application as mentioned in Step #4 has passed the verification, we will create a test account for you in the staging environment and provide the digital assets for your account. Once your staging account is ready, our Support team will notify you via email. In the email, an account name and initial password will be provided to you. 

Test assets will be deposited from time to time for your test account in the staging environment. The assets cannot be withdrawn to any wallets of any form or test chains.

Currently, the defined maintenance window period is from 2:00 PM to 3:00 PM (UTC+8), every Monday, Wednesday and Friday. During this period, we will perform maintenance procedures like version update, which may affect your access to the staging environment.  

若您在步骤#4中提交的申请已经通过审核，我们会及时为您在联调测试环境开通账户并进行测试资产充值。一旦测试账户准备就绪，我们的客服团队会通过邮件提供给您用以登录的用户名和初始密码。

联调测试环境中会定期为测试账户补充测试资产，其无法被提现到任何正式、测试链上的任何钱包内。

目前，HashKey Pro常规的维护窗口为每周一、三、五的2:00PM~ 3:00PM（UTC+8）。在这段期间内，我们可能进行版本同步、账户充值等维护工作，这将可能对您在测试环境访问业务的连续性受影响。

### 5.2. Access to the Staging Environment<br \>&emsp;^联调测试接入
You can access the staging environment through the domain [api-test.pro.hashkey.com](api-test.pro.hashkey.com). Please refer to the email with your account name and initial password.

If your host is unable to easily resolve the domain name, you can bind [api-test.pro.hashkey.com](api-test.pro.hashkey.com) with
45.249.244.103 by setting the hosts, then you can proceed to access the request. Before the test, the following prerequisites are necessary:

1. You need to bind your 2FA verification in the staging environment.
2. You need to choose API Key authentication method. The following two methods can be used in parallel:
* Public-Private Mode: This authentication method is based on asymmetric encryption (ECDSA, to be more specific). In this case, the public-private key pair is generated in the user’s browser. The private key is stored locally. Only the public key is uploaded to the server. Therefore, no one but the user has the private key.
* Message Authentication Code Mode: This authentication method is based on symmetric encryption (HMAC-SHA256, to be more specific). In this case, a secret key is generated in the server and a copy of the key is sent to the user to keep. If you do not want that the secret key is held by the exchange as well, you should choose to use the public-private key pair method.

您可以通过前述的用户名和初始密码[api-test.pro.hashkey.com](api-test.pro.hashkey.com)这一站点来登录测试联调环境。

如果您的主机无法进行域名解析，您可以通过hosts设置来绑定[api-test.pro.hashkey.com](api-test.pro.hashkey.com)与45.249.244.103，然后再进行访问请求。在开始测试前，还需要完成以下前置条件：

（1）您需要在测试联调环境绑定2FA验证方式；

（2）您需要选择API KEY授权模式，这两个模式可并行使用：

* 公钥-私钥模式：基于非对称加密的公钥-私钥模式（采用ECC算法），公钥和私钥由客户浏览器生成，私钥由用户保存，交易所仅保存用户提交的公钥信息。本方式可以保证用户的私钥信息仅有其本人保管。
* 消息验证码模式：基于对称加密的消息验证码模式（采用SHA256算法），密钥由交易所服务器生成并下发至用户保存。如用户不希望由交易所保管密钥，可不采用本方式，而是使用公钥-私钥模式。

### 5.3. Connector Documents<br \>&emsp;^接口文档
Refer to the link below to know more details about the HashKey Pro connector：
[https://hashkeypro.github.io/api-spec/](https://hashkeypro.github.io/api-spec/)
Refer to the FAQ link below to know more details about the HashKey Pro interface: 
[FAQ](https://support.xdaex.com/hc/en-us/search?query=api&utf8=%E2%9C%93)

若您想了解关于HashKey Pro接口的文档，请您移步：
[https://hashkeypro.github.io/api-spec/](https://hashkeypro.github.io/api-spec/)
若您想了解关于HashKey Pro接口的FAQ，请您移步：
[FAQ](https://support.xdaex.com/hc/zh-cn/search?utf8=✓&query=api)

### 5.4. Staging Environment Regulations<br \>&emsp;^联调测试守则
The staging environment also deploys threat-awareness strategies like threat-awareness strategies, connector traffic limit. In order to provide a stable staging environment for you and other users, we will reject all security, performance and traffic testing to be performed.

If a similar behavior occurs, it may be perceived as a malicious action and HashKey Pro will not provide service for you.

联调测试环境亦部署了威胁感知、接口流量限制等策略，为了保障您与其他用户的测试可以稳定运行，我们谢绝所有对于联调测试环境的安全测试、性能测试和流量测试。

如若发生类似行为，可能会被系统判定为恶意行为，并拒绝提供服务。

### 5.5. Listening to Valuable Suggestions<br \>&emsp;^聆听宝贵建议
If you encounter any issues or for any suggestions during the test, you are welcome to contact us through support@pro.hashkey.com. Your email will be carefully handled. 

All issues or malfunctions that impact the implementation of your test will be responded within 24 hours to facilitate solution.

在测试过程中有任何问题和建议，都欢迎您发送邮件到support@pro.hashkey.com与我们取得联系，我们将仔细阅读您的来信。

对于影响您持续开展测试工作的故障问题，我们会在24小时内作出回复，并为您提供解决方案。

## 6. Interface Access Eligibility<br \>&emsp;^获取接入资格
After one week of testing, once both you and our side conclude that your account is ready, we will notify you through email that you are qualified and approved for the actual production environment interface and wait for your next transactions.

Finally, thank you for choosing HashKey Pro!

一周的联调测试后，当您和我们都认可调试就绪后，我们会通过邮件通知您通过生产环境的申请，也期待您后续使用我们的服务。

最后，感谢您选择HashKey Pro！