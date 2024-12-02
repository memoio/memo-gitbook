# Computing Power Platform

The Computing Power Platform is a comprehensive system that serves as a marketplace for computing nodes and users. It requires any computing node to provide an SGX remote attestation upon registration, ensuring that the computing node can provide a Trusted Execution Environment (TEE), thereby guaranteeing that users' personal data will not be leaked during AI training. Additionally, when users create orders and execute AI training in computing nodes, the nodes need to periodically submit computational power proofs to ensure the authenticity of their computational power.

## Role Design

There are various roles within the Computing Power Platform, including users, computational resource providers, and validators. If you are a computational resource seeker, you register as a user; if you are an equipment provider, you register as a computational resource provider; if you provide information management services, you register as a validator.

### Users

Users are the ultimate service targets of the Computing Power Platform. Most functions and mechanisms are designed to better serve users and ensure their security when using the platform. Service requests are initiated by users and require them to pay a certain fee.

Users need to pay a fee based on the computational power of the resource provider, i.e., the CPU/GPU model, and sign an order with the resource provider. Once the order is activated, you can start deploying applications on the computing nodes and securely transmit your personal data to the machines of the resource providers to utilize the purchased computational resources for computation or AI training.

The tokens paid by users for orders will be settled based on the actual service duration provided to the users. Computational resource providers need to submit computational power proofs at regular intervals, and only by submitting the correct proofs will they receive a portion of the order amount.

### Computational Resource Providers

Computational resource providers supply actual computational devices, such as CPUs/GPUs, to users within the Computing Power Platform. They use container orchestration software to organize existing hardware resources (including processors, memory, storage, graphics cards, etc.) and launch gateway services to provide various API interfaces, implementing identity authentication, application deployment, application access, and computational power verification functions.

After deploying local infrastructure and launching gateway services, computational resource providers also need to register their node information on the blockchain with an SGX remote attestation, achieving node publicity and verification of the trusted execution environment. To prevent computational resource providers from engaging in malicious activities later on, they are required to stake a certain amount of tokens during registration.

During application deployment, the resource description file is downloaded from the YAML file URL provided in the deployment request, and the node interacts with the container orchestration software to deploy the application described in the resource description file to the local environment, thus completing the provision of computational services.

### Validators

Validators mainly participate in the proof verification of the Computing Power Platform, including TEE proofs and computational power proofs. Validators need to periodically challenge computational resource providers to verify the authenticity of the computational power they provide. Additionally, validators also verify the TEE proofs submitted by computational resource providers to the chain.

## Computational Power Proof

Computational power proof, or proof of work, is a cryptographic puzzle that requires a significant amount of computational resources to solve. Therefore, by verifying the result of this cryptographic puzzle, one can verify the computational power of CPUs/GPUs. Proof of work relies on the irreversibility of hash algorithms, which means that the corresponding hash value can only be calculated from the original data, and the original data cannot be deduced from the hash value in reverse. Secondly, hash algorithms have an avalanche effect, meaning that a slight change in the input data will cause a significant difference in the hash value.

These two characteristics mean that to calculate the original data for a specified hash value, the only way is to continuously change the original data and perform hash calculations, comparing the hash results with the specified hash value. For a specified hash value, each hash has a 1/2^256 probability of obtaining the specified hash value, so it often requires 2^256 hash calculations to compute the original data corresponding to the specified hash value, and these 2^256 hash calculations are completely independent of each other. Moreover, hash functions can be parallelized in CPUs/GPUs, thus accurately reflecting the actual computational power level of CPUs/GPUs.

## Trusted Execution Environment

A trusted execution environment is an isolated execution environment within a central processing unit that ensures the integrity and confidentiality of the executed programs and data. Therefore, it can obtain the expected structure without leaking user data. In addition, the trusted execution environment also provides remote attestation capabilities, allowing TEE nodes to generate reports on the computing environment and submit them to the chain, thereby allowing validator nodes to verify the correctness of the computation results and the privacy of the computing environment.