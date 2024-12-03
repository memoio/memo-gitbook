# Computation & Training Layer

The Computation & Training Layer allows participation in third-party public computing or AI training tasks while protecting user privacy data, and earning rewards from these tasks. This layer is based on Trusted Execution Environments (TEE). Trusted Execution Environments are isolated execution environments within central processing units that ensure the integrity and confidentiality of the executed programs and data, thus obtaining the expected results without leaking user data. Additionally, TEEs provide remote attestation capabilities, allowing TEE nodes to generate reports on the computing environment and submit them to the blockchain, thereby enabling verification nodes to validate the correctness of the computation results and the privacy of the computing environment.

## Trusted Execution Environment

Throughout the lifecycle of data (data storage, data transmission, and data usage), we can provide protection for data storage and data transmission through end-to-end encryption. However, before using data for computation, encrypted data must be decrypted, which makes the user's personal data visible to others, inevitably leading to data leakage. Trusted Execution Environments offer privacy protection for data usage.

Trusted Execution Environments, such as Intel SGX, use a set of CPU instructions to create secure areas called Enclaves—private hardware-encrypted areas within RAM, which include trusted code and data. Except for the trusted code within the Enclave, the rest of the applications, including the operating system and BIOS, cannot access this encrypted data. Intel SGX allows the trusted code inside the Enclave to call and be called by the untrusted code outside the Enclave, but interactions between trusted and untrusted code should be minimized.

Intel SGX provides the following protections against known hardware and software attacks:

- Enclave memory cannot be read or written from outside the Enclave, regardless of the current privilege level or CPU mode.
- Product enclaves cannot be debugged through software or hardware debuggers (enclaves with the following debugging attributes can be created: these attributes support dedicated debuggers, i.e., Intel SGX debuggers that can view their contents like standard debuggers. This measure is intended to assist in the software development cycle, but enclaves with debugging attributes must not be used for product release).
- Enclave environments cannot be entered through traditional function calls, transfers, register operations, or stack operations. The only way to call enclave functions is to complete the new instruction for executable multi-protect verification programs.
- Enclave memory is encrypted using industry-standard encryption algorithms with rollback protection. Accessing memory or connecting DRAM modules to another system will only produce encrypted data.
- Memory encryption keys are randomly changed with power cycles (e.g., during startup or recovery from sleep and hibernation states). The keys are stored in the CPU and are inaccessible.
- Isolated data within the Enclave can only be accessed by code that shares the Enclave.

## TEE Cluster

The memo protocol operates a set of TEE clusters, where TEE nodes must stake a certain amount of memo tokens to participate in computing or AI training. After completing the computation, the results and proofs are uploaded to the blockchain. Verification nodes will verify the proofs on the chain; if the verification passes, rewards are distributed to the TEE nodes, and if it fails, the memo tokens staked by the TEE nodes are deducted.

If users wish to participate in computing or AI training, they need to authorize one or more TEE nodes to access their encrypted data and can use their authorized TEE nodes as personal proxy servers. If third parties need to access user data for computation or AI training, they only need to access the authorized TEE nodes, eliminating the need for users to remain online at all times.

Third parties, such as AI companies, after publishing tasks and paying fees (including fees for users, TEE nodes, and verification nodes), can directly interact with the corresponding TEE nodes, input their AI training parameters, and have the TEE nodes obtain user data and perform AI training. This approach protects the privacy of user data and also safeguards the privacy of AI model parameters from AI companies.