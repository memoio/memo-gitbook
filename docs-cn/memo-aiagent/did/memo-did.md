# 1. MEMO DID设计

## 1.1 DID格式

MEMO DID的组成如下：

> **DID组成**
>
> ```http
> did:memo:<memo-specific-id>
> ```

一个DID例子如下：

> **DID用例**
>
> ```http
> did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e
> ```

其中<font color="red">`<memo-specific-id>`</font>=<font color="red">`hex(hash(address, nonce))`</font>

<font color="red">`<address>`</font>是以太坊地址，<font color="red">`<nonce>`</font>等于<font color="red">`<address>`</font>发起的交易nonce。



## 1.2 DID URL格式

MEMO DID URL组成如下：

>**DID URL组成**
>
>```http
>did:memo:<memo-specific-id> path [ ? <query> ] [ # <fragment> ]
>```

一些DIDURL例子如下：

> **DID URL例子1**
>
> ```http
> did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey
> ```

> **DID URL例子2**
>
> ```http
> did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1
> ```

支持以下path:

- 暂无

支持以下query：

- 暂无

支持以下fragment：

- <font color="red">`#masterKey`</font>：指定主公钥；
- <font color="red">`#key-<n>`</font>：指定其他的公钥；



## 1.3 DID文档核心属性

**DID文档**

| 属性名               | 是否必要 | 表示方法                                                  | 用处                        |
| -------------------- | -------- | --------------------------------------------------------- | --------------------------- |
| id                   | 是       | 一个符合[DID](#1.1 DID格式)构成规则的字符串               | 用户ID                      |
| verificationMethod   | 是       | 一组符合[验证方法](#verificationMethod)构成规则的验证方法 | 一组用于验证的公钥集合      |
| authentication       | 否       | 一组符合[DID URL](#1.2 DID URL格式)构成规则的字符串       | 以该DID的名义认证登录       |
| assertionMethod      | 否       | 一组符合[DID URL](#1.2 DID URL格式)构成规则的字符串       | 以该DID的名义发布可验证声明 |
| capabilityDelegation | 否       | 一组符合[DID URL](#1.2 DID URL格式)构成规则的字符串       | 委托他人管理该DID属下的文件 |
| recovery             | 否       | 一组符合[DID URL](#1.2 DID URL格式)构成规则的字符串       | 用于密钥遗失后恢复密钥      |



**<a name="verificationMethod">验证方法</a>**

| 属性名       | 是否必要 | 表示方法                                            | 用处                 |
| ------------ | -------- | --------------------------------------------------- | -------------------- |
| id           | 是       | 一组符合[DID URL](#1.2 DID URL格式)构成规则的字符串 | 验证方法标识符       |
| controller   | 是       | 一个符合[DID](#1.1 DID格式)构成规则的字符串         | 验证方法的所有者     |
| type         | 是       | 一个字符串                                          | 验证方法类型         |
| publicKeyHex | 是       | 一个符合16进制编码的字符串                          | 16进制表示的公钥信息 |



### 1.3.1 id属性

在DID文档中，id表示与DID文档对应的DID标识符。

**id**：在DID文档中，id属性必须给出且必须在DID文档的最顶层。id属性必须是一个字符串并且满足[1.1DID](#1.1 DID格式)所描述的构成规则。

> **DID文档例子1**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
>  	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> }
> ```



### 1.3.3 verificationMethod属性

在DID文档中，verificationMethod属性描述了一组验证证明的方式，通常使用公钥表示。通过公钥可以验证签名是否由对应的私钥签署，从而确定签名者的身份或权限。verificationMethod属性通常用于线下两个DID主体之间的交互，以实现认证或授权功能。

**verificationMethod**：在DID文档中，verificationMethod是必须的且至少包含主验证方法masterkey，另外verificationMethod属性必须是一组验证方法且满足所描述的构成规则。且需要包含以下属性：

- **id**：在验证方法中，id属性必须给出，另外id属性必须是一个字符串且满足[1.2DID URL](#1.2 DID URL格式)描述的构成规则。
- **type**：在验证方法中，type属性必须给出，另外type属性必须是一个字符串。type属性对应唯一的hash函数以及签名函数，从而描述了如何使用verificationMehtod中公钥验证签名。
- **controller**：在验证方法中，controller属性必须给出，另外controller属性必须是一个字符串且满足[1.1DID](#1.1 DID格式)描述的构成规则。controller表示被授予拥有更新verificationMethod属性中type属性以及publicKeyHex属性的权利，而DID的masterKey则无权修改。
- **publicKeyHex**：在验证方法中，publicKeyHex属性必须给出，另外publicKeyHex属性必须是一个符合16进制编码的字符串，表示验证方案的压缩公钥。

> **DID文档例子3**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
>     "verificationMethod": [
>        {
>            "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>            "type": "EcdsaSecp256k1VerificationKey2019",
>            "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>            "publicKeyHex": "0x031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>        },
>        {
>            "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1",
>            "type": "EcdsaSecp256k1VerificationKey2019",
>            "controller": "did:memo:b69f63fe2f84d782b134624020802fa49c70f6fad8009c0be1da25229c2be99c",
>            "publicKeyHex": "0x02948b109c8298057c1d798d9a14ed30f33689d8397e2e60f89ef408b08d7f48a1"
>        }
>        ],
>    }
> ```

> **特殊验证方法masterKey**
>
> 在每个DID文档的verificationMethod属性中，必然存在一个特殊的验证方法，其id为`{did}#masterKey`。在例子4中即为`did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey`。该验证方法除了能够在线下验证签名者的身份外，也是线上合约验证签名者是否具有修改DID文档权限的方法，其他验证方法不具备该功能。另外，`masterKey`所对应的验证方法中，controller的`memo-specific-id`以全零表示，这意味着msterKey不受任何具体的DID实体控制，即masterKey是无法修改的。

>**验证方法的controller**
>
>由于密钥无法控制自身，并且无法从DID文档中推断出验证方法的控制器，因此需要在验证方法中明确表达密钥控制器的身份。验证方法的controller仅拥有对单个验证方法中type和publicKeyHex属性进行修改的权限。通常情况下，验证方法controller均为DID实体本身，但可能这两者均不相同。



### 1.3.4 authentication属性

在DID文档中，authentication属性描述了网关或者服务商如何对DID实体的身份进行线下身份认证，通常用于用户登录网关或者执行任何类型的质询-响应协议等。

**authentication**：在DID文档中，authentication是可选的。如果authentication存在，则必须是一组字符串且满足[1.2 DID URL](#1.2 DID URL格式)构成规则。

> **DID文档例子4**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	...
>        "authentication": [
>            "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>            "did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-5"
>        ],
>    }
>    ```



### 1.3.5 assertionMethod属性

在DID文档中，assertionMethod属性通常用于发布DID主体的声明，例如与[可验证凭证](https://www.w3.org/TR/vc-data-mode)结合，发布有关DID主体身份信息的声明（待定）。

**assertionMethod**：在DID文档中，assertionMethod是可选的。如果assertionMethod属性存在，则必须是一组字符串且满足[1.2DID URL](#1.2 DID URL格式)构成规则。

> **DID文档例子5**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	...
>        "assertionMethod": [
>            "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1",
>            "did:memo:6b4d7cdef82c4581f7e2b1d8dc11b124410a1b9ac2f8923f6724da86184dc52f#key-3"
>        ],
>    }
>    ```



### 1.3.6 capabilityDelegation属性

在DID文档中，capability属性用于授权文件管理。例如当用户登录网关后，需要先授予网关对该用户所属文件的访问和管理权限，才能访问网关的相应服务。为了授予网关管理文件的权限，用户需要将网关的特定验证方法（网关指定）添加到capabilityDelegation属性中。网关访问文件时，添加数字签名消息，并由验证者提取验证方法中的公钥验证数字签名。如果验证成功，则调用中有权访问受保护的资源。

**capabilityDelegation**：在DID文档中，capabilityDelegation属性是可选的。如果capabilityDelegation属性存在，则必须是一组字符串且满足[1.2 DID URL](#1.2 DID URL格式)构成规则。

> **DID文档例子6**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	...
>        "capabilityDelegation": [
>            "did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-1",
>            "did:memo:6b4d7cdef82c4581f7e2b1d8dc11b124410a1b9ac2f8923f6724da86184dc52f#key-2"
>        ],
>    }
>    ```



### 1.3.7 recovery属性

在DID文档中，recovery属性用于私钥丢失后恢复密钥（待定）。

**recovery**：在DID文档中，recovery属性是可选的。如果recovery存在，则必须是一组字符串且满足[1.2 DID URL](#1.2 DID URL格式)构成规则。

> **DID文档例子7**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	...
>     "recovery": [
>            "did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-1",
>            "did:memo:6b4d7cdef82c4581f7e2b1d8dc11b124410a1b9ac2f8923f6724da86184dc52f#key-2"
>        ],
>    }
>    ```



## 1.4 DID功能设计

### 1.4.1 创建DID

创建DID的接口如下：

```go
Registe(did, publicKey)
```

在创建DID之前，需要生成did以及publicKey，其流程如下：

- 生成一对公私钥，并生成地址；
- 获取链上随机数或者合约中随机数nonce；
- 根据nonce和地址计算hash并转换成16进制字符串，作为memo-specific-id；
- 在memo-specific-id前添加did:memo生成最终的did；

调用该接口后，会将最终did以及公钥信息添加到交易消息中，同时使用私钥对交易进行签名。合约根据交易中信息，以及默认信息，如：验证方法id，type以及controller，生成并保存初始DID文档


初始DID文档如下：

> **DID文档例子8-1**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     ],
> }
> ```



### 1.4.2 查询DID

**（1）解析DID**

解析DID即通过DID解析DID文档，其接口如下：

```go
Resolve(did)
```

在MEMO DID的合约中，所有的verificationMethod会依次序添加到verificationMethod数组中，因此解析verificationMethod中所有元素，只需遍历verificationMethod数组即可。

其他的属性如：authentication，assertionMehod，capabilityDelegation以及recovery，则会在每次添加操作执行完成后，发出对应的事件，同时合约提供相应的查询接口，用以查询上述属性是否有效。因此，可以通过遍历相应的事件，同时调用查询接口，即可解析MEMO DID所有属性。

至于@context属性，则会在上述解析完成后，默认添加。

**（2）反引用解析DID URL**

反引用解析DID URL，即通过DID URL解析得到验证方法，其接口如下：

```go
Dereference(didUrl)
```

在MEMO DID中，DID URL即表示verificationMethod的ID，可解析为DID以及fragment两部分。因此，可通过DID定位到对应的verificationMthod数组，同时可以通过fragment定位到最终verificationMthod所在的位置。



### 1.4.3 更新DID

对于DID文档的修改，存在如下接口：

- 添加验证方法**addVerificationMethod(did, type, controller, publicKeyHex)**，该接口仅masterKey可以调用；
- 修改验证方法**updateVerificationMethod(methodID, type, publicKeyHex)**，该接口仅<font color="red">`验证方法中controller`</font>可以调用；
- 撤销验证方法**deactivateVerificationMethod(methodID)**，该接口仅masterKey可以调用；
- 添加认证方式**addAuthentication(did, methodID)**，该接口仅masterKey可以调用；
- 撤销认证方式**deactivateAuthentication(did, methodID)**，该接口仅masterKey可以调用；
- 添加用户声明验证方式**addAssertionMethod(did, methodID)**，该接口仅masterKey可以调用；
- 撤销用户声明验证方式**deactivateAssertionMethod(did, methodID)**，该接口仅masterKey可以调用；
- 添加文件代理方**addCapabilityDelegation(did, methodID, expiration)**，该接口仅masterKey可以调用；
- 撤销文件代理方**deactivateCapabilityDelegation(did, methodID)**，该接口仅masterKey可以调用；
- 添加密钥恢复**addrecovery(did, methodID)**，该接口仅masterKey可以调用；
- 撤销密钥恢复**deactivaterecovery(did, methodID)**，该接口仅masterKey可以调用；



我们以一些典型的例子来说明：

**addVerificationMethod(did, type, controller, publicKeyHex)**

- 用户选择did以及controller(默认和did相同，但是可能存在单个用户拥有多个did，设定主did控制其他did和其验证方法)；
- 用户生成一对公私钥，根据生成算法指定type;
- 使用masterKey的私钥签名，并访问合约接口addVerificationMethod(did, type, controller, publicKeyHex)；
  - 合约验证签名是否由did masterKey所签；
  - 合约根据递增的number和did生成methodID；
  - 合约根据methodID，type，controller，publicKeyHex生成验证方法；
  - 合约将验证方法添加到did文档的verificationMethod属性中；

在例子8-1基础上添加`did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1`后文档如下：

> **DID文档例子8-2**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:b69f63fe2f84d782b134624020802fa49c70f6fad8009c0be1da25229c2be99c",
>         "publicKeyHex": "02948b109c8298057c1d798d9a14ed30f33689d8397e2e60f89ef408b08d7f48a1"
>     },
>     ],
> }
> ```



**modifyVerifycationMethod(did, methodIndex, type, publicKeyHex)**

- 用户选择methodID，解析出did以及methodIndex;
- 用户生成一对公私钥，并根据生成算法确定type；
- 用户利用验证方法 controller包含的masterKey对应的私钥签名，并访问合约接口modifyVerifycationMethod(did, methodIndex, type, publicKeyHex)；
  - 合约验证签名是否由methodID中controller所签；
  - 合约修改验证方法中type以及publicKeyHex属性；

在例子8-2基础上更新`did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1`后文档如下：

> **DID文档例子8-3**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:b69f63fe2f84d782b134624020802fa49c70f6fad8009c0be1da25229c2be99c",
>         "publicKeyHex": "02fa46f35cecc57016e13c94ba3b0302c7d14daa482741ca18ede9dd2255aaeeeb"
>     },
>     ],
> }
> ```



**deactivateVerificationMethod(did, methodIndex)**

- 用户选择methodID，解析出did以及methodIndex;
- 用户使用did masterKey签名消息，访问合约接口deactivateVerificationMethod(did, methodIndex)；
  - 合约验证签名是否由did masterKey所签；
  - 合约删除对应的验证方法；

在例子8-3基础上删除`did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1`后文档如下：

> **DID文档例子8-4**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     ],
> }
> ```



**addCapabilityDelegation(did, methodID, expiration)**

- 用户选择did，methodID，并设置过期时间；
- 用户使用masterKey签名消息，并访问合约接口addCapabilityDelegation(did, methodID, expiration)；
  - 合约验证签名是否由did masterKey所签；
  - 合约添加methodID到文档的capabilityDelegation属性中，同时设置到期时间（到期时间对于展示给用户的DID文档来说是透明的，只有读取文档时合约会判断，但不会展示出来）；

在例子8-3基础上添加`did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-1`后文档如下：

> **DID文档例子8-5**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     ],
>     "capabilityDelegation": [
>         "did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-1",
>     ],
> }
> ```

```json
{
	"@context": "https://www.w3.org/ns/did/v1",
	"id": "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96",
	"verifycationMethod": [{
		"id": "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#masterKey",
		"controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000000",
		"type": "EcdsaSecp256k1VerificationKey2019",
		"publicKeyHex": "0x03d21e6c4843fa3f5d019e551131106e2075925b01da2a83dc177879a512eb608f"
    },
    {
    	"id": "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#key-1",
    	"controller": "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96",
    	"type": "EcdsaSecp256k1VerificationKey2019",
    	"publicKeyHex": "0x02d78b20654eb7a5d58d83b25d090a338eff18f0b5f919777c9d894c2e161b4b52"
    }],
    "authentication": [
    	"did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#masterKey"
    ],
    "assertionMethod": [
    	"did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#masterKey"
    ],
    "capabilityDelegation": [
    	"did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#key-1"
    ],
    "recovery": [
    	"did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#masterKey"
    ]
}
```
