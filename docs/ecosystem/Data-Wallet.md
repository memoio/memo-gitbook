# Data Wallet

Data Wallet is an encrypted wallet that includes managing user identities and data assets. With Data Wallet, you can manage data assets like managing FTs (Fungible Tokens)/NFTs (Non-Fungible Tokens). Data assets are the result of tokenizing and monetizing personal user data (Web2 data or Web3 data), and an additional Reader role is added for data assets, allowing the Reader role to access personal data.

When using a traditional wallet, user Alice can access all data under her account through the wallet. However, when user Bob adds user Alice as a Reader for a certain piece of data, Alice cannot access that data through a traditional wallet. But with Data Wallet, she can access this data through the wallet.

## How does Data Wallet work?

Similar to traditional wallets, Data Wallet does not store users' personal data. Data Wallet only provides tools for interacting with blockchain and storage systems. Specifically, Data Wallet retains one or multiple pairs of public and private keys for interacting with the blockchain, and Data Wallet retains one or multiple encryption keys for interacting with storage systems.

When a user first opens Data Wallet, a pair of public and private keys is generated, along with a series of mnemonic words. The user's private key is encrypted and stored within Data Wallet, and the address and user ID generated from the public key are made public. With the private key, users can transfer assets they hold (FT/NFT/data assets), moving them from one address to another. Additionally, Data Wallet generates and saves different encryption keys for different storage systems; these encryption keys are used to encrypt and decrypt personal data that users store in storage systems.