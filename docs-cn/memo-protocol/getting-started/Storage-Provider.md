# 启动Provider

## Docker方式启动Provider的步骤

### 第 1 步：Docker 环境准备

安装后确认 Docker 正在运行。

```shell
service docker start
```

### 第 2 步：设置主目录

设置节点主目录：

以 "~/memo_provider" 为例：

```shell
export MEFS_PATH=~/memo_provider
```

设置节点数据存储目录：

以 ~/memo_provider_data 为例：

```shell
export MEFS_DATA=~/memo_provider_data
```

主目录包含配置文件和其他运行节点所需的文件，存储目录是数据存储的位置。

### 第 3 步：拉取镜像（提供者）

```shell
docker pull memoio/mefs-provider:latest
```

### 第 4 步：初始化（创建新钱包）

执行初始化命令，这将生成您的钱包地址并生成配置文件。

```shell
docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest init --password=memoriae
```

参数解释：

--password：输入您的提供者密码，默认为memoriae。

### 第 5 步：检查钱包地址

```shell
docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest wallet default
```

参数解释：

wallet default：获取默认钱包地址

### 第 6 步：充值

启动节点需要 Memo 和 cMemo 两种代币。

获取 cMemo 代币，有一个水龙头：<https://faucet.metamemo.one/>

以下是 MemoChain 信息。

MemoChain 信息

链 RPC：<https://chain.metamemo.one:8501/>

货币名称：CMEMO

链 ID：985

链浏览器：<https://scan.metamemo.one:8080/>

要为您的钱包获取 Memo 代币，您可以从其他拥有足够 Memo 代币的钱包地址转账一些 Memo 代币。提供者至少需要 30 个 Memo 代币。

加入我们的讨论，请使用 Slack 链接：

Slack 链接

### 第 7 步：修改配置文件

```shell
docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest config set --key=contract.version --value=3

docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest config set --key=contract.endPoint --value="https://chain.metamemo.one:8501"

docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest config set --key=contract.roleContract --value="0xbd16029A7126C91ED42E9157dc7BADD2B3a81189"
```

### 第 8 步：启动节点

```shell
docker run -d -p 4001:4001 -v $MEFS_PATH:/root -v $MEFS_DATA:/root/data -e PASSWORD="memoriae" -e PRICE=250000 -e GROUP=3 -e SWARM_PORT=4001 -e DATA_PATH=/root/data --name mefs-provider memoio/mefs-provider:latest
```

请确保您的提供者主目录和密码与前一步相同。
如果有任何部署问题，请加入部署节点讨论，使用 Slack 链接：

[Slack Link](https://join.slack.com/t/memo-nru9073/shared_invite/zt-sruhyryo-suc689Nza3z8boa4JkaLqw)

## 检查运行状态

### 第 1 步：进入 docker 容器

```shell
docker exec -it mefs-provider bash
```

进入容器后，您可以使用 mefs-provider 命令执行操作。

### 第 2 步：检查提供者信息

```shell
mefs-provider info
```

```shell
----------- 信息 -----------

2022-03-23 16:44:19 CST #当前时间

2.1.0-alpha+git.86acc04+2022-03-22.14:17:54CST #mefs-provider 版本信息

----------- 网络信息 -----------

ID: 12D3KooWR74K1v6naGUAjHRrkakxVS88h4X93bMnquoRiEDdLJTx

IP: [/ip4/10.xx.xx.xx/tcp/8003] #当前节点网络信息，8003 是 swarm-port 端口，用于节点通信

类型：私有

声明地址：{12D3KooWR74K1v6naGUAjHRrkakxVS88h4X93bMnquoRiEDdLJTx: [/ip4/10.xx.xx.xx/tcp/8003]}

----------- 同步信息 -----------

状态：true, 插槽：323608, 时间：2022-03-23 16:44:00 CST #请检查您的同步状态是否为 true，如果不是，请检查您的节点网络

同步高度：3363, 远程：3363 #同步状态

挑战时代：23 2022-03-23 11:56:30 CST

----------- 角色信息 -----------

ID：30

类型：提供者

钱包：0x749573E11C18A0f5Eb53248382588e2064E0Af80

余额：999.87 Gwei (交易费), 0 AttoMemo (Erc20), 493586.64 NanoMemo (在 fs)

已存储数据：大小 57647104 字节 (54.98 MiB), 价格 56750000

----------- 群组信息 -----------

EndPoint：http://119.xx.xx.xx:8191 

合约地址：0xCa3C4103bd5679F43eC9E277C2bAf5598f94Fe6D

Fs 地址：0xFB9FF26EB4093aa8fFf762F2dF4E61d3A7532Af9

ID：1 #群组 ID

安全等级：7

大小：109.95 MiB

价格：113500000

守护者：10, 提供者：16, 用户：4

----------- 质押信息 ----------

质押：1.00 Memo, 26.00 Memo (总质押), 26.00 Memo (池中总额) 
```

### 第 3 步：声明

• 当作为提供者节点参与时，您需要执行声明命令（声明公共网络地址）以实现节点间通信；

• 准备好您的公共网络 ip+端口，我将在下面展示；

• 注意：在容器内执行命令；

• mefs-provider info 命令只有在同步信息显示为 true 后才能执行。尽管不执行此操作也可以成功同步，但将无法与其他节点通信。

```shell
mefs-provider net declare /ip4/X.X.X.X/tcp/4007
```

参数解释

X.X.X.X 是您的公共 ip 地址。

端口 4007 是您的公共网络端口，映射端口是主机的端口 4001（-p 4001：启动参数中的第一个端口 4001）。​​

## 检查网络状态

### 获取本地节点网络信息

命令说明：输入命令 net info 查看当前节点的网络 id (cid)、ip 地址和端口。

```shell
mefs-provider net info
```

```shell
网络 ID 12D3Koo2BpPPzk9srHVVU4kkVF1RPJi9nYNgV4e6Yjjd4PGr5q1k, IP [/ip4/10.xx.xx.xx/tcp/18003], 类型：私有
获取节点的网络连接信息

命令说明：输入命令 net peers 查看当前节点的网络连接信息。

```shell
mefs-provider net peers
shell
12D3KooWMrZTqoU8febMxxxxxxxxxCeqLQy1XCU9QcjP1YWAXVi [/ip4/10.xx.xx.xx/tcp/8003]

12D3KooWC2PmhSrU1VexxxxxxxxxtwvQuFZj3vPfjdfebAuJQtc [/ip4/10.xx.xx.xx/tcp/8004]

12D3KooWR74K1v6naGxxxxxxxxxVS88h4X93bMnquoRiEDdLJTx [/ip4/10.xx.xx.xx/tcp/8003]

12D3KooWSzzwJ

7es1xxxxxxxxxPaqei3TUHnNZDmWTELSA7NJXQ [/ip4/10.xx.xx.xx/tcp/18003]

12D3KooWG8PjbbN9xxxxxxxxx2oYFtjeQ8XnqvnEqfrB4AiW7eJ [/ip4/10.xx.xx.xx/tcp/8003]

12D3KooWRjamwQtxxxxxxxxxb44AAXLNy9CB7u1FL2eC5QZekpF [/ip4/1.xx.xx.xx/tcp/24071]

...
```

### 连接到指定节点

命令说明：输入命令 net connect 连接到任何节点；如果您的节点网络有任何问题，请输入命令 net connect 连接到我们的公共节点。

```shell
mefs-provider net connect /ip4/10.2.x.x/tcp/8004/p2p/12D3KooWAykMmqu952ziotQiAYTN6SwfvBd1dsejSSak2jdSwryF
```

## 常见错误及解决办法

### 错误 1

没有链上的交易费

解决方案：

检查节点，cMemo 代币余额和 memo 余额。Memo 代币余额必须满足节点启动的最低金额。

### 错误 2

执行反转：在 180 天内不能取消质押

解决方案：

节点质押金额需要在最后一次质押后 180 天才能提取。

### 错误 3

如果日志报告元数据和状态文件丢失，您可以执行恢复操作。

解决方案：

mefs-provider recover db --path /home/mcloud/provider2/.memo-provider/meta

mefs-provider recover db --path /home/mcloud/provider2/.memo-provider/state
