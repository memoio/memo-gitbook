# Deploying the Meeda OP-Stack Layer 2

---

date: 2024-11-5

op-contracts: op-contracts/v2.0.0-beta.3

op-node: op-stack(v1.9.4);

op-geth: v1.101411.0

---

Refer to the official docs: [Creating Your Own L2 Rollup Testnet | Optimism Docs](https://docs.optimism.io/builders/chain-operators/tutorials/create-l2-rollup). The following is a step-by-step guide.

## Get the Source

```shell
cd ~/op-layer2
git clone https://github.com/memoio/optimism.git
cd optimism
# Switch to the correct branch (latest contracts; matches current OP Sepolia)
# git checkout op-contracts/v2.0.0-beta.2
git checkout op-contracts/v2.0.0-beta.3
```

## Install Dependencies

```shell
# Check required versions (install versions listed now; adjust as needed later)
./packages/contracts-bedrock/scripts/getting-started/versions.sh

# Install Go
wget -c https://golang.google.cn/dl/go1.21.1.linux-amd64.tar.gz
tar zxvf go1.21.1.linux-amd64.tar.gz -C /usr/local
cd ~
mkdir go
vim ~/.profile
# Append to the end of .profile:
export GOPATH=$HOME/go
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
# Verify
source .profile
go version
# Optional: switch Go proxy
go env -w GOPROXY=https://goproxy.cn,direct

# Install Node.js
wget https://nodejs.org/dist/v20.16.0/node-v20.16.0-linux-x64.tar.xz
tar -xvf node-v20.16.0-linux-x64.tar.xz
mkdir /usr/local/nodejs
mv node-v20.16.0-linux-x64/* /usr/local/nodejs/
# Append to the end of .profile:
export NODE_HOME=/usr/local/nodejs
export PATH=$NODE_HOME/bin:$PATH
# Verify
source .profile
node --version

# Install pnpm
npm install -g pnpm
pnpm --version

# Install Foundry
curl -L https://foundry.paradigm.xyz | bash
# Follow the instructions printed by the command above
source .profile
foundryup
# Verify
forge --version

# Install jq
sudo apt-get install jq
jq --version

# Install direnv
curl -sfL https://direnv.net/install.sh | bash
# Append to the last line of .bashrc:
eval "$(direnv hook bash)"
# Verify
direnv --version

# Install make
sudo apt-get install make

# Install just
curl --proto '=https' --tlsv1.2 -sSf https://just.systems/install.sh | bash -s -- --to /usr/local/
vim ~/.profile
# Append to the end of .profile:
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin:/usr/local/
# Verify
source .profile
just version
```

Then install the remaining dependencies and build the contracts:

```shell
# cd ~/op-layer2/optimism
# pnpm install
# pnpm build
cd ~/op-layer2/optimism/packages/contracts-bedrock
just build
```

> The following is outdated because OP no longer uses pnpm and has switched to just:
> 
> Running `pnpm install` may encounter errors:
> 
>  `ERR_SOCKET_TIMEOUT  request to https://registry.npmjs.org/@react-native/debugger-frontend/-/debugger-frontend-0.73.3.tgz failed, reason: Socket timeout`
> 
> Suggested solutions:
> 
> ```shell
> # Option 1
> # If the network is poor, you may need larger values (add one or more trailing zeros)
> npm config set fetch-retry-mintimeout 20000
> npm config set fetch-retry-maxtimeout 120000
> # To check the default values of these configs
> npm config ls -l
> 
> # Option 2
> # Switch to npmmirror (Taobao mirror)
> npm config set registry http://registry.npmmirror.com
> 
> # Option 3
> # Clear cache
> npm cache clean --force
> 
> # Option 4
> # Clear proxy settings and reboot

## Start op-batcher

Ensure the op-batcher address has at least 1 Sepolia ETH.
Only start `op-batcher` after `op-node` has fully synced. Otherwise, stale batcher timestamps can cause frequent batch submissions due to partial sync.
How to determine `op-node` is basically synced (i.e., safe to start `op-batcher`):
Check the sync timestamp printed in `op-node` logs, or in `op-geth` inspect `eth.getBlockByNumber(eth.blockNumber)` for the latest L2 block and read the timestamp. If the difference between that timestamp and current time is within 5000 seconds, consider it basically synced.

>
> ```
> npm config rm https-proxy
> # Restore registry
> npm config set registry http://registry.npmjs.org/
> ```
> 
> I spent a lot of time on this and had to retry many times. Eventually `pnpm install` succeeded and all dependencies were installed. (I only used Option 1.)

## Set Environment Variables

```shell
cd ~/op-layer2/optimism
# git checkout op-contracts/v2.0.0-beta.2
git checkout op-contracts/v2.0.0-beta.3
cp .envrc.example .envrc
# It will error on first load; ignore for now

# Open .envrc and fill the values. Example:
# RPC requested from Infura; reuses legacy memo-L2 params (see gitlab/middleware/memoda/blob/master/docs/opstack/params.md)
L1_RPC_URL = https://sepolia.infura.io/v3/1b65bb7a429c4970b5f3381ede10f8e7
L1_RPC_KIND = infura

L2_CHAIN_ID = 11155986 # Choose a unique value; each chain must use a different chain_id
```

## Generate Addresses

For testing, you can generate via script:

```shell
./packages/contracts-bedrock/scripts/getting-started/wallets.sh
```

In production, use a combination of an HSM and a hardware wallet.

Here we continue using the legacy memo-L2 parameters:

```shell
# Admin address
export GS_ADMIN_ADDRESS=
export GS_ADMIN_PRIVATE_KEY=

# Batcher address
export GS_BATCHER_ADDRESS=
export GS_BATCHER_PRIVATE_KEY=

# Proposer address
export GS_PROPOSER_ADDRESS=
export GS_PROPOSER_PRIVATE_KEY=

# Sequencer address
export GS_SEQUENCER_ADDRESS=
export GS_SEQUENCER_PRIVATE_KEY=
```

Save these four account addresses and private keys into .envrc.

Fund these accounts. Suggested amounts: admin - 0.5 Sepolia ETH, proposer - 0.2 Sepolia ETH, batcher - 0.1 Sepolia ETH.

## Load Environment Variables

```shell
direnv allow
# On success you'll see similar output:
direnv: loading ~/optimism/.envrc                                                            
direnv: export +DEPLOYMENT_CONTEXT +ETHERSCAN_API_KEY +GS_ADMIN_ADDRESS +GS_ADMIN_PRIVATE_KEY +GS_BATCHER_ADDRESS +GS_BATCHER_PRIVATE_KEY +GS_PROPOSER_ADDRESS +GS_PROPOSER_PRIVATE_KEY +GS_SEQUENCER_ADDRESS +GS_SEQUENCER_PRIVATE_KEY +IMPL_SALT +L1_RPC_KIND +L1_RPC_URL +PRIVATE_KEY +TENDERLY_PROJECT +TENDERLY_USERNAME
```

## Configure the Network

```shell
cd packages/contracts-bedrock
# Generate the config file
./scripts/getting-started/config.sh
# A file named getting-started.json will be generated under packages/contracts-bedrock/deploy-config

vim deploy-config/getting-started.json

# Edit getting-started.json:
## Change the following values
"batchInboxAddress": "0xff00000000000000000000000000000011155986" # Random; usually suffix matches chain ID
# "l2OutputOracleSubmissionInterval": 120 # Default 120: how often to submit output root to L1, 120 * L2BlockTime
"eip1559Denominator": 250
# "faultGameGenesisOutputRoot": "0x0000000000000000000000000000000000000000000000000000000000000000",

## Add the following
# "l2GenesisFjordTimeOffset": "0x0", # Activate Fjord version
# "L2OutputOracleStartingBlockNumber": 0,
"gasPriceOracleBaseFeeScalar": 7663,
"gasPriceOracleBlobBaseFeeScalar": 0,
"useFaultProofs": false, 
"disputeGameFinalityDelaySeconds": 600,
"proofMaturityDelaySeconds": 600,
"respectedGameType": 0,
"useAltDA": true,
"daCommitmentType": "GenericCommitment",
"daChallengeWindow": 300,
"daResolveWindow": 300,
```

In production, l2ChainID should be unique and added to [ethereum-lists/chains](https://github.com/ethereum-lists/chains).

When `useAltDA` is true, the DataAvailabilityChallenge contract is deployed, enabling challenges against AltDA commitments. This is sometimes called Plasma, but current Plasma only supports keccak256 commitments and is incompatible with Meeda. Meeda natively supports challenge and proof, so skipping Plasma does not reduce security.

When `useFaultProofs` is true, fault proofs are enabled and `OptimismPortal2.sol` is used instead of `OptimismPortal.sol`. Without fault proofs, a single `op-proposer` periodically submits `outputRoot` to L1 (centralized and trust-heavy). Fault proofs decentralize submission by allowing anyone to propose outputs and by introducing `op-challenger` to verify/challenge.

> With the above settings, frequent L2 reorgs were observed. op-node log: `t=2024-08-21T02:27:27+0000 lvl=warn msg="L2 reorg: existing unsafe block does not match derived attributes from L1" err="random field does not match. expected: 0x6cc733d89ec1fb3314d3b0ddbb1ccdb1d0f1ff8a7125b68a7c1395bd9a30d753. got: 0xe88014e2c77b43a9f0c9ac092eb01a8f366a305b1367af08e3a67b711a6fc7eb" ...`
> 
> Root cause: mismatch between L1-derived attributes and locally cached unsafe block. Two inputs go to L1: proposer output roots and batcher commitments to Meeda. Logs indicate the sequencer used a stale origin block `random` when producing unsafe blocks. No fix yet; redeploy with latest versions and observe.

## Deploy L1 Contracts

Note: Check the `base fee` on [Etherscan](https://sepolia.etherscan.io/blocks?p=1) and deploy when lower to save ETH.

Deploying all L1 contracts consumes about 72,525,043 gas. At ~3.18 gwei this costs ~0.231 ETH.

```shell
cd packages/contracts-bedrock/

# avoid version conflicts
forge cache clean
forge clean

DEPLOYMENT_OUTFILE=deployments/artifact.json DEPLOY_CONFIG_PATH=deploy-config/getting-started.json forge script scripts/deploy/Deploy.s.sol:Deploy --private-key $GS_ADMIN_PRIVATE_KEY --broadcast --rpc-url $L1_RPC_URL --slow
```

After deployment, addresses are saved in `deployments/artifact.json`. Example:

```shell
{
  "AddressManager": "0x4a37bD1DcFcba8c3e0c97CF071bf7997fBcE5613",
  "AnchorStateRegistry": "0x18541C871C2BF2A9f0d246062d6D4e7afE6D0AEd",
  "AnchorStateRegistryProxy": "0x088469Df3Ff7ac004A8BF069E585756109237Be7",
  "DelayedWETH": "0xB76b967599Fbd76c0d551bAF36dc071bFD94229C",
  "DelayedWETHProxy": "0x8b51160dbc460eE6c58a14383b584D931F2bb45B",
  "DisputeGameFactory": "0xBb5026207010148f62bFFa08c74e91f572630a43",
  "DisputeGameFactoryProxy": "0xD90D54685c4536D506a202E40bb211a3a5047696",
  "L1CrossDomainMessenger": "0x4ACe5179eeAc2Fa68FFc5A506eE75eC3BD14A44f",
  "L1CrossDomainMessengerProxy": "0xbe5f9dEFa43f52b482a91a8D3bbc1c8EA987c31E",
  "L1ERC721Bridge": "0xc01837b4953e7Bd72eF12419788a735f1f73B4F2",
  "L1ERC721BridgeProxy": "0x68E194834390A76FC2C449310746248401692f68",
  "L1StandardBridge": "0xA1aC0fCd63848e1E15BB3D14eC3987de9Cf0d6AE",
  "L1StandardBridgeProxy": "0x2bb08a800A11Ef9F1a0EB4A47Ab1caf045315246",
  "L2OutputOracle": "0x1e53298ab6eA5168610d44e291746dA2a4D1e9F5",
  "L2OutputOracleProxy": "0x0A3EeE49381171A960D3451DF42420B5A5a4dd49",
  "Mips": "0x4D0c1177d29c185E656cd1D7D1045EcAC50d3E1a",
  "OptimismMintableERC20Factory": "0x59a9A6528200B7948f7c70BabB05Dd050fcdE38a",
  "OptimismMintableERC20FactoryProxy": "0x867fb6DF3f49336d12476d85f5B8373371b53409",
  "OptimismPortal": "0xEB285cD027A4F8634e75F60bc7FD9FaE02B367Bb",
  "OptimismPortal2": "0xd2Cc50812436cC9bFD330AC47022576E72dE7b6b",
  "OptimismPortalProxy": "0x3a4B63B8EC1596f88f7318b9cE6cE70BA10D70EB",
  "PreimageOracle": "0xb3c54B72e0a95eECb5f804502a4eb34f3F93bC4D",
  "ProtocolVersions": "0x7951Dcb817d20997ab3e3f086Eaa97994b02D95C",
  "ProtocolVersionsProxy": "0xfb4D5062DBBfB419499e78Dc77dd1d47E776B604",
  "ProxyAdmin": "0x231DD86fe5AEF6EF67472677DfD0485c75dF779C",
  "SafeProxyFactory": "0xa6B71E26C5e0845f74c812102Ca7114b6a896AB2",
  "SafeSingleton": "0xd9Db270c1B5E3Bd161E8c8503c55cEABeE709552",
  "SuperchainConfig": "0x46D9445Bb910b6ec6EB731187b45D4449456f0e2",
  "SuperchainConfigProxy": "0x15654d11311BC099A74685fD18BAC940806C5718",
  "SystemConfig": "0x75a70C720aC2eeCE199d996Af43863665f6b89A7",
  "SystemConfigProxy": "0xB0ac0F88C41CbBF97B472AB55f17c0CE31adF140",
  "SystemOwnerSafe": "0x5cD8E9A4e956bE8d72fc449EA3a4d7401c24dEc8"
}
```

If fault proofs are enabled, while running `Deploy.s.sol`:

1. For `AnchorStateRegistry`, deploy it (`deployAnchorStateRegistry()`) but do not initialize it (`initializeAnchorStateRegistry()`).

2. Build genesis, start op-geth and op-node, then run `cast rpc optimism_outputAtBlock <hex_block_number> --rpc-url http://localhost:9545` to get the genesis outputRoot.

3. Set `faultGameGenesisOutputRoot` in `getting-started.json` to the value from step 2, then run `initializeAnchorStateRegistry()`.

## Generate l2-allocs File

```shell
CONTRACT_ADDRESSES_PATH=deployments/artifact.json DEPLOY_CONFIG_PATH=deploy-config/getting-started.json STATE_DUMP_PATH=deployments/l2-allocs.json forge script scripts/L2Genesis.s.sol:L2Genesis --sig 'runWithStateDump()' --chain-id $L2_CHAIN_ID
```

The resulting l2-allocs file is ~9.1MB and includes code bytecode and addresses for predeployed L2 contracts.

## Build op-geth

```shell
cd ~/op-layer2
git clone https://github.com/ethereum-optimism/op-geth.git
cd op-geth
# Latest production versions
# git checkout v1.101315.3
# git checkout v1.101408.0 (encountered errors)
git checkout v1.101411.0 # If make geth fails, change `go 1.22` to `go 1.22.0` in go.mod
make geth
```

## Build op Components

```shell
cd ~/op-layer2/optimism
git checkout op-stack/v1.9.4
make op-node op-batcher op-proposer
```

If fault proofs are enabled, also build:

`make op-challenger op-program cannon cannon-prestate`

## Generate L2 Configuration Files

```shell
# Switch to production branches (op-node, op-batcher, op-proposer)
# Based on https://github.com/ethereum-optimism/optimism/tree/v1.9.3
# git checkout op-stack/v1.9.3
git checkout op-stack/v1.9.4

# Generate L2 genesis files
cd optimism/op-node
go run cmd/main.go genesis l2 --deploy-config ../packages/contracts-bedrock/deploy-config/getting-started.json --l1-deployments ../packages/contracts-bedrock/deployments/artifact.json --outfile.l2 genesis.json --outfile.rollup rollup.json --l1-rpc $L1_RPC_URL --l2-allocs ../packages/contracts-bedrock/deployments/l2-allocs.json

# Create authentication between op-geth and op-node
openssl rand -hex 32 > jwt.txt

cp genesis.json ~/op-layer2/op-geth
cp jwt.txt ~/op-layer2/op-geth
```

Example rollup.json:

```json
{
  "genesis": {
    "l1": {
      "hash": "0x3a283b2f2801ee5c283fed93312146c84918547974806823c7ea624149f56fce",
      "number": 6547525
    },
    "l2": {
      "hash": "0x98a2dc4ece56f7068c69b54192b004867cdad61b0de23bfbed708c5cbb27f0fe",
      "number": 0
    },
    "l2_time": 1724303088,
    "system_config": {
      "batcherAddr": "0x0d2897e7e3ad18df4a0571a7bacb3ffe417d3b06",
      "overhead": "0x0000000000000000000000000000000000000000000000000000000000000000",
      "scalar": "0x00000000000000000000000000000000000000000000000000000000000f4240",
      "gasLimit": 30000000
    }
  },
  "block_time": 2,
  "max_sequencer_drift": 600,
  "seq_window_size": 3600, // 3600 * 12s
  "channel_timeout": 300, 
  "channel_timeout_granite": 0,
  "l1_chain_id": 11155111,
  "l2_chain_id": 11155986,
  "regolith_time": 0,
  "canyon_time": 0,
  "delta_time": 0,
  "ecotone_time": 0,
  "fjord_time": 0,
  "batch_inbox_address": "0xff00000000000000000000000000000011155986",
  "deposit_contract_address": "0x3a4b63b8ec1596f88f7318b9ce6ce70ba10d70eb",
  "l1_system_config_address": "0xb0ac0f88c41cbbf97b472ab55f17c0ce31adf140",
  "protocol_versions_address": "0x0000000000000000000000000000000000000000",
  "da_challenge_contract_address": "0x0000000000000000000000000000000000000000"
}
```

Field explanations:

```shell
"max_sequencer_drift": How far the L2 timestamp can differ from the actual L1 timestamp.
"seq_window_size": Maximum number of L1 blocks that a Sequencer can wait to incorporate the information in a specific L1 block. For example, if the window is 10 then the information in L1 block n must be incorporated by L1 block n+10.
"channel_timeout": Maximum number of L1 blocks that a transaction channel frame can be considered valid. A transaction channel frame is a chunk of a compressed batch of transactions. After the timeout, the frame is dropped.
```

In simple terms: `seq_window_size` means that data for epoch n (L1 block n) must be uploaded to L1 within `seq_window_size` L1 blocks. For example, at epoch 66100, L2 produced blocks 908111–908116; if `seq_window_size` is 300, the blob containing those transactions must be submitted before L1 block 66400.

`genesis.l1.number` comes from the System contract `startBlock` set during deployment.

When `op-node` starts, it syncs from that L1 block (~1s per block). If much time has passed since deployment, sync will take longer.

> Optional/advanced:
> You can manually set `l1.hash` and `l1.number` in `rollup.json` to a recent finalized block to reduce sync time. Find a finalized block (≈20 minutes old) on Etherscan and run:
> ```shell
> cast block 6497179 -r https://sepolia.infura.io/v3/1b65bb7a429c4970b5f3381ede10f8e7
> ```
> Use the output to update `l1.number`, `l1.hash`, and `l2_time` (the L1 block timestamp). Also update `genesis.json` `timestamp` to `l2_time` in hex (`cast to-hex 1723619988`).

## Initialize op-geth

```shell
cd ~/op-layer2/op-geth

mkdir datadir
# build/bin/geth init --datadir=datadir genesis.json # 1013xx版本
build/bin/geth init --datadir=datadir --state.scheme=hash genesis.json
```

## Start op-geth

```shell
cd ~/op-layer2/op-geth

vim start.sh
# Paste the following content
./build/bin/geth \
  --datadir ./datadir \
  --http \
  --http.port=8545 \
  --http.corsdomain="*" \
  --http.vhosts="*" \
  --http.addr=0.0.0.0 \
  --http.api=web3,debug,eth,txpool,net,engine \
  --ws \
  --ws.addr=0.0.0.0 \
  --ws.port=8546 \
  --ws.origins="*" \
  --ws.api=debug,eth,txpool,net,engine \
  --syncmode=full \
  --gcmode=archive \
  --nodiscover \
  --maxpeers=0 \
  --networkid=11155986 \
  --authrpc.vhosts="*" \
  --authrpc.addr=0.0.0.0 \
  --authrpc.port=8551 \
  --authrpc.jwtsecret=./jwt.txt \
  --rollup.disabletxpoolgossip=true \
  --verbosity=3

# Run in background
chmod +x start.sh
nohup ./start.sh > out.log 2>&1 &
```

When shutting down the Rollup, stop components in reverse of startup order.

To safely stop op-geth: use `CTRL-C` in the foreground, or `kill -INT <pid>` (signal 2) if running in the background. Abrupt stops risk database corruption.

Important: Always run op-geth and op-node in a 1:1 mapping. Do not attach multiple op-geth to one op-node or vice versa.

## Start op-node

```shell
cd ~/op-layer2/optimism/op-node
vim start.sh
# Paste the following content; adjust --l1 and --l1.rpckind.
# If using blobs, set --l1.beacon=<http endpoint of L1 Beacon-node>
./bin/op-node \
  --l2=http://localhost:8551 \
  --l2.jwt-secret=./jwt.txt \
  --sequencer.enabled \
  --rollup.config=./rollup.json \
  --rpc.addr=0.0.0.0 \
  --rpc.port=8547 \
  --p2p.no-discovery \
  --rpc.enable-admin \
  --p2p.sequencer.key=$GS_SEQUENCER_PRIVATE_KEY \
  --l1=xxx \
  --l1.rpckind=standard \
  --altda.da-server=http://127.0.0.1:8082 \
  --altda.enabled=true \
  --altda.da-service=true \
  --altda.verify-on-read=false \
  --l1.beacon.ignore=true \
  --log.level=debug \
  --l1.trustrpc=true

# Run in background
chmod +x start.sh
nohup ./start.sh > out.log 2>&1 &
```

`altda.da-server` should be the Meeda light node HTTP endpoint.

Use `tail out.log | grep 'Advancing bq origin'` to view latest L2 sync progress.

Or check sync status:

```shell
cast rpc optimism_syncStatus --rpc-url http://localhost:8547|jq
```

```shell
# Current block
cast block-number --rpc-url http://localhost:8545
# Current safe block
cast block-number --rpc-url http://localhost:8545 safe
# Current finalized block
cast block-number --rpc-url http://localhost:8545 finalized
# Block number delta
expr 90057 - 65711
```

View chain state:

```shell
cast rpc optimism_outputAtBlock <hex_block_number> --rpc-url http://localhost:8547|jq
# Convert block number to hex
cast to-hex 23
```

> On startup, op-node syncs from L1. Using an Infura URL hit rate limits and produced errors like `Engine temporary error ... 429 Too Many Requests ... batch item count exceeded`. Lowering `l1.rpc-max-batch-size` (e.g., 10 from default 20) still failed.
>
> Switching to an unrestricted L1 RPC (from chainlist) fixed it:
> ```shell
> --l1=wss://ethereum-sepolia-rpc.publicnode.com \
> --l1.rpckind=standard \
> ```

To stop op-node, first run `cast rpc admin_stopSequencer --rpc-url http://localhost:8547`, then stop the process.

## Start op-batcher

Ensure the op-batcher address has at least 1 Sepolia ETH.

Only start `op-batcher` after `op-node` has fully synced. Otherwise, partial sync can cause stale batcher timestamps and frequent submissions.

How to tell `op-node` is basically synced (OK to start `op-batcher`):

Check the sync timestamp printed in `op-node` logs, or in `op-geth` run `eth.getBlockByNumber(eth.blockNumber)` to inspect the latest L2 block timestamp. If it’s within 5000 seconds of current time, consider it basically synced.

```shell
cd ~/op-layer2/optimism/op-batcher
vim start.sh
# Paste the following content
./bin/op-batcher \
  --l2-eth-rpc=http://localhost:8545 \
  --rollup-rpc=http://localhost:8547 \
  --poll-interval=6s \
  --sub-safety-margin=6 \
  --num-confirmations=1 \
  --rpc.addr=0.0.0.0 \
  --rpc.port=8548 \
  --rpc.enable-admin \
  --max-channel-duration=0 \
  --max-l1-tx-size-bytes=30000 \
  --resubmission-timeout=48s \
  --max-pending-tx=1 \
  --metrics.enabled=true \
  --metrics.port=7301 \
  --l1-eth-rpc=xxx \
  --private-key=$GS_BATCHER_PRIVATE_KEY \
  --altda.da-server=http://127.0.0.1:8082 \
  --altda.enabled=true \
  --altda.verify-on-read=false \
  --altda.da-service=true

# Run in background
chmod +x start.sh
nohup ./start.sh > out.log 2>&1 &
```

`max-channel-duration` specifies how many L1 blocks between submissions to L1. If the channel fills earlier, it submits immediately; otherwise it submits again after 1500 blocks (`max-channel-duration=1500`, 1500 * 12s = 5h). If set to 0, it falls back to `sequencerWindowSize` in getting-started.json (also measured in L1 blocks), default 3600 (≈12h). This is the validity window for the op-batcher channel. If within 5 hours the batch size reaches `--max-l1-tx-size-bytes=1040000` (~0.99MB), it will also submit. We use Meeda as DA here, so data is uploaded to Meeda.

> However, after running we observed frequent L2 reorgs. We suspected the batcher’s L1 submission interval was too long, so we changed `--max-l1-tx-size-bytes=256000`.

To **stop** op-batcher:

```shell
curl -d '{"id":0,"jsonrpc":"2.0","method":"admin_stopBatcher","params":[]}' \
    -H "Content-Type: application/json" http://localhost:8548 | jq
```

Wait until `Batch Submitter stopped` appears in logs, then stop the process.

If you sent the stop command but the process hasn’t exited yet, you can restart op-batcher with:

```shell
curl -d '{"id":0,"jsonrpc":"2.0","method":"admin_startBatcher","params":[]}' \
    -H "Content-Type: application/json" http://localhost:8548 | jq   
```

The metrics port defaults to 7300 and conflicts with op-node; change it to 7301.

Currently, Meeda supports a maximum data size of 127B * (32768 / 4) = 1016KB. Therefore, set `max-l1-tx-size-bytes` to 1040384 or lower; otherwise uploads will fail.

## Start op-proposer

```shell
cd ~/op-layer2/optimism/op-proposer
vim start.sh
# Paste the following content
./bin/op-proposer \
  --poll-interval=12s \
  --rpc.port=8560 \
  --rollup-rpc=http://localhost:8547 \
  --l2oo-address=(L2OutputOracleProxy) \ # fill in L2OutputOracleProxy address
  --private-key=$GS_PROPOSER_PRIVATE_KEY \
  --l1-eth-rpc=xxx \
  --log.level=DEBUG \
  --allow-non-finalized=true

# Run in background
chmod +x start.sh
nohup ./start.sh > out.log 2>&1 &
```

op-proposer polls L2 blocks every `--poll-interval=12s`, and every `"l2OutputOracleSubmissionInterval": 120` (i.e., 120 L2 blocks ≈ 4 minutes) sends a `proposeL2Output` transaction to `L2OutputOracleProxy`.

See the `L2OutputOracleProxy` contract on Etherscan to view the latest submitted L2 block and the next target block: [L2OutputOracleProxy on Etherscan](https://sepolia.etherscan.io/address/0x372D2aE7950BDdE3Fdb5bEc57aBc470A6800f50d#readProxyContract).

If fault proofs are enabled, remove `--l2oo-address` in start.sh and add:

```sh
--proposal-interval=1200s \
--game-factory-address=(cat ../packages/contracts-bedrock/deployments/artifact.json | jq -r .DisputeGameFactoryProxy) \
```

To **stop** op-proposer, simply kill the op-proposer process. Shut down components in the reverse order of startup.

> If fault proofs are enabled, also start op-challenger. Example script:
> 
> ```shell
> cd ~/op-layer2/optimism/op-challenger
> vim start.sh
> # Paste the following content
> ./bin/op-challenger \
>         --trace-type cannon \
>         --l1-eth-rpc=http://10.10.100.51:8545 \
>         --l1-beacon=http://10.10.100.51:8545 \
>         --l2-eth-rpc=http://localhost:8545 \
>         --rollup-rpc=http://localhost:8547 \
>         --selective-claim-resolution \
>         --private-key=$GS_ADMIN_PRIVATE_KEY \
>         --game-factory-address=0xee2Ab3a7812d8a6665c8b1E72ed75C6c0aAf3beF \
>         --datadir=./data/ \
>         --cannon-rollup-config=../op-node/rollup.json \
>         --cannon-l2-genesis=../op-node/genesis.json \
>         --cannon-server=../op-program/bin/op-program \
>         --cannon-bin=../cannon/bin/cannon \
>         --cannon-prestate=./op-program/bin/prestate.json
> 
> # Run in background
> chmod +x start.sh
> nohup ./start.sh > out.log 2>&1 &
> ```

## Tools

Query L1 balance

```shell
 cast balance -r https://sepolia.infura.io/v3/1b65bb7a429c4970b5f3381ede10f8e7 0x57b9c7853646F991Fd8A441b252a70bc001ae10A
```

Query contract address

```shell
cat ../packages/contracts-bedrock/deployments/artifact.json | jq -r .L2OutputOracleProxy
```

Transfer

```shell
cast send 0x0d2897e7e3ad18df4a0571a7bacb3ffe417d3b06 --value 1gwei --rpc-url http://localhost:8545 --private-key 0x
```

Check account L2 balance

```shell
cast balance -r http://localhost:8545 0x0d2897e7e3ad18df4a0571a7bacb3ffe417d3b06
```

## Deposit (Stake)

ETH on L2 is moved from L1 via deposits. Sending a small amount from L1 to L2 is a deposit.

```shell
cd ~/optimism/packages/contracts-bedrock
# View L1StandardBridgeProxy contract address
cat deployments/artifact.json | jq -r .L1StandardBridgeProxy
# Send an L1 transaction transferring value to L1StandardBridgeProxy
cast send 0x532cBdbB5EB75aABb5a161939C5DEb96AdE11664 --value 1gwei --rpc-url https://sepolia.infura.io/v3/1b65bb7a429c4970b5f3381ede10f8e7 --private-key PK
```

The balance is ultimately held by `OptimismPortalProxy`.

```shell
cat deployments/artifact.json | jq -r .OptimismPortalProxy
cast balance -r https://sepolia.infura.io/v3/1b65bb7a429c4970b5f3381ede10f8e7 0xDff5D1EC955FD25ad050DE84A8e1FCbEBd771897
```

Your L1 balance decreases immediately; after about 1 minute, the L2 balance increases.

Check L2 balance:

```shell
cast balance -r http://localhost:8545 0x57b9c7853646F991Fd8A441b252a70bc001ae10A
```

Check L1 balance:

```shell
cast balance -r https://sepolia.infura.io/v3/41c82b2df53e455f9d38171972103fd8 0x57b9c7853646F991Fd8A441b252a70bc001ae10A
```

## Withdraw

Moving ETH from an L2 account back to L1 is a withdrawal.

Withdrawals involve three transaction phases:

### Withdrawal Initiating Transaction

Call `L2CrossDomainMessenger.sendMessage` on L2 with: target (L1 destination), message (L1 calldata), minGasLimit (minimum gas for finalizing).

Building these parameters is non-trivial; use the Optimism SDK or the bridge UI.

Then wait for op-proposer to include the initiating transaction in an output root submitted to L1. Each new output proposal commits the `sendMessage` mapping state, proving a pending withdrawal.

If `useFaultProofs=false`, the proposer submits to `L2OutputOracle` every `l2OutputOracleSubmissionInterval` (default 120 L2 blocks ≈ 4 minutes), stored on-chain as `submissionInterval`.

If `useFaultProofs=true`, the proposer submits to `DisputeGameFactory` every `--proposal-interval`.

### Withdrawal Proving Transaction

Once an output root containing the `MessagePassed` event is on L1, you must prove the withdrawal exists.

Off-chain steps:
1. Fetch withdrawal calldata from L2 (target, nonce, value, etc.).
2. Find the outputRoot index that includes the initiating event.
3. Get proofs from L2: latest state root and block hash, account state root, and storage proof for the message.
  Tip: `eth_getProof` returns state proofs given account, storage slot (message hash), and block.
4. Call `OptimismPortal.proveWithdrawalTransaction()` on L1 with the proof.

On-chain steps:
1. `OptimismPortal.proveWithdrawalTransaction()` runs sanity checks.
2. Verifies the message hash exists in `L2ToL1MessagePasser.sentMessages` and has not been proven.
3. Records outputRoot, timestamp, and index in `provenWithdrawals` and emits an event.

Then wait for the finalization period (`finalizationPeriodSeconds`) before finalizing.

If fault proofs are enabled (`OptimismPortal2`), proofs are recorded in `faultDisputeGame` and may be challenged; after maturity, `op-challenger` resolves via `resolveClaim()`/`resolve()`.

### Withdrawal Finalizing Transaction

After the challenge period, finalize the withdrawal on L1.

## Using the Optimism SDK

The Optimism SDK makes deposits, withdrawals, and sending transactions straightforward. A withdrawal moves ETH from an L2 account back to L1.

```shell
cd ~/op-layer2/optimism/packages/contracts-bedrock
# View contract addresses
cat deployments/artifact.json
```

Create a demo project to use the Optimism SDK:

```shell
mkdir op-sample-project
cd op-sample-project
```

```shell
pnpm init
```

```shell
pnpm add @eth-optimism/sdk
# pnpm add viem
```

```shell
pnpm add ethers@^5
```

Set the private key environment variable

```shell
export PRIVATE_KEY=
```

```shell
node
```

```shell
const optimism = require("@eth-optimism/sdk")
# const optimism = require("viem")
const ethers = require("ethers")

const l1Provider = new ethers.providers.StaticJsonRpcProvider("https://sepolia.infura.io/v3/41c82b2df53e455f9d38171972103fd8")
const l2Provider = new ethers.providers.StaticJsonRpcProvider("http://localhost:8545")

// Note: Use the proxy addresses for these contracts; otherwise later transactions may fail to estimate gas.
const AddressManager = ''
const L1CrossDomainMessenger = ''
const L1StandardBridge = '' 
const OptimismPortal = ''
const L2OutputOracle = ''
// If fault proofs are enabled, also set the following contract addresses.
const OptimismPortal2 = ''
const DisputeGameFactory = ''

const l1ChainId = await l1Provider.getNetwork().then(network => network.chainId)
const l2ChainId = await l2Provider.getNetwork().then(network => network.chainId)

const privateKey = process.env.PRIVATE_KEY
const l1Wallet = new ethers.Wallet(privateKey, l1Provider)
const l2Wallet = new ethers.Wallet(privateKey, l2Provider)
```

Now create a `CrossChainMessenger` via the OP SDK to easily handle cross-domain messages between L1 and L2.

```shell
const messenger = new optimism.CrossChainMessenger({
  l1SignerOrProvider: l1Wallet,
  l2SignerOrProvider: l2Wallet,
  l1ChainId,
  l2ChainId,

  // This is the only part that differs from natively included chains.
  contracts: {
    l1: {
      AddressManager,
      L1CrossDomainMessenger,
      L1StandardBridge,
      OptimismPortal,
      L2OutputOracle,

      // Need to be set to zero for this version of the SDK.
      StateCommitmentChain: ethers.constants.AddressZero,
      CanonicalTransactionChain: ethers.constants.AddressZero,
      BondManager: ethers.constants.AddressZero,

      OptimismPortal2,
      DisputeGameFactory,
    }
  }
})
```

**Check Balances:**

```shell
console.log((await l1Wallet.getBalance()).toString())
console.log((await l2Wallet.getBalance()).toString())
```

**Deposit ETH:**

```shell
tx = await messenger.depositETH(ethers.utils.parseEther('0.001'),{gasLimit:200000})
await tx.wait()
await messenger.waitForMessageStatus(tx.hash, optimism.MessageStatus.RELAYED)
```

**Withdraw ETH:**

Initiate the withdrawal on L2

```shell
const withdrawal = await messenger.withdrawETH(ethers.utils.parseEther('0.000716224933581508'),{gasLimit:200000})
await withdrawal.wait()
```

At this point, your L2 balance has decreased but the L1 wallet balance is unchanged. Wait until the withdrawal is ready to be proved to the L1 bridge contract.

```shell
await messenger.waitForMessageStatus(withdrawal.hash, optimism.MessageStatus.READY_TO_PROVE)
```

Send an L1 transaction to prove the L2 withdrawal.

```shell
await messenger.proveMessage(withdrawal.hash)
```

The final step is to relay the withdrawal on L1, which only occurs after the fault-proof window. In this test chain it is set to 12 seconds.

```shell
await messenger.waitForMessageStatus(withdrawal.hash, optimism.MessageStatus.READY_FOR_RELAY)
```

Relay.

```shell
await messenger.finalizeMessage(withdrawal.hash)
```

```shell
await messenger.waitForMessageStatus(withdrawal.hash, optimism.MessageStatus.RELAYED)
```

Full example:

```js
// Import Optimism SDK and ethers.js
const optimism = require('@eth-optimism/sdk');
const ethers = require('ethers');

// L1 and L2 RPC providers (L1 via Infura, L2 is local)
const l1Provider = new ethers.providers.StaticJsonRpcProvider('xxx');
const l2Provider = new ethers.providers.StaticJsonRpcProvider('http://localhost:8545');

// Contract addresses; ensure these are valid for your network
const AddressManager = '0x4909Be147744840a682b61d70D16c75F27D62D6E'
const L1CrossDomainMessenger = '0x8F7D33CEB5eF303aC1A55b371bE33bE734Ef2C20'
const L1StandardBridge = '0x0980bE2A855Ee9a3BCB4c4F31792E5d73E94EaBD'
const OptimismPortal = '0x723f7e6E724D7Ce6A5305Eb436051C85dDA0A9dE'
const L2OutputOracle = '0xc397b74290D36e29940Cd6e77A47a7067D92e2Df'
const OptimismPortal2 = ''
const DisputeGameFactory = ''

// Get L1 and L2 chain IDs
async function getChainIds() {
    const l1ChainId = await l1Provider.getNetwork().then(network => network.chainId);
    const l2ChainId = await l2Provider.getNetwork().then(network => network.chainId);
    return { l1ChainId, l2ChainId };
}

// Read private key from environment; ensure PRIVATE_KEY is set
const privateKey = process.env.PRIVATE_KEY;
if (!privateKey) {
  console.error('Please set the PRIVATE_KEY environment variable!');
  process.exit(1);
}

// Create L1 and L2 wallets
const l1Wallet = new ethers.Wallet(privateKey, l1Provider);
const l2Wallet = new ethers.Wallet(privateKey, l2Provider);

// CrossChainMessenger factory
async function createMessenger() {
    const { l1ChainId, l2ChainId } = await getChainIds();

    const messenger = new optimism.CrossChainMessenger({
        l1SignerOrProvider: l1Wallet,
        l2SignerOrProvider: l2Wallet,
        l1ChainId,
        l2ChainId,
        contracts: {
            l1: {
                AddressManager,
                L1CrossDomainMessenger,
                L1StandardBridge,
                OptimismPortal,
                L2OutputOracle,
                OptimismPortal2,
                DisputeGameFactory,
                StateCommitmentChain: ethers.constants.AddressZero,
                CanonicalTransactionChain: ethers.constants.AddressZero,
                BondManager: ethers.constants.AddressZero,
            }
        }
    });
    return messenger;
}

// Check balances
async function checkBalances() {
    const l1Balance = await l1Wallet.getBalance();
    const l2Balance = await l2Wallet.getBalance();
    console.log(`L1 balance: ${ethers.utils.formatEther(l1Balance)} ETH`);
    console.log(`L2 balance: ${ethers.utils.formatEther(l2Balance)} ETH`);
}

// Deposit ETH from L1 to L2
async function depositETH(messenger) {
    console.log('Starting deposit of ETH to L2...');
    const tx = await messenger.depositETH(ethers.utils.parseEther('0.02'), { gasLimit: 2000000 });
    await tx.wait();
    console.log('ETH deposit succeeded!');
    await messenger.waitForMessageStatus(tx.hash, optimism.MessageStatus.RELAYED);
    console.log('Message relayed to L2.');
}

// Withdraw ETH from L2 to L1

async function withdrawETH(messenger) {
    console.log('Starting withdrawal from L2 to L1...');
    const withdrawal = await messenger.withdrawETH(ethers.utils.parseEther('0.001'), { gasLimit: 200000 });
    await withdrawal.wait();
    console.log('L2 withdrawal TX mined, waiting to be ready to prove...');
    console.log('Withdrawal TX hash: ', withdrawal.hash); // Print TX hash
    const customHash = withdrawal.hash;
//    console.log('Custom withdrawal tx hash:', customHash); // Print custom withdrawal tx hash
    console.log('Waiting for READY_TO_PROVE...');
    try {
        await messenger.waitForMessageStatus(customHash, optimism.MessageStatus.READY_TO_PROVE);
    } catch (error) {
        console.error('Error while getting message status:', error);
    }

    console.log('Proving L2 withdrawal transaction...');
    await messenger.proveMessage(customHash);
    console.log('Proof complete, waiting to relay...');
    await messenger.waitForMessageStatus(customHash, optimism.MessageStatus.READY_FOR_RELAY);

    console.log('Relaying transaction...');
    await messenger.finalizeMessage(customHash);
    await messenger.waitForMessageStatus(withdrawal.hash, optimism.MessageStatus.RELAYED)
    console.log('Withdrawal relayed successfully!');
}

// Main
async function main() {
    try {
        const messenger = await createMessenger();
        await checkBalances();
//        await depositETH(messenger);
        await withdrawETH(messenger);
        await checkBalances();
    } catch (error) {
        console.error('Error during execution:', error);
    }
}

// Run main
main();
```
