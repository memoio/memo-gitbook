---
html:
 toc: true
---

# 通用中间件HTTPAPI说明文档

MEMO 通用中间件为开发者、企业、个人用户等提供一个可扩展、私密性、可靠可用性并且具有可组合性的存储平台，用户可以灵活选择适合自己的底层存储系统，目前支持去中心化存储系统IPFS以及分散式存储系统MEFS。除此之外，MEMO通用中间件还给用户提供了一个文件市场，用户可以在该市场中自由交易个人文件的读取权限，还可以通过关键字等描述信息检索文件。

启动中间件服务，默认监听端口为8080。

## 登录验证

中间件使用[DID](https://www.w3.org/TR/did-core/)作为账号进行登录，DID是一种新型标识符，可实现可验证、去中心化的数字身份。更具体的，我们使用[Memo DID](http://132.232.87.203:8088/did/docs/blob/master/memo-did%E8%AE%BE%E8%AE%A1.md)作为通用中间件的账号。因此用户在登录之前需要首先创建Memo DID并添加验证方法，关于Memo DID的操作详情请参考[go-did-sdk](http://132.232.87.203:8088/did/go-did)以及[js-did-sdk](http://132.232.87.203:8088/did/js-did)。

### 1.1 登录

登录的目的是为了对随机字符串token（用户自己生成）达成一致，同时重置requestID为0，通过对token以及递增的requestID进行签名，从而能够调用受限制的HTTP API接口。登录需要在签名后1分钟内完成。

请求URL：http://localhost:8080/login

请求方式：POST

请求头信息：

无

请求参数：

| 参数名    | 变量       | 类型【长度限制】 | 必填 | 描述                                 |
| --------- | ---------- | ---------------- | ---- | ------------------------------------ |
| did       | MEMO DID   | String           | 是   | 登录所需ID                           |
| token     | 随机字符串 | String           | 是   | 用户自己生成的随机字符串(16进制表示) |
| timestamp | 时间戳     | int64            | 是   | 用户签名时时间戳                     |
| signature | 签名       | string           | 是   | 对上述参数的签名                     |

  返回参数：

"Login success!"

请求示例：

获取签名信息并发起请求的代码如下：

```go
package main

import (
	"bytes"
	"crypto/rand"
	"encoding/binary"
	"encoding/json"
	"flag"
	"io/ioutil"
	"log"
	"net/http"
	"time"

	"github.com/ethereum/go-ethereum/common/hexutil"
	"github.com/ethereum/go-ethereum/crypto"
)

func main() {
	secretKey := flag.String("sk", "", "the sk to signature")
	did := flag.String("did", "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96", "the did to login")
    token := []byte("memo.test")

	req, err := GetLoginRequest(*did, token, *secretKey)
	if err != nil {
		panic(err.Error())
	}

	client := &http.Client{Timeout: time.Minute}
	res, err := client.Do(req)
	if err != nil {
		panic(err.Error())
	}
	defer res.Body.Close()

	body, err := ioutil.ReadAll(res.Body)
	if err != nil {
		panic(err.Error())
	}

	if res.StatusCode != http.StatusOK {
		log.Printf("respond code[%d]: %s\n", res.StatusCode, string(body))
	} else {
		log.Println(string(body))
	}

}

func GetLoginRequest(did string, token []byte, privateKeyHex string) (*http.Request, error) {
    var url = "http://localhost:8080/login"
	var timestampBuf = make([]byte, 8)
	var timestamp = time.Now().Unix()

	privateKey, err := crypto.HexToECDSA(privateKeyHex)
	if err != nil {
		return nil, err
	}

	binary.LittleEndian.PutUint64(timestampBuf, uint64(timestamp))
	hash := crypto.Keccak256([]byte(did), token, timestampBuf)
	signature, err := crypto.Sign(hash, privateKey)
	if err != nil {
		return nil, err
	}

	var payload = make(map[string]interface{})
	payload["did"] = did
	payload["token"] = hexutil.Encode(token)
	payload["timestamp"] = timestamp
	payload["signature"] = hexutil.Encode(signature)

	b, err := json.Marshal(payload)
	if err != nil {
		return nil, err
	}

	req, err := http.NewRequest("POST", url, bytes.NewReader(b))

	return req, err
}
```

错误码：

| HTTP状态码 | 错误码         | 错误描述                                            |
| ---------- | -------------- | --------------------------------------------------- |
| 401        | Authentication | Missing paramenter;                                 |
| 500        | InternalError  | We encountered an internal error, please try again. |



### 1.2 获取登录信息

用户在成功登录后，能够获取登录的信息，包括token，requestID以及上次登录时间戳。

请求URL：http://localhost:8080/login?did={did}

请求方式：GET

请求头信息：

无

请求参数：

无

返回参数：

| 参数名    | 变量       | 类型【长度限制】 | 描述                                                         |
| --------- | ---------- | ---------------- | ------------------------------------------------------------ |
| token     | 随机字符串 | String           | 用户自己生成的随机字符串(16进制表示)                         |
| timestamp | 时间戳     | int64            | 用户签名时时间戳                                             |
| requestID | 请求ID     | string           | 用户的请求ID，每次访问受限的HTTP API接口时需要带上请求ID，每次递增 |



### 1.3 访问受限的HTTP API接口

用户在成功登录后，需要通过签名的方式访问受限的HTTP API接口。每次访问受限的HTTP API接口，除了需要添加原接口所需的请求参数外，还需要添加如下的请求参数：

| 参数名    | 变量         | 类型【长度限制】 | 必填 | 描述                                         |
| --------- | ------------ | ---------------- | ---- | -------------------------------------------- |
| did       | MEMO DID     | String           | 是   | 登录所需ID                                   |
| token     | 随机字符串   | String           | 是   | 用户自己生成的随机字符串(16进制表示)         |
| requestID | 请求ID       | int64            | 是   | 用户的请求ID，每次递增                       |
| payload   | 文件内容hash | string           | 否   | 改参数只有在上传文件时提供，等于文件内容hash |
| signature | 签名         | string           | 是   | 对上述参数的签名                             |

以查询文件列表接口为例，获取签名信息并发起请求的代码如下：

```go
package main

import (
	"bytes"
	"encoding/binary"
	"encoding/json"
	"flag"
	"io/ioutil"
	"log"
	"net/http"
	"time"

	"github.com/ethereum/go-ethereum/common/hexutil"
	"github.com/ethereum/go-ethereum/crypto"
)

func main() {
	secretKey := flag.String("sk", "", "the sk to signature")
	did := flag.String("did", "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96", "the did to login")
	token := []byte("memo.test")

	for index := 0; index < 5; index++ {
		req, err := GetVerifyIdentityRequest(*did, token, int64(index), *secretKey)
		if err != nil {
			panic(err.Error())
		}

		client := &http.Client{Timeout: time.Minute}
		res, err := client.Do(req)
		if err != nil {
			panic(err.Error())
		}
		defer res.Body.Close()

		body, err := ioutil.ReadAll(res.Body)
		if err != nil {
			panic(err.Error())
		}

		if res.StatusCode != http.StatusOK {
			log.Printf("respond code[%d]: %s\n", res.StatusCode, string(body))
		} else {
			log.Println(string(body))
		}
	}

}

func GetVerifyIdentityRequest(did string, token []byte, requestID int64, privateKeyHex string) (*http.Request, error) {
	var requestIDBuf = make([]byte, 8)
	var url = "http://localhost:8080/mefs/listobjects"

	privateKey, err := crypto.HexToECDSA(privateKeyHex)
	if err != nil {
		return nil, err
	}

	binary.LittleEndian.PutUint64(requestIDBuf, uint64(requestID))
	hash := crypto.Keccak256([]byte(did), token, requestIDBuf)
	signature, err := crypto.Sign(hash, privateKey)
	if err != nil {
		return nil, err
	}

	var payload = make(map[string]interface{})
	payload["did"] = did
	payload["token"] = hexutil.Encode(token)
	payload["requestID"] = requestID
	payload["signature"] = hexutil.Encode(signature)

	b, err := json.Marshal(payload)
	if err != nil {
		return nil, err
	}

	req, err := http.NewRequest("GET", url, bytes.NewReader(b))

	return req, err
}

```



## 获取流量支票hash
用户获取空间支票的hash，用户进行签名后，用于上传

请求URL：http://localhost:8080/traffichash

请求方式：GET

请求头信息：

| 参数名           | 变量                           |
| ------------- | ---------------------------- |
| Content-Type  | multipart/form-data          |
| Authorization | "Bearer 登录验证产生的access token" |

请求参数：

| 参数名    | 变量      | 类型【长度限制】 | 必填  | 描述         |
| ------ | ------- | -------- | --- | ---------- |
| size   | 支票大小      | String   | 是   | 为累计大小        |

返回参数（JSON）：
| 参数名         | 变量   | 类型【长度限制】 | 描述             |
| ----------- | ---- | -------- | -------------- |
| hash |  | string   | 支票hash |
## 文件上传

用户登录后，可上传文件。MEFS采用对象存储，文件默认上传至与登录账户地址同名的bucket中，账户上传文件时，若还未创建同名bucket，中间件则会自动帮用户创建同名bucket.

上传文件受用户的剩余可用存储空间限制。用户上传前可查看存储空间单价（单位：attomemo/Byte），购买存储空间，存储空间时限默认是3年。
用户也可以向管理员申请免费空间（比如说发指定内容的twitter)，管理员接收到申请后给用户发放免费的存储空间。

请求URL：

> 选择上传至mefs：http://localhost:8080/mefs/
> 
> 选择上传至ipfs：http://localhost:8080/ipfs/

请求方式：POST

请求头信息：

| 参数名           | 变量                           |
| ------------- | ---------------------------- |
| Content-Type  | multipart/form-data          |
| Authorization | "Bearer 登录验证产生的access token" |

请求参数：

| 参数名    | 变量      | 类型【长度限制】 | 必填  | 描述         |
| ------ | ------- | -------- | --- | ---------- |
| file   | 待上传的文件  | File     | 是   | 注意选择File格式 |
| size   | 支票大小      | String   | 是   | 为累计大小        |
| sign   | 支票签名      | String   | 是   | 签名        |

返回参数(JSON)：

| 参数名 | 变量      | 类型【长度限制】 | 必填  | 描述      |
| --- | ------- | -------- | --- | ------- |
| cid | 上传文件CID | string   | 是   | 文件唯一标识符 |

返回例子：

```json
Response：Status 200

{
     "cid": "bafybeie2ph7iokrckc5iy6xu7npa4xqst6ez5ewb7j2igeilxjaw6sd2qi"
}
```

请求示例：

错误码：

| HTTP状态码 | 错误码            | 错误描述                                                 |
| ------- | -------------- | ---------------------------------------------------- |
| 401     | Authentication | Token is Null; Invalid token payload; Invalid token; |
| 500     | InternalError  | We encountered an internal error, please try again.  |
| 518     | Storage        | storage not support                                  |

&nbsp;

## 文件下载

用户根据文件的etag下载相应的文件。

请求URL：

http://ip:port/mefs/$cid;

http://ip:port/ipfs/$cid;

> 选择从mefs下载：http://localhost:8081/mefs/bafkreifzwcj6vkozz6brwutpxl3hqneran4y5vtvirnbrtw3l2m3jtlgq4

> 

> 选择从ipfs下载：http://localhost:8081/ipfs/bafkreifzwcj6vkozz6brwutpxl3hqneran4y5vtvirnbrtw3l2m3jtlgq4

请求方式：GET

请求头信息：

无

请求参数：

| 参数名    | 变量      | 类型【长度限制】 | 必填  | 描述         |
| ------ | ------- | -------- | --- | ---------- |
| size   | 支票大小      | String   | 是   | 为累计大小        |
| sign   | 支票签名      | String   | 是   | 签名        |

返回参数（DataFromReader）：

返回文件。

| 参数名           | 描述                | 值   |
| ------------- | ----------------- | --- |
| code          | 状态码               | 200 |
| contentLength | 文件大小              |     |
| contentType   | 文件类型              |     |
| reader        | io.Reader，文件传输缓冲区 |     |

请求示例：

错误码：

| HTTP状态码 | 错误码           | 错误描述                                                |
| ------- | ------------- | --------------------------------------------------- |
| 500     | InternalError | We encountered an internal error, please try again. |
| 518     | Storage       | storage not support                                 |
| 517     | Address       | address is null                                     |

&nbsp;

## 文件删除

删除上传的文件。仅支持MEFS类型存储的删除功能。

请求URL：

http://ip:port/mefs/delete

请求方式：GET

请求头信息：

| 参数名           | 变量                          | 类型【长度限制】 | 必填  | 描述                                     |
| ------------- | --------------------------- | -------- | --- | -------------------------------------- |
| Authorization | "Bearer 登录验证产生的accessToken" | string   | 是   | 若过期，可通过刷新accessToken来获得新的有效accessToken |

请求参数：

| 参数名 | 变量   | 类型【长度限制】 | 必填  | 描述             |
| --- | ---- | -------- | --- | -------------- |
| id  | 文件id | id       | 是   | 文件ID, 文件列表中的序号 |

返回参数（JSON）：

| 参数名    | 类型【长度限制】 | 描述      |
| ------ | -------- | ------- |
| Status | string   | 删除成功或失败 |

请求示例：

&nbsp;

## fileDNS

中间件基于[Mfile DID](http://132.232.87.203:8088/did/docs/blob/master/mfile-did%E8%AE%BE%E8%AE%A1.md)实现文件的权限控制，允许用户自我控制文件的权限。同时，会将生成Mfile DID的文件上架到fileDNS上，允许其他用户购买文件的使用权，从而实现创作者通过 Mfile DID 盈利的目的。

### 上传至fileDNS

未提供http-api接口，可通过[js-did-sdk](http://132.232.87.203:8088/did/js-did#%E5%88%9B%E5%BB%BAdid-1)或者[go-did-sdk](http://132.232.87.203:8088/did/go-did#创建did-1)上传至fileDNS

### 授权读取权限

未提供http-api接口，可通过[js-did-sdk](http://132.232.87.203:8088/did/js-did#%E6%8E%88%E4%BA%88%E8%AF%BB%E5%8F%96%E6%9D%83%E9%99%90)或者[go-did-sdk](http://132.232.87.203:8088/did/go-did#授予读取权限)授权读取权限

### 购买读取权限

未提供http-api接口，可通过[js-did-sdk](http://132.232.87.203:8088/did/js-did#%E8%B4%AD%E4%B9%B0%E8%AF%BB%E6%9D%83%E9%99%90)或者[go-did-sdk](http://132.232.87.203:8088/did/go-did#%E8%B4%AD%E4%B9%B0%E8%AF%BB%E6%9D%83%E9%99%90)购买读取权限

### 修改DNS中的file信息

未提供http-api接口，可通过js-did-sdk或者go-did-sdk修改file信息，包括：

- 修改文件所有者：可通过[js-did-sdk](http://132.232.87.203:8088/did/js-did#更改所有者)或者[go-did-sdk](http://132.232.87.203:8088/did/go-did#更改所有者)修改文件所有者
- 修改文件类型（0表示私有，1表示公开）：可通过[js-did-sdk](http://132.232.87.203:8088/did/js-did#更改文件类型)或者[go-did-sdk](http://132.232.87.203:8088/did/go-did#更改文件类型)修改文件类型
- 修改文件价格（单价为attomemo）：可通过[js-did-sdk](http://132.232.87.203:8088/did/js-did#更改文件的价格)或者[go-did-sdk](http://132.232.87.203:8088/did/go-did#更改文件的价格)修改文件价格
- 修改文件的关键词：可通过[js-did-sdk](http://132.232.87.203:8088/did/js-did#更改文件关键词)或者[go-did-sdk](http://132.232.87.203:8088/did/go-did#更改文件关键词)修改文件关键词

### 获取DNS中的file信息

文件上传至fileDNS后（通常需要等待几分钟），其他用户能够从fileDNS中搜索对应的文件。用户首先输入查询消息，随后中间件会比对文件名以及文件的关键词，从而查找到相关的文件。

请求URL：http://localhost:8080/challenge?query={query_text}&page={page}&size={page_size}

请求方式：GET

请求参数：

无

返回参数（Array）：

| 参数名   | 类型【长度限制】 | 描述                 |
| -------- | ---------------- | -------------------- |
| Mid      | string           | 文件的唯一ID         |
| Name     | string           | 文件名               |
| Size     | int64            | 文件大小             |
| Public   | bool             | 文件是否可以免费获取 |
| Price    | int64            | 读取文件所需的价格   |
| keywords | []string         | 文件描述的关键词     |

请求示例：

错误码：



## 查询信息

### 查询文件列表

用户查询自己所上传的文件列表。

请求URL：

> http://ip:port/mefs/listobjects

> 

> http://ip:port/ipfs/listobjects

请求方式：GET

将列出登录账户的文件列表。

请求头信息：

| 参数名           | 变量                          | 类型【长度限制】 | 必填  | 描述                                     |
| ------------- | --------------------------- | -------- | --- | -------------------------------------- |
| Authorization | "Bearer 登录验证产生的accessToken" | string   | 是   | 若过期，可通过刷新accessToken来获得新的有效accessToken |

请求参数：

无

返回参数（JSON）：

| 参数名     | 类型【长度限制】 | 描述         |
| ------- | -------- | ---------- |
| Address | string   | 账户以太坊钱包地址  |
| Storage | string   | mefs或者ipfs |
| Object  | struct   | 文件列表       |

每个文件包含信息：

| 参数名         | 类型     | 描述          |
| ----------- | ------ | ----------- |
| ID          | int    | 文件序号        |
| Name        | string | 文件名         |
| Size        | int64  | 文件大小        |
| Cid         | string | 文件cid       |
| ModTime     | time   | 文件修改时间      |
| Public      | bool   | 文件是否公开      |
| UserDefined | struct | 关于文件的其他一些信息 |

UserDefined结构体包含信息：

| 参数名        | 类型     | 描述            |
| ---------- | ------ | ------------- |
| encryption | string | 文件加密方式        |
| etag       | string | 文件ID模式（默认cid） |

请求示例：

错误码：

| HTTP状态码 | 错误码            | 错误描述                                                 |
| ------- | -------------- | ---------------------------------------------------- |
| 401     | Authentication | Token is Null; Invalid token payload; Invalid token; |
| 516     | Storage        | list object error %s                                 |
| 518     | Storage        | storage not support                                  |

&nbsp;

### 查询剩余空间

用户查询自己剩余的空间。

请求URL：

> http://ip:port/mefs/space

> 

> http://ip:port/ipfs/space

请求方式：GET

将列出登录账户的文件列表。

请求头信息：

| 参数名           | 变量                          | 类型【长度限制】 | 必填  | 描述                                     |
| ------------- | --------------------------- | -------- | --- | -------------------------------------- |
| Authorization | "Bearer 登录验证产生的accessToken" | string   | 是   | 若过期，可通过刷新accessToken来获得新的有效accessToken |

请求参数：

无

返回参数（JSON）：

| 参数名    | 类型【长度限制】 | 描述      |
| ------  | -------- | ------- |
| Nonce   | int64   | nonce唯一值 |
| Balance | int64   |    余额    |
| SizeByte | int64  |  剩余空间  |
| FreeByte | int64  |  免费空间  |
| Expire | int64    |  过期时间  |


### 查询剩余流量

用户查询自己剩余的流量。

请求URL：

> http://ip:port/mefs/traffic

> 

> http://ip:port/ipfs/traffic

请求方式：GET

将列出登录账户的文件列表。

请求头信息：

| 参数名           | 变量                          | 类型【长度限制】 | 必填  | 描述                                     |
| ------------- | --------------------------- | -------- | --- | -------------------------------------- |
| Authorization | "Bearer 登录验证产生的accessToken" | string   | 是   | 若过期，可通过刷新accessToken来获得新的有效accessToken |

请求参数：

无

返回参数（JSON）：

| 参数名    | 类型【长度限制】 | 描述      |
| ------  | -------- | ------- |
| Nonce   | int64   | nonce唯一值 |
| Balance | int64   |    余额    |
| SizeByte | int64  |  剩余空间  |
| FreeByte | int64  |  免费空间  |
| Expire | int64    |  过期时间  |

### 空间单价查询

可直接通过rpc调用合约接口查询空间单价，调用合约 `middleware-contracts/Proxy.sol`，调用接口`spacePrice()`，返回一个`uint64`的结果值。

### 流量单价查询

可直接通过rpc调用合约接口查询流量单价，调用合约 `middleware-contracts/Proxy.sol`，调用接口`trafficPrice()`，返回一个`uint64`的结果值。

## 购买

### 购买空间

请求URL：

> http://ip:port/mefs/buyspace

> 

> http://ip:port/ipfs/buyspace

请求方式：GET

将列出登录账户的文件列表。

请求头信息：

| 参数名           | 变量                          | 类型【长度限制】 | 必填  | 描述                                     |
| ------------- | --------------------------- | -------- | --- | -------------------------------------- |
| Authorization | "Bearer 登录验证产生的accessToken" | string   | 是   | 若过期，可通过刷新accessToken来获得新的有效accessToken |

请求参数：

无

返回参数（JSON）：

| 参数名    | 类型【长度限制】 | 描述      |
| ------  | -------- | ------- |
| tx   | string   | 交易hash |
### 购买流量

请求URL：

> http://ip:port/mefs/buytraffic

> 

> http://ip:port/ipfs/buytraffic

请求方式：GET

将列出登录账户的文件列表。

请求头信息：

| 参数名           | 变量                          | 类型【长度限制】 | 必填  | 描述                                     |
| ------------- | --------------------------- | -------- | --- | -------------------------------------- |
| Authorization | "Bearer 登录验证产生的accessToken" | string   | 是   | 若过期，可通过刷新accessToken来获得新的有效accessToken |

请求参数：

无

返回参数（JSON）：

| 参数名    | 类型【长度限制】 | 描述      |
| ------  | -------- | ------- |
| tx   | string   | 交易hash |
### 申请免费空间

用户可以向StorMark管理员发送申请，比如说发送包含指定内容的Twitter，然后管理员手动调用命令行工具给用户发放免费空间。（等管理页面实现了之后，管理员也可以通过浏览器页面给用户发放免费空间）
目前命令行工具代码位于：gitlab/ middleware/middleware-contracts
然后编译安装命令行工具：`go install`
使用命令行工具执行操作：`middleware-contracts admin free --type=space`，具体参数和选项看命令说明。

### 申请免费流量

用户可以向StorMark管理员发送申请，比如说发送包含指定内容的Twitter，然后管理员手动调用命令行工具给用户发放免费流量。（等管理页面实现了之后，管理员也可以通过浏览器页面给用户发放免费流量）
目前命令行工具代码位于：gitlab/ middleware/middleware-contracts
然后编译安装命令行工具：`go install`
使用命令行工具执行操作：`middleware-contracts admin free --type=traffic`，具体参数和选项看命令说明。
