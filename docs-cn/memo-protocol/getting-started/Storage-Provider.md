# Start Provider

## Docker

### Step by step

### Step 1: Docker Environment Preparation

Confirm Docker is running after install.

```shell
service docker start
```

### Step 2: Set up the home directory

Set node home directory:

Using "~/memo_provider" as an example:

```shell
export MEFS_PATH=~/memo_provider
```

Set node data storage directory:

~/memo_provider_data as an example:

```shell
export MEFS_DATA=~/memo_provider_data
```

The home directory contains config file and other files for running a node, the storage directory is where data is stored.

### Step 3: Pull image (provider)

```shell
docker pull memoio/mefs-provider:latest
```

### Step 4: Initialization（Create new wallet）

Execute the initialize command, which will generate your wallet address and generate a configuration file.

```shell
docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest init --password=memoriae
```

Explanation of arguments:

--password： Enter your provider password, the default is memoriae.

### Step 5: Check wallet address

```shell
docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest wallet default
```

Explanation of arguments:

wallet default: Get the default wallet address

### Step 6: Top up

Starting node needs both the Memo and cMemo token.

To get the cMemo token,  there is one faucet,  <https://faucet.metamemo.one/>

This is the MemoChain information.  

Memochain information

Chain RPC: <https://chain.metamemo.one:8501/>

Currency name: CMEMO

Chain ID: 985

Chain browser: <https://scan.metamemo.one:8080/>

To get Memo Tokens for your wallet, you can transfer some Memo Tokens from other wallet address which has enough Memo Tokens. The provider needs minimum 30 Memo Tokens.

Join our discussing with Slack Link:

[Slack Link](https://join.slack.com/t/memo-nru9073/shared_invite/zt-sruhyryo-suc689Nza3z8boa4JkaLqw)

### Step 7: Modify the configuration file

```shell
docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest config set --key=contract.version --value=3

docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest config set --key=contract.endPoint --value="https://chain.metamemo.one:8501"

docker run --rm -v $MEFS_PATH:/root --entrypoint mefs-provider memoio/mefs-provider:latest config set --key=contract.roleContract --value="0xbd16029A7126C91ED42E9157dc7BADD2B3a81189"
```

### Step 8: Start node

```shell
docker run -d -p 4001:4001 -v $MEFS_PATH:/root -v $MEFS_DATA:/root/data -e PASSWORD="memoriae" -e PRICE=250000 -e GROUP=3 -e SWARM_PORT=4001 -e DATA_PATH=/root/data --name mefs-provider memoio/mefs-provider:latest
```

- Please make sure your provider home directory and password are the same as in the previous step.

If there is any deploy issue.  Please join the deploy-node discussing with Slack Link:

[Slack Link](https://join.slack.com/t/memo-nru9073/shared_invite/zt-sruhyryo-suc689Nza3z8boa4JkaLqw)

## Checking the running status

### Step 1: Enter the docker container

```shell
docker exec -it mefs-provider bash
```

- After entering the container, you can use the mefs-provider command to perform operations.

### Step 2: Check provider information

```shell
mefs-provider info
```

```shell
----------- Information -----------

2022-03-23 16:44:19 CST #Current time

2.1.0-alpha+git.86acc04+2022-03-22.14:17:54CST #mefs-provider version information

----------- Network Information -----------

ID: 12D3KooWR74K1v6naGUAjHRrkakxVS88h4X93bMnquoRiEDdLJTx

IP: [/ip4/10.xx.xx.xx/tcp/8003] #The current node network information, 8003 is the swarm-port port, used for node communication

Type: Private

Declared Address: {12D3KooWR74K1v6naGUAjHRrkakxVS88h4X93bMnquoRiEDdLJTx: [/ip4/10.xx.xx.xx/tcp/8003]}

----------- Sync Information -----------

Status: true, Slot: 323608, Time: 2022-03-23 16:44:00 CST #Please check if your sync status is true, if it is false, please check your node network

Height Synced: 3363, Remote: 3363 #sync status

Challenge Epoch: 23 2022-03-23 11:56:30 CST

----------- Role Information -----------

ID: 30

Type: Provider

Wallet: 0x749573E11C18A0f5Eb53248382588e2064E0Af80

Balance: 999.87 Gwei (tx fee), 0 AttoMemo (Erc20), 493586.64 NanoMemo (in fs)

Data Stored: size 57647104 byte (54.98 MiB), price 56750000

----------- Group Information -----------

EndPoint: http://119.xx.xx.xx:8191

Contract Address: 0xCa3C4103bd5679F43eC9E277C2bAf5598f94Fe6D

Fs Address: 0xFB9FF26EB4093aa8fFf762F2dF4E61d3A7532Af9

ID: 1 #group id

Security Level: 7

Size: 109.95 MiB

Price: 113500000

Keepers: 10, Providers: 16, Users: 4

----------- Pledge Information ----------

Pledge: 1.00 Memo, 26.00 Memo (total pledge), 26.00 Memo (total in pool) 
```

### Step 3: Declare

• When participating as a provider node, you need to execute the declare command (declare the public network address) for communication between nodes;

• Get your public network ip+port ready, I will show you below;

• Note: Execute the command in the container;

• The command mefs-provider info can only be executed after the sync information displays as true. Although the synchronization can be successful without doing so, it will not be able to communicate with other nodes.

```shell
mefs-provider net declare /ip4/X.X.X.X/tcp/4007
```

**Parameter explanation**

X.X.X.X is your public ip address.

Port 4007 is your public network port, and the mapped port is the host's port 4001 (-p 4001: the first port 4001 of the boot parameter).​

## Check network status

**Get local node network information**

Command description: Enter command net info to view the network id (cid), ip address and port of the current node.

```shell
mefs-provider net info
```

```shell
Network ID 12D3Koo2BpPPzk9srHVVU4kkVF1RPJi9nYNgV4e6Yjjd4PGr5q1k, IP [/ip4/10.xx.xx.xx/tcp/18003], Type: Private 
```

**Get the network connection information of the node**

Command description: Enter command net peers to view the network connection information of the current node.

```shell
mefs-provider net peers
```

```shell
12D3KooWMrZTqoU8febMxxxxxxxxxCeqLQy1XCU9QcjP1YWAXVi [/ip4/10.xx.xx.xx/tcp/8003]

12D3KooWC2PmhSrU1VexxxxxxxxxtwvQuFZj3vPfjdfebAuJQtc [/ip4/10.xx.xx.xx/tcp/8004]

12D3KooWR74K1v6naGxxxxxxxxxVS88h4X93bMnquoRiEDdLJTx [/ip4/10.xx.xx.xx/tcp/8003]

12D3KooWSzzwJ7es1xxxxxxxxxPaqei3TUHnNZDmWTELSA7NJXQ [/ip4/10.xx.xx.xx/tcp/18003]

12D3KooWG8PjbbN9xxxxxxxxx2oYFtjeQ8XnqvnEqfrB4AiW7eJ [/ip4/10.xx.xx.xx/tcp/8003]

12D3KooWRjamwQtxxxxxxxxxb44AAXLNy9CB7u1FL2eC5QZekpF [/ip4/1.xx.xx.xx/tcp/24071]

... 
```

**Connect to a specified node**

Command description: Enter command net connect to connect to any node; if there is any problem with your node network, please enter command net connect to connect to our public node.

```shell
mefs-provider net connect /ip4/10.2.x.x/tcp/8004/p2p/12D3KooWAykMmqu952ziotQiAYTN6SwfvBd1dsejSSak2jdSwryF
```

## COMMON MISTAKES

### ERROR 1

not have tx fee on chain

Solution:

Check the node, both of the cMemo token balance, and the memo balance. The Memo token balance must meet the minimum amount for node startup.

### ERROR 2

execution reversed: can't unpledge during 180d

Solution:

The node pledge amount needs to be withdrawn 180 days after the last pledge.

### ERROR 3

If the log reports that the meta and state files are missing, you can perform the Recover operation.

Solution:

mefs-provider recover db --path /home/mcloud/provider2/.memo-provider/meta

mefs-provider recover db --path /home/mcloud/provider2/.memo-provider/state

## 