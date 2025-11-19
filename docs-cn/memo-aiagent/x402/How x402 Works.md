
本页面介绍 x402 中完整的支付流程：从初始请求到最终链上结算。

### 概述

x402 通过简单的 HTTP 请求-响应模式实现可编程支付。当客户端请求一个需要付费的资源时，服务器返回支付要求，客户端提交支付，服务器在验证/结算成功后返回资源内容。

### 交互流程

![[Payment-flow.png]]

1. `Client` 向 `resource server` 发起一次普通的 HTTP 请求。
2. `Resource server` 返回 `402 Payment Required` 状态码，并在响应体中附带一个 `Payment Required Response` JSON 对象（描述支付要求）。
3. `Client` 从响应中 `accepts` 字段的多个 `paymentDetails` 里选择其一，并根据该 `paymentDetails.scheme` 构造对应的 `Payment Payload`。
4. `Client` 再次向 `resource server` 发起请求，并在请求头中加入 `X-PAYMENT`，其值为序列化后的 `Payment Payload`。
5. `Resource server` 验证该 `Payment Payload` 是否有效：要么本地直接校验，要么将 `Payment Payload` 与所用的 `Payment Details` POST 到 `facilitator server` 的 `/verify` 接口。
6. `Facilitator server` 根据 `Payment Payload` 的 `scheme` 与 `networkId` 执行验证逻辑，并返回一个 `Verification Response`。
7. 若 `Verification Response` 有效，`resource server` 执行实际资源加工或任务；若无效，继续返回 `402 Payment Required` 以及新的 `Payment Required Response`。
8. `Resource server` 结算支付：要么直接与区块链交互，要么将 `Payment Payload` 与 `Payment Details` POST 到 `facilitator server` 的 `/settle` 接口，由其代理执行。
9. `Facilitator server` 根据 `Payment Payload` 的 `scheme` 与 `networkId` 向区块链提交支付交易。
10. `Facilitator server` 等待该支付在链上确认。
11. 支付确认后，`Facilitator server` 返回 `Payment Execution Response` 给资源服务器。
12. `Resource server` 返回 `200 OK`，响应体为客户端所请求的资源；同时若支付已成功执行，会在响应头加入 `X-PAYMENT-RESPONSE`，其值为 Base64 编码后的 `Settlement Response` JSON。

上述流程保证：客户端按要求支付；服务器（或协助者 Facilitator）负责验证与结算；资源交付时可携带支付执行的结果证明，方便后续审计或追踪。


