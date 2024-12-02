# DID

## go-did

go-did is an SDK implemented in the Golang language, which supports the creation, querying, deletion, and modification of DIDs (Decentralized Identifiers).

### Installing go-did

1. Download go-did and its dependencies

bash

```bash
cd $HOME
### Delete all local dependency libraries and go-did
rm -rf did-solidity go-did memov2-contractsv2
### Get the dependency packages
git clone http://132.232.87.203:8088/did/did-solidity.git
git clone http://132.232.87.203:8088/508dev/memov2-contractsv2.git
## Switch to the latest branch (did-solidity)
cd did-solidity
git checkout meeda-v2.0.0
cd ..
### Get go-did
git clone http://132.232.87.203:8088/did/go-did.git
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

You can follow the instructions in the [usage documentation](http://132.232.87.203:8088/did/go-did/blob/master/README.md).

## js-did

js-did is an SDK implemented in the JavaScript language, which supports the creation, querying, deletion, and modification of DIDs (Decentralized Identifiers).

### Installing js-did

You need to download it from [gitlab](http://132.232.87.203:8088/did/js-did).

## Using js-did

You can follow the instructions in the [usage documentation](http://132.232.87.203:8088/did/js-did/blob/master/README.md).