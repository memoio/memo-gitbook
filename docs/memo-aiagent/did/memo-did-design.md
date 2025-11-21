# 1. MEMO DID Design

## 1.1 DID Format

The composition of MEMO DID is as follows:

> **DID Composition**
>
> ```http
> did:memo:<memo-specific-id>
> ```

An example of a DID is as follows:

> **DID Example**
>
> ```http
> did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e
> ```

Here, `<memo-specific-id>` = `hex(hash(address, nonce))`, where `<address>` is an Ethereum address and `<nonce>` is the transaction nonce initiated by `<address>`.

## 1.2 DID URL Format

The composition of a MEMO DID URL is as follows:

> **DID URL Composition**
>
> ```http
> did:memo:<memo-specific-id> path [ ? <query> ] [ # <fragment> ]
> ```

Some examples of DID URLs are as follows:

> **DID URL Example 1**
>
> ```http
> did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey
> ```

> **DID URL Example 2**
>
> ```http
> did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1
> ```

Supported paths:

- None at present

Supported queries:

- None at present

Supported fragments:

- `#masterKey`: Specifies the master public key.
- `#key-<n>`: Specifies other public keys.

## 1.3 Core Attributes of DID Documents

**DID Document**

| Attribute Name         | Required | Representation Method                                      | Purpose                          |
|------------------------|----------|------------------------------------------------------------|----------------------------------|
| id                     | Yes      | A string conforming to the [DID](#1.1 DID Format) structure | User ID                          |
| verificationMethod     | Yes      | A set of verification methods conforming to the [verificationMethod](#verificationMethod) structure | A set of public keys for verification |
| authentication         | No       | A set of strings conforming to the [DID URL](#1.2 DID URL Format) structure | Authentication on behalf of the DID |
| assertionMethod        | No       | A set of strings conforming to the [DID URL](#1.2 DID URL Format) structure | Issuing verifiable claims on behalf of the DID |
| capabilityDelegation   | No       | A set of strings conforming to the [DID URL](#1.2 DID URL Format) structure | Delegating management of files under the DID |
| recovery               | No       | A set of strings conforming to the [DID URL](#1.2 DID URL Format) structure | Key recovery in case of key loss |

**<a name="verificationMethod">Verification Method</a>**

| Attribute Name         | Required | Representation Method                                      | Purpose                          |
|------------------------|----------|------------------------------------------------------------|----------------------------------|
| id                     | Yes      | A string conforming to the [DID URL](#1.2 DID URL Format) structure | Identifier for the verification method |
| controller             | Yes      | A string conforming to the [DID](#1.1 DID Format) structure | Owner of the verification method |
| type                   | Yes      | A string                                                   | Type of the verification method  |
| publicKeyHex           | Yes      | A string in hexadecimal format                             | Public key information in hexadecimal format |

### 1.3.1 id Attribute

In a DID document, the `id` attribute represents the DID identifier corresponding to the DID document.

**id**: In a DID document, the `id` attribute must be present and must be at the top level of the document. The `id` attribute must be a string and conform to the structure described in [1.1 DID](#1.1 DID Format).

> **DID Document Example 1**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
>  	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> }
> ```

### 1.3.3 verificationMethod Attribute

In a DID document, the `verificationMethod` attribute describes a set of methods for verifying proofs, typically represented by public keys. Public keys can be used to verify whether a signature was signed by the corresponding private key, thereby confirming the identity or authority of the signer. The `verificationMethod` attribute is commonly used for interactions between two DID entities offline to achieve authentication or authorization functions.

**verificationMethod**: In a DID document, the `verificationMethod` attribute is mandatory and must include at least the master verification method (`masterKey`). Additionally, the `verificationMethod` attribute must be a set of verification methods conforming to the described structure, and it must include the following attributes:

- **id**: In the verification method, the `id` attribute must be present and must be a string conforming to the [1.2 DID URL](#1.2 DID URL Format) structure.
- **type**: In the verification method, the `type` attribute must be present and must be a string. The `type` attribute corresponds to a unique hash function and signature function, thereby describing how to use the public key in the `verificationMethod` to verify the signature.
- **controller**: In the verification method, the `controller` attribute must be present and must be a string conforming to the [1.1 DID](#1.1 DID Format) structure. The `controller` indicates the entity granted the right to update the `type` and `publicKeyHex` attributes in the `verificationMethod`. The DID's `masterKey` does not have the authority to modify these.
- **publicKeyHex**: In the verification method, the `publicKeyHex` attribute must be present and must be a string in hexadecimal format, representing the compressed public key of the verification scheme.

> **DID Document Example 3**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
>     "verificationMethod": [
>        {
>            "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>            "type": "EcdsaSecp256k1VerificationKey2019",
>            "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>            "publicKeyHex": "0x031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>        },
>        {
>            "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1",
>            "type": "EcdsaSecp256k1VerificationKey2019",
>            "controller": "did:memo:b69f63fe2f84d782b134624020802fa49c70f6fad8009c0be1da25229c2be99c",
>            "publicKeyHex": "0x02948b109c8298057c1d798d9a14ed30f33689d8397e2e60f89ef408b08d7f48a1"
>        }
>        ],
>    }
> ```

> **Special Verification Method masterKey**
>
> In each DID document's `verificationMethod` attribute, there is a special verification method with an `id` of `{did}#masterKey`. For example, in Example 4, it is `did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey`. This verification method not only verifies the identity of the signer offline but also serves as the method for the smart contract to verify whether the signer has the authority to modify the DID document. Other verification methods do not have this functionality. Additionally, the `controller` of the `masterKey` has a `memo-specific-id` represented by all zeros, indicating that the `masterKey` is not controlled by any specific DID entity, meaning the `masterKey` is immutable.

> **Verification Method Controller**
>
> Since keys cannot control themselves and the controller of a verification method cannot be inferred from the DID document, the identity of the key controller must be explicitly stated in the verification method. The controller of a verification method only has the authority to modify the `type` and `publicKeyHex` attributes within a single verification method. Typically, the controller of a verification method is the DID entity itself, but it may not always be the case.

### 1.3.4 authentication Attribute

In a DID document, the `authentication` attribute describes how gateways or service providers authenticate the identity of a DID entity offline, typically used for user login to gateways or for any type of challenge-response protocol.

**authentication**: In a DID document, the `authentication` attribute is optional. If present, it must be a set of strings conforming to the [1.2 DID URL](#1.2 DID URL Format) structure.

> **DID Document Example 4**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	...
>        "authentication": [
>            "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>            "did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-5"
>        ],
>    }
> ```

### 1.3.5 assertionMethod Attribute

In a DID document, the `assertionMethod` attribute is typically used for issuing claims about the DID subject, such as combining with [Verifiable Credentials](https://www.w3.org/TR/vc-data-model) to issue claims about the identity information of the DID subject (pending).

**assertionMethod**: In a DID document, the `assertionMethod` attribute is optional. If present, it must be a set of strings conforming to the [1.2 DID URL](#1.2 DID URL Format) structure.

> **DID Document Example 5**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	...
>        "assertionMethod": [
>            "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1",
>            "did:memo:6b4d7cdef82c4581f7e2b1d8dc11b124410a1b9ac2f8923f6724da86184dc52f#key-3"
>        ],
>    }
> ```

### 1.3.6 capabilityDelegation Attribute

In a DID document, the `capability` attribute is used for authorizing file management. For example, when a user logs into a gateway, they must first grant the gateway access and management permissions for their files in order to use the gateway's services. To grant the gateway permission to manage files, the user must add a specific verification method (designated by the gateway) to the `capabilityDelegation` attribute. When the gateway accesses the files, it adds a digital signature to the message, which is then verified by the verifier using the public key from the verification method. If the verification is successful, the caller is authorized to access the protected resource.

**capabilityDelegation**: In a DID document, the `capabilityDelegation` attribute is optional. If present, it must be a set of strings conforming to the [1.2 DID URL](#1.2 DID URL Format) structure.

> **DID Document Example 6**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	...
>        "capabilityDelegation": [
>            "did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-1",
>            "did:memo:6b4d7cdef82c4581f7e2b1d8dc11b124410a1b9ac2f8923f6724da86184dc52f#key-2"
>        ],
>    }
> ```

### 1.3.7 recovery Attribute

In a DID document, the `recovery` attribute is used for key recovery in case of private key loss (pending).

**recovery**: In a DID document, the `recovery` attribute is optional. If present, it must be a set of strings conforming to the [1.2 DID URL](#1.2 DID URL Format) structure.

> **DID Document Example 7**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	...
>     "recovery": [
>            "did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-1",
>            "did:memo:6b4d7cdef82c4581f7e2b1d8dc11b124410a1b9ac2f8923f6724da86184dc52f#key-2"
>        ],
>    }
> ```

## 1.4 DID Functional Design

### 1.4.1 Creating a DID

The interface for creating a DID is as follows:

```go
Register(did, publicKey)
```

Before creating a DID, the `did` and `publicKey` need to be generated through the following process:

- Generate a pair of public and private keys and create an address.
- Obtain a random number from the blockchain or a nonce from the contract.
- Calculate the hash based on the nonce and address, convert it to a hexadecimal string, and use it as the `memo-specific-id`.
- Prefix the `memo-specific-id` with `did:memo` to generate the final `did`.

After calling this interface, the final `did` and public key information are added to the transaction message, which is then signed with the private key. The contract generates and saves the initial DID document based on the information in the transaction and default information such as the verification method `id`, `type`, and `controller`.

The initial DID document is as follows:

> **DID Document Example 8-1**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     ],
> }
> ```

### 1.4.2 Querying a DID

**(1) Resolving a DID**

Resolving a DID involves retrieving the DID document through the DID. The interface is as follows:

```go
Resolve(did)
```

In the MEMO DID contract, all `verificationMethod` entries are sequentially added to the `verificationMethod` array. Therefore, to resolve all elements in `verificationMethod`, simply iterate through the `verificationMethod` array.

Other attributes such as `authentication`, `assertionMethod`, `capabilityDelegation`, and `recovery` are managed through events emitted after each modification operation, and the contract provides corresponding query interfaces to check the validity of these attributes. Thus, by iterating through the relevant events and calling the query interfaces, all attributes of the MEMO DID can be resolved.

As for the `@context` attribute, it is added by default after the above resolution is complete.

**(2) Dereferencing a DID URL**

Dereferencing a DID URL involves resolving the verification method through the DID URL. The interface is as follows:

```go
Dereference(didUrl)
```

In MEMO DID, a DID URL represents the `id` of a `verificationMethod` and can be parsed into two parts: the DID and the fragment. Therefore, the DID can be used to locate the corresponding `verificationMethod` array, and the fragment can be used to pinpoint the exact position of the `verificationMethod`.

### 1.4.3 Updating a DID

The following interfaces are available for modifying a DID document:

- **addVerificationMethod(did, type, controller, publicKeyHex)**: This interface can only be called by the `masterKey`.
- **updateVerificationMethod(methodID, type, publicKeyHex)**: This interface can only be called by the `controller` of the verification method.
- **deactivateVerificationMethod(methodID)**: This interface can only be called by the `masterKey`.
- **addAuthentication(did, methodID)**: This interface can only be called by the `masterKey`.
- **deactivateAuthentication(did, methodID)**: This interface can only be called by the `masterKey`.
- **addAssertionMethod(did, methodID)**: This interface can only be called by the `masterKey`.
- **deactivateAssertionMethod(did, methodID)**: This interface can only be called by the `masterKey`.
- **addCapabilityDelegation(did, methodID, expiration)**: This interface can only be called by the `masterKey`.
- **deactivateCapabilityDelegation(did, methodID)**: This interface can only be called by the `masterKey`.
- **addRecovery(did, methodID)**: This interface can only be called by the `masterKey`.
- **deactivateRecovery(did, methodID)**: This interface can only be called by the `masterKey`.

We illustrate with some typical examples:

**addVerificationMethod(did, type, controller, publicKeyHex)**

- The user selects a `did` and a `controller` (defaulting to the same as the `did`, but it is possible for a single user to have multiple DIDs, with the main DID controlling other DIDs and their verification methods).
- The user generates a pair of public and private keys and specifies the `type` based on the generation algorithm.
- The user signs with the private key of the `masterKey` and calls the contract interface `addVerificationMethod(did, type, controller, publicKeyHex)`.
  - The contract verifies whether the signature is from the `masterKey` of the `did`.
  - The contract generates the `methodID` based on an incrementing number and the `did`.
  - The contract creates the verification method based on the `methodID`, `type`, `controller`, and `publicKeyHex`.
  - The contract adds the verification method to the `verificationMethod` attribute of the DID document.

After adding `did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1` to the document in Example 8-1, the document is as follows:

> **DID Document Example 8-2**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:b69f63fe2f84d782b134624020802fa49c70f6fad8009c0be1da25229c2be99c",
>         "publicKeyHex": "02948b109c8298057c1d798d9a14ed30f33689d8397e2e60f89ef408b08d7f48a1"
>     },
>     ],
> }
> ```

**modifyVerificationMethod(did, methodIndex, type, publicKeyHex)**

- The user selects a `methodID`, parses out the `did` and `methodIndex`.
- The user generates a pair of public and private keys and determines the `type` based on the generation algorithm.
- The user signs with the private key corresponding to the `masterKey` contained in the verification method's `controller`, and calls the contract interface `modifyVerificationMethod(did, methodIndex, type, publicKeyHex)`.
  - The contract verifies whether the signature is from the `controller` of the `methodID`.
  - The contract modifies the `type` and `publicKeyHex` attributes of the verification method.

After updating `did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1` in Example 8-2, the document is as follows:

> **DID Document Example 8-3**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:b69f63fe2f84d782b134624020802fa49c70f6fad8009c0be1da25229c2be99c",
>         "publicKeyHex": "02fa46f35cecc57016e13c94ba3b0302c7d14daa482741ca18ede9dd2255aaeeeb"
>     },
>     ],
> }
> ```

**deactivateVerificationMethod(did, methodIndex)**

- The user selects a `methodID`, parses out the `did` and `methodIndex`.
- The user signs the message with the `masterKey` of the `did` and calls the contract interface `deactivateVerificationMethod(did, methodIndex)`.
  - The contract verifies whether the signature is from the `masterKey` of the `did`.
  - The contract removes the corresponding verification method.

After deleting `did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#key-1` from the document in Example 8-3, the document is as follows:

> **DID Document Example 8-4**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     ],
> }
> ```

**addCapabilityDelegation(did, methodID, expiration)**

- The user selects a `did` and `methodID`, and sets an expiration time.
- The user signs the message with the `masterKey` and calls the contract interface `addCapabilityDelegation(did, methodID, expiration)`.
  - The contract verifies whether the signature is from the `masterKey` of the `did`.
  - The contract adds the `methodID` to the `capabilityDelegation` attribute of the document, while setting the expiration time (the expiration time is transparent to the DID document displayed to the user; it is only checked by the contract when reading the document, but not displayed).

After adding `did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-1` to the document in Example 8-3, the document is as follows:

> **DID Document Example 8-5**
>
> ```json
> {
> 	"@context": "https://www.w3.org/ns/did/v1",
> 	"id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e",
> 	"verificationMethod": [
>     {
>         "id": "did:memo:ce5ac89f84530a1cf2cdee5a0643045a8b0a4995b1c765ba289d7859cfb1193e#masterKey",
>         "type": "EcdsaSecp256k1VerificationKey2019",
>         "controller": "did:memo:0000000000000000000000000000000000000000000000000000000000000",
>         "publicKeyHex": "031c9cc1c36d53ff1feae79bbd32854a05b3cb4bbf4032383ed1eac79af9a918e7"
>     },
>     ],
>     "capabilityDelegation": [
>         "did:memo:75cc9f6d3c4a68fbe81f7e4926c413c599d2507f408666576401eaf0b36f66fe#key-1",
>     ],
> }
> ```

```json
{
	"@context": "https://www.w3.org/ns/did/v1",
	"id": "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96",
	"verificationMethod": [{
		"id": "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#masterKey",
		"controller": "did:memo:000000000000000000000000000000000000000000000000000000000000000",
		"type": "EcdsaSecp256k1VerificationKey2019",
		"publicKeyHex": "0x03d21e6c4843fa3f5d019e551131106e2075925b01da2a83dc177879a512eb608f"
    },
    {
    	"id": "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#key-1",
    	"controller": "did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96",
    	"type": "EcdsaSecp256k1VerificationKey2019",
    	"publicKeyHex": "0x02d78b20654eb7a5d58d83b25d090a338eff18f0b5f919777c9d894c2e161b4b52"
    }],
    "authentication": [
    	"did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#masterKey"
    ],
    "assertionMethod": [
    	"did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#masterKey"
    ],
    "capabilityDelegation": [
    	"did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#key-1"
    ],
    "recovery": [
    	"did:memo:d687daa192ffa26373395872191e8502cc41fbfbf27dc07d3da3a35de57c2d96#masterKey"
    ]
}
```
