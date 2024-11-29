# DID

## go-did

go-did是由golang语言实现的SDK，可以支持创建，查询，删除以及修改DID。

### 安装go-did

1. 下载go-did以及依赖包

```bash
cd $HOME
### 删除所有的本地依赖库和meeda-node
rm -rf did-solidity go-did memov2-contractsv2
### 获取依赖包
git clone http://132.232.87.203:8088/did/did-solidity.git
git clone http://132.232.87.203:8088/508dev/memov2-contractsv2.git
## 将依赖包切换到最新分支(did-solidity)
cd did-solidity
git checkout meeda-v2.0.0
cd ..
### 获取go-did
git clone http://132.232.87.203:8088/did/go-did.git
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

可根据[使用文档](http://132.232.87.203:8088/did/go-did/blob/master/README.md)中说明进行操作。

## js-did

go-did是由JavaScript语言实现的SDK，可以支持创建，查询，删除以及修改DID。

### 安装js-did

需要您从[gitlab](http://132.232.87.203:8088/did/js-did)上下载

## 使用js-did

可根据[使用文档](http://132.232.87.203:8088/did/js-did/blob/master/README.md)中说明进行操作。