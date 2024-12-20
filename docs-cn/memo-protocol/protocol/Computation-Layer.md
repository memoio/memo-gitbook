# Computation & Training Layer

Computation & Training Layer可在保护用户私密数据的前提下参与第三方公开的计算或者AI训练任务，并从中获取到一定的奖励，该层基于可信执行环境。可信执行环境是中央处理器中隔离的执行环境，能够确保执行程序以及数据的完整性和机密性，因此能够在不泄露用户数据的前提下获得预期的结构。此外，可信执行环境还提供远程认证功能，远程认证功能允许TEE节点生成计算环境报告并提交上链，从而允许验证节点验证计算结果的正确性以及计算环境的私密性。

## 可信执行环境

在数据的生命周期内（数据存储，数据传输以及数据使用），我们可以通过端对端加密为数据存储和数据传输提供保护。但是在使用数据计算之前，必须将加密数据解密后才能处理数据。这将导致用户的个人数据对他人可见，不可避免的导致用户数据泄露。可信执行环境为数据使用提供隐私保护

可信执行环境，例如Intel SGX，通过一套CPU指令能够为应用创建安全区Enclave——RAM 内的私有硬件加密区域，其中包括可信代码以及数据。除了Enclave内的可信代码外，其余应用包括操作系统以及BIOS均无法访问这部分加密数据。Intel SGX允许Enclave内的可信代码和Enclave外的不可信代码相互调用，但应尽量将可信代码和不可信代码之间的交互次数降至最低。

Intel SGX 可针对已知的硬件和软件攻击提供以下保护措施：

- 安全区内存不可从安全区外读写，无论当前的权限是何种级别，CPU 处于何种模式。
- 产品安全区不能通过软件或硬件调试器来调试（可创建具有以下调试属性的安全区：该调试属性支持专用调试器，即Intel SGX 调试器像标准调试器那样对其内容进行查看。 此措施旨在为软件开发周期提供辅助，但调试属性的安全区不能用于产品发布）。
- 安全区环境不能通过传统函数调用、转移、注册操作或堆栈操作进入。调用安全区函数的唯一途径是完成可执行多道保护验证程序的新指令。
- 安全区内存采用具有回滚保护功能的行业标准加密算法进行加密。访问内存或将 DRAM 模块连接至另一系统只会产生加密数据。
- 内存加密秘钥会随着电源周期（例如，启动时或者从睡眠和休眠状态进行恢复时）随机更改。 该秘钥存储在 CPU 中且不可访问。
- 安全区中的隔离数据只能通过共享安全区的代码访问。

## TEE集群

memo协议运行了一组TEE集群，TEE节点需要质押一定的memo代币才能参与计算或者AI训练，完成计算后会将结果和证明上传到链上。验证节点会验证链上的证明，验证通过即为TEE节点发放奖励，验证不通过则会扣除TEE节点质押的memo代币。

用户若想参与计算或者AI训练，需要用户授权1个或者多个TEE节点访问用户加密数据的权限，并可将其授权的TEE节点作为用户的个人代理服务器。若第三方需要获取用户数据进行计算或者AI训练，只需要访问授权的TEE节点即可，这样做用户无需一直保持上线状态。

第三方例如AI公司在发布任务并支付费用后（包括用户，TEE节点以及验证节点的费用），可以直接和相应的TEE节点交互，传入其AI训练参数并由TEE节点获取用户数据并进行AI训练。这样做既保护了用户数据的隐私，也保护了AI公司的AI模型参数的隐私。