# FAQ

1.What's Memolabs?

[Memolabs](https://memolabs.org/) is a laboratory focused on researching and expanding the application of decentralized cloud storage technology. Our mission is to build reliable infrastructure for Web3.0 and develop powerful decentralized storage and Web3 application tools.

2.What's Mefs?

mefs is a low-cost, reliable and secure decentralized cloud storage system developed by [Memolabs](https://memolabs.org/), which utilizes edge storage devices and the immutability and decentralization of blockchain. For more information, please refer to [Memolabs](https://memolabs.org/).

3.Why file operation is object?

In MEFS, object and file are equivalent. So whlie you see "object" or "file", you can think of them as "file".

4.How to set an address of the blockchain?

Run mefs-user/keeper/provider config Eth to view the address of the blockchain to which you are connected, If you want to modify, you can run mefs-user/keeper/provider config Eth xxx, where xxx is the API address of the chain.

5.How to check the version of mefs?

Run `mefs-user/keeper/provider version to see the version number of mefs.

6.How to check my balance?

Run mefs-user/keeper/provider test showBalance in the command line to view the balance of the running account, the unit is wei;

7.Account address and network address

After starting mefs, there will be two addresses: account address and network address; the account address format is 0x..., the network address format is 8M...; These two addresses are actually equivalent, one for interacting with the chain, and one for network connection.

8.What's Meeda?

Meeda is a data availability solution launched by Memolabs for Ethereum Layer2, especially Optimistic Rollup, which is low-cost, safe and reliable.

9.What's Data Availability (DA)?

DA not only includes data storage, but also needs to ensure that any node can access the data. After Ethereum expansion solution Layer2 Rollup executes and submits transactions, the verification node needs to verify the transaction data, and only the verified transactions will be finalized by Layer1. The premise of the verification process is to be able to access the transaction data. Therefore, the transaction data can be accessed by any node, so the process of verifying the correctness of the transaction is called data availability. For more details, please refer to: https://ethereum.org/en/developers/docs/data-availability/.

10.How to combine Ethereum Layer2 with Meeda?

For details, please refer to the contents of `Node/Op Node & Meeda` and `Developers/Connect L2 to Meeda`.
