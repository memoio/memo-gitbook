# Ecnomic Model

## Overall Design

The design of memo is divided into three layers:

+ Settlement Layer: Used for role registration, group management, and fee settlement.
+ Data Management Layer: Used for data management: order aggregation, challenge and repair scheduling.
+ Storage Layer: Responsible for storing data and responding to data requests.

Three Roles

There are three roles in the system: user, keeper, and provider. An account can only take on one role.

+ Keeper: Responsible for data management.
+ Provider: Responsible for data storage.
+ User: Data provider.

## Settlement Layer

### Functions of the Settlement Layer

+ Role Registration: The threshold for each account to enter this system is to register in the settlement layer to become a system account.
+ Group Management: A group serves as an overall management unit, and an account can only choose to join one group; a keeper must be approved to join a group, while providers and users can join freely; keepers within a group collectively manage the storage data information of this group (an exit mechanism is temporarily not available).
+ Pledge Pool Management: Each account can put tokens into the staking pool, which distributes profits based on the amount of tokens staked.
+ Storage Settlement Pool: The fees paid by users will be frozen in the settlement pool and paid based on the actual storage duration, size, and price.

### Storage Income

+ User Payment: After user and provider sign a storage order, it is submitted to the settlement layer by the keeper group. In the storage settlement pool, the order fee will be deducted from the user's balance and then frozen.
+ Provider: The provider, after continuously responding to challenges, obtains a space-time fee voucher and then gets income in the settlement pool according to the voucher.
+ Keeper: For each order, the keeper group charges a certain percentage of fees, for example, 4%; members of the keeper group distribute this fee according to their contributions to the group; the fee is released in two ways: 1. 75% is obtained when the provider gets income, which is linearly released; 2. The remaining 25% is obtained after the order expires.
+ Foundation: The foundation charges a 1% management fee.

#### Example

If a user stores 1GB of data for 100 days at a rate of 1 token/(day*GB), the user needs to pay 105 tokens for this order, of which 100 tokens go to the provider, 4 tokens to the group, and 1 token to the foundation; on day 0, the foundation can get 1 token, and neither the provider nor the group can get anything; on day 10, the provider can get 10 tokens, and the group can get 0.3 tokens; on day 90, the provider can get 90 tokens, and the group can get 2.7 tokens; on day 100, the provider gets all 100 tokens, and the group gets 4 tokens.

### Staking Income

Each account can invest in the staking pool, which distributes profits based on the staking shares of all accounts.
The revenue of the staking pool comes from incentives. As the storage scale expands, the system issues incentives to the staking pool based on factors such as storage scale and price.
