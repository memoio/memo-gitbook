# DA

## Meeda

### Meeda-node

#### 通过源码安装meeda-node

安装meeda-node包括以下几个步骤：

1. 删除所有的本地依赖库以及meeda-node，并下载所有的依赖库以及meeda-node。

   ```bash
   cd $HOME
   ### 删除所有的本地依赖库和meeda-node
   rm -rf meeda-node did-solidity go-did memov2-contractsv2 go-mefs-v2 relay memo-go-contracts-v2
   ### 获取依赖包
   git clone http://132.232.87.203:8088/did/did-solidity.git
   git clone http://132.232.87.203:8088/did/go-did.git
   git clone http://132.232.87.203:8088/508dev/memov2-contractsv2.git
   git clone http://132.232.87.203:8088/508dev/go-mefs-v2.git
   git clone http://132.232.87.203:8088/508dev/relay.git
   git clone http://132.232.87.203:8088/508dev/memo-go-contracts-v2.git
   ### 将依赖包切换到最新分支(go-mefs-v2)
   cd go-mefs-v2
   git checkout 2.7.4
   cd ..
   ## 将依赖包切换到最新分支(did-solidity)
   cd did-solidity
   git checkout meeda-v2.0.0
   cd ..
   ## 将依赖包切换到最新分支(go-did)
   cd go-did
   git checkout meeda-v2.0.2
   cd ..
   ### 获取meeda-node
   git clone http://132.232.87.203:8088/middleware/meeda-node.git
   ### 
   cd meeda-node
   ### 切换到最新分支
   git checkout 2.1.0
   ```

2. 编译meeda-node。

   ```bash
   CGO_ENABLED=1 make build
   ```

3. 安装meeda-node。

   ```bash
   make install
   ```

4. 检查meeda-node是否安装成功。

   ```bash
   meeda version
   ```

 该命令会输出当前meeda-node的版本信息，commit信息以及编译日期。

#### 下一步：部署meeda-node

您已经安装完成meeda-node，现在您需要部署meeda-node从而接入meeda的数据可用性网络。您有以下两种类型的节点类型可供选择：

- meeda轻节点
- meeda存储节点，目前仅由memolabs团队部署维护，不开放部署。

### Meeda轻节点

Meeda轻节点功能：质押成为轻节点；从存储节点访问数据从而生成KZG证明，并提交证明，从而获得收益；验证Meeda存储节点和其他轻节点提交的KZG证明，若验证KZG证明有误，则发起挑战，从而获得奖励，提交错误证明的节点受到惩罚；查询历史总收益额和总罚款。

通过Meeda轻节点提供的http接口，用户还可以下载Meeda存储的数据、查看文件相关信息、查看证明信息。

以下步骤将引导您部署Meeda轻节点。

#### 运行meeda轻节点

启动meeda轻节点时需要设置sk参数。sk用于轻节点签署以太坊上的交易。
轻节点启动时会查询其质押额，如果质押额不足，将会进行质押。
还可以指定存储节点的ip地址，用于从存储节点访问数据相关信息。默认值为：http://183.240.197.189:38082。

meeda轻节点部署完成后，轻节点的rpc接口将绑定到8082端口。

##### 部署到product链上

> ```bash
> meeda light run --sk=<private key> --ip=<node ip address>
> ```

##### 部署在dev链上

>```bash
>meeda light run --chain=dev --sk=<private key> --ip=<node ip address>
>```

#### 查询收益信息

>```bash
>meeda light profit
>```

#### 关闭meeda轻节点

为了正常停止轻节点，请在节点运行的终端中使用`Control + C`命令。
