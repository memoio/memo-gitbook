# DID

[DID Spec](https://github.com/memoio/memo-did-spec)

MEMOLabs DID design document: [did-design](https://github.com/memoio/did-docs)

## DID Server

The DID Server is a DID service deployed and maintained by MEMOLabs. For detailed API documentation: [Swagger API](https://didapi.memolabs.org/swagger/index.html)

## go-did

go-did is an SDK implemented in the Golang language, which supports the creation, querying, deletion, and modification of DIDs (Decentralized Identifiers).

[did-go-sdk](https://github.com/memoio/did-go-sdk)

### Installing go-did

1. Download go-did and its dependencies

bash

```bash
cd $HOME
### Delete all local dependency libraries and go-did
rm -rf did-solidity go-did memov2-contractsv2
### Get the dependency packages
git clone https://github.com/memoio/did-solidity.git
git clone https://github.com/memoio/memov2-contractsv2.git
## Switch to the latest branch (did-solidity)
cd did-solidity
git checkout meeda-v2.0.0
cd ..
### Get go-did
git clone https://github.com/memoio/did-go-sdk.git
### Switch to the latest branch
cd go-did
git checkout meeda-v2.0.2
cd ..
```

1. Update the project's local dependencies (go.mod)

```text
replace (
	github.com/memoio/contractsv2 => ../memov2-contractsv2
	github.com/memoio/did-solidity => ../did-solidity
	github.com/memoio/go-did => ../go-did
)
```

### Using go-did

You can follow the instructions in the [usage documentation](https://github.com/memoio/did-go-sdk/blob/master/README.md).

## js-did

js-did is an SDK implemented in the JavaScript language, which supports the creation, querying, deletion, and modification of DIDs (Decentralized Identifiers).

### Installing js-did

You need to download it from:

[did-js-sdk](https://github.com/memoio/did-js-sdk#)

## Using js-did

You can follow the instructions in the [usage documentation](https://github.com/memoio/did-js-sdk/blob/master/README.md).
