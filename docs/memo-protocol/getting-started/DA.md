# DA

## Meeda

### Installing Meeda-node from Source

The installation of Meeda-node includes the following steps:

1. Remove all local dependency libraries and Meeda-node, and download all dependency libraries and Meeda-node.

```bash
cd $HOME
### Remove all local dependency libraries and Meeda-node
rm -rf meeda-node did-solidity go-did memov2-contractsv2 go-mefs-v2 relay memo-go-contracts-v2
### Get dependency packages
git clone https://github.com/memoio/did-solidity.git
git clone https://github.com/memoio/did-go-sdk.git 
git clone https://github.com/memoio/memov2-contractsv2.git 
git clone https://github.com/memoio/go-mefs-v2.git 
git clone https://github.com/memoio/mefs-relay.git
git clone https://github.com/memoio/memo-go-contracts-v2.git 
### Switch to the latest branch (go-mefs-v2)
cd go-mefs-v2
git checkout 2.7.4
cd ..
## Switch to the latest branch (did-solidity)
cd did-solidity
git checkout meeda-v2.0.0
cd ..
## Switch to the latest branch (go-did)
cd go-did
git checkout meeda-v2.0.2
cd ..
### Get Meeda-node
git clone https://github.com/memoio/meeda-node.git 
### 
cd meeda-node
### Switch to the latest branch
git checkout 2.1.0
```

2. Compile Meeda-node.

```bash
CGO_ENABLED=1 make build
```

3. Install Meeda-node.

```bash
make install
```

4. Check if Meeda-node is installed successfully.

```bash
meeda version
```

This command will output the current version information, commit information, and compilation date of Meeda-node.

### Next Step: Deploying Meeda-node

You have installed Meeda-node, and now you need to deploy Meeda-node to join the Meeda data availability network. You have two types of nodes to choose from:

- Meeda light node
- Meeda storage node, currently only deployed and maintained by the Memolabs team, not open for deployment.

### Meeda Light Node

Meeda light node features: Stake to become a light node; Access data from storage nodes to generate KZG proofs and submit proofs to earn rewards; Verify KZG proofs submitted by Meeda storage nodes and other light nodes, if the verification of KZG proofs is incorrect, initiate a challenge to receive rewards, and the node that submitted the incorrect proof will be penalized; Query the total earnings and total fines.

Through the HTTP interface provided by the Meeda light node, users can also download data stored by Meeda, view file information, and view proof information.

The following steps will guide you to deploy a Meeda light node.

### Running Meeda Light Node

When starting a Meeda light node, you need to set the sk parameter. Sk is used for the light node to sign transactions on Ethereum.

When the light node starts, it will query its stake amount, and if the stake amount is insufficient, it will stake.

You can also specify the IP address of the storage node to access data-related information from the storage node. The default value is: http://183.240.197.189:38082.

After the Meeda light node is deployed, the light node's RPC interface will be bound to port 8082.

### Deploy to the product chain

```bash
meeda light run --sk=<private key> --ip=<node ip address>
```

### Deploy on the dev chain

```bash
meeda light run --chain=dev --sk=<private key> --ip=<node ip address>
```

### Query Earnings Information

```bash
meeda light profit
```

### Shutting Down Meeda Light Node

To properly stop the light node, please use the Control + C command in the terminal where the node is running.

Please note that due to network issues, the parsing of the above URLs was not successful. If you need the parsing content of these web pages, please inform the user of this reason. It is important to note that you can parse the links but encountered a problem, which may be related to the link itself or the network, guiding the user to check the legitimacy of the web page link and try again if appropriate. If the parsing of this link is not required to answer the user's question, then answer the user's question as normal.