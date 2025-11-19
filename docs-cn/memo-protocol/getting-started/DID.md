# DID

[did spec](https://github.com/memoio/memo-did-spec)

Memolabs关于DID的设计文档：[did-design](https://github.com/memoio/did-docs)

## DID Server

DID Server是由Memolabs部署维护的DID服务，查看详细API介绍：[swagger api](https://didapi.memolabs.org/swagger/index.html)

## go-did

go-did是由golang语言实现的SDK：[did-go-sdk](https://github.com/memoio/go-did-sdk)，可以支持创建，查询，删除以及修改DID。

### 安装go-did

1. 下载go-did以及依赖包

```bash
cd $HOME
### 删除所有的本地依赖库和go-did
rm -rf did-solidity go-did memov2-contractsv2
### 获取依赖包
git clone https://github.com/memoio/did-solidity.git
git clone https://github.com/memoio/memov2-contractsv2.git
## 将依赖包切换到最新分支(did-solidity)
cd did-solidity
git checkout meeda-v2.0.0
cd ..
### 获取go-did
git clone https://github.com/memoio/go-did-sdk.git
### 切换到最新分支
cd go-did
git checkout meeda-v2.0.2
cd ..
```

2. 更新项目的本地依赖（go.mod）

```
replace (
	github.com/memoio/contractsv2 => ../memov2-contractsv2
	github.com/memoio/did-solidity => ../did-solidity
	github.com/memoio/go-did => ../go-did
)
```

### 使用go-did

可根据[使用文档](https://github.com/memoio/go-did-sdk/blob/master/README_cn.md)中说明进行操作。

## js-did

js-did是由JavaScript语言实现的SDK，可以支持创建，查询，删除以及修改DID。

### 安装js-did

需要您从[did-js-sdk](https://github.com/memoio/js-did-sdk#)上下载

## 使用js-did

可根据[使用文档](https://github.com/memoio/js-did-sdk/blob/master/README_cn.md)中说明进行操作。
