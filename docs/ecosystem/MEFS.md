# MEFS

## Introduction

&nbsp;

MEFS（MEMO is a large-scale decentralized data storage system with high security and reliability built around blockchain. It is a new-gen blockchain decentralized cloud storage protocol that organizes global edge storage nodes to provide users with safe, reliable and highly available storage services.

&nbsp;
MEFS(MEMO File System) is the file storage system for MEMO.

![memo](../../images/mefs/memo-icon.png)

&nbsp;

MEMO is a new-gen blockchain decentralized cloud storage protocol developed by MEMO Labs. Our mission is to build a reliable infrastructure for Web3.0. To achieve high scalability and availability, MEMO has vastly innovated data tiering, verification, fault tolerance, and recovery mechanisms. MEMO has made technological breakthroughs on blockchain cloud storage with a new architecture and multiple innovations.

&nbsp;

MEMO has designed three user roles: the User, the storage space user; the Provider, the storage space provider; and the Keeper, the coordination manager. Driven by smart contracts, three interconnected roles constrain each other.

&nbsp;

MEMO can be divided into three functional role-based layers: settlement, verification, and storage. The settlement layer processes settlement on-chain by aggregating order information and sending the amount of each order to the storage nodes Provider. The verification layer is conducted off-chain. The Keeper nodes challenge the Provider nodes, verifie the results of the proof, and decide whether to issue the withdrawal certificate to the Provider nodes. All processes on the verification layer will go through nodes on the same layer and pass the Byzantine fault-tolerant consensus. The storage layer consists of massive scattered Provider nodes, which store genuine User data and regularly submit the proof of storage to the verification layer.

&nbsp;

The following articles will help you learn more.

What is MEMO?

[Memoriae — Next Generation of Decentralized Cloud Storage Based on Blockchain](https://memolabs.medium.com/memoriae-next-generation-of-decentralized-cloud-storage-based-on-blockchain-9151ab8c1aaa)

Roles：

[Build an Autonomous Storage System: Role Design in Memoriae](https://memolabs.medium.com/build-an-autonomous-storage-system-role-design-in-memoriae-f724c405ddc)

[Memoriae System Node Matching](https://memolabs.medium.com/memoriae-system-node-matching-d246fca41009)

Technology：

[Multilevel Fault-tolerant Mechanism Design for MEMO Decentralized Cloud Storage System](https://memolabs.medium.com/multilevel-fault-tolerant-mechanism-design-for-memo-decentralized-cloud-storage-system-f3c585eb401d)

[MEMO Original Data Recovery Strategy: Risk-Aware Failure Identification (RAFI)](https://medium.com/memolabs/the-risk-aware-failure-identification-rafi-strategy-of-memo-decentralized-cloud-storage-system-6c5990ec8cb3)

![memo-picture](../../images/mefs/memoPic.png)

&nbsp;  

### Sign up for Auth Token

The user clicks on the link to obtain test tokens in the Memoriae wallet (under development), and the system will send the user a test token of 10 Memo. When the user's test balance is not less than 10 Memo, they will not be able to apply for the test token again.

send an eail to [sup@memolabs.org](mailto:sup@memolabs.org) to apply for test tokens

Email subject: apply for test token

Email content: account address (format such as 0x...), role (Provider or Keeper or User)

&nbsp;

### Interactive Forum

Persons who are interested in decentralized data storage systems are welcome to join the Memo community and participate in interaction!

Our [GITHUB](https://github.com/memoio/testnet/issues) URL is:

<https://github.com/memoio/testnet/issues>

Our [TWITTER URL](https://twitter.com/Memo_Labs) is:

<https://twitter.com/Memo_Labs>

## Characteristics and Advantages

&nbsp;

**Decentralized storage.** After the user uploads the source file, the Memo system can distribute the data to multiple storage nodes and provide a unified standardized interface to the outside world. When the target user initiates a download request, the nearest node downloads the fragments by the nearest node and provides them to the user completely. Multiple images or copies are provided throughout the network, which greatly improves the access speed and stability.

&nbsp;

**Shared globally**.Multiple Memo devices can form a cluster effect to realize global data sharing, unified storage space management, and automatic load balancing of the cloud storage platform. Files can be distributed across regions and networks, supporting data storage and efficient use across the entire network , On-demand expansion to meet the needs of users for high-speed expansion.

&nbsp;

**Data security.** Data storage function is the cornerstone of Memo, and data security and reliability are the cornerstones of data storage function. Memo's data privacy policy requires data at its source-the client-that is encrypted with a user key before it is written to the edge storage device. In addition, the access control mechanism ensures that the data can be accessed by its owner or its maintainer and the store in collaboration, while not being accessed by other users, or unrelated maintainers and storers.

&nbsp;

**System reliability.** Memo's goal of ensuring reliability is to prevent data loss when user equipment, maintenance equipment, or edge storage equipment fails. Duplication, erasure coding, and other data redundancy technologies can be used to ensure the reliability of data stored on edge storage devices.

&nbsp;

**The original data recovery method--RAFI**. In addition to data redundancy mechanisms, data repair methods are also an important factor in determining data reliability. Memo's original data recovery method RAFI is used to further improve the reliability of data stored in edge storage devices. RAFI technology can optimize the edge storage space, and more guarantee the security of data and the stability of the system. Through real-time query, data with high risk of loss can be quickly found, effectively shortening the total time of data repair and improving performance.

&nbsp;

### Compared with Other Blockchain Storage Projects, What Is Special about MEMO?

MEMO is a blockchain-based decentralized cloud storage system. It's unlike some projects aiming at producing blocks whose storage data volume is directly linked to the calculation power of blocks; it is also different from pseudo-decentralized distributed storage projects, one of whose nodes or several usually undertake the function of 'centralized nodes'.

## Roles Design of Memo System

In the Memo distributed cloud storage system, each user can register a role according to his own needs. The system roles include User, Keeper, and Provider. If it is a storage demander, it is registered as a User; if it is a device provider, it is registered as a Provider; if it only provides information management services, it is registered as a Keeper. This article will explain the design principles of each role from the perspective of overall system operation.

The basic roles of the three parties are as follows:

1、User: is the consumer of the storage service in the system, puts forward storage requirements and uploads data

2、Keeper: It is the management coordinator in the system, responsible for information intermediary and management functions.

3、Provider: is the storage device provider in the system, providing storage space for users.

Among these three roles, User and Provider are the supply and demand parties of storage services, and User is also the end user in the system. In short, User uses storage space in the Memo system, Keeper finds a suitable storage node for User, and storage node Provider stores data for User. Keeper is equivalent to an intermediate information matchmaker, providing intermediary services for users and providers. We can understand Keeper as a headhunter on a recruitment platform or an intermediary in a real estate trading platform.
