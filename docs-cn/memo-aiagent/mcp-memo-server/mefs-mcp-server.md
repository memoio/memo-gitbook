# MEFS-MCP-SERVER

MEMO MCP Server为AI代理提供标准的存储接口，允许AI代理将生成的内容保存到MEFS并提供数据检索功能。为AI代理提供去中心化

## 用例

- 数据加密存储：将AI生成的内容存储到MEFS。
- 用户上下文检索：将用户的行为和偏好数据存储到MEFS，并在后续请求中，快速检索相关的用户数据并提供给AI代理。
- 数据无缝分享：AI代理之间能够通过内容标识符 (CID) 实现了无需信任的数据分享。

## 免费额度

立即开始使用mefs-storage，即可免费享受10GB的存储空间。

## 快速安装指南

您可以按照以下步骤安装MCP服务器，在使用MEFS MECP server前，请确保您已创建了EVM私钥。

### 前置条件

在使用MEFS MECP server前，请确保您已创建了EVM私钥。您可以使用[MetaMask](https://metamask.io/)，[Okx Wallet](https://web3.okx.com/)等EVM钱包创建钱包，并获取您的私钥。

### 配置 MCP 服务器（需要指定私钥）

您可以在您的客户端中，使用以下配置启动MCP服务器

**标准配置**适用于大多数客户端

```json
{
  "mcpServers": {
    "mefs-storage-server": {
      "command": "npx",
      "args": ["mefs-mcp-server"],
      "env": {
        "MCP_TRANSPORT_MODE": "stdio",
        "MEFS_PRIVATE_KEY": "<YOUR-PRIVATE-KEY>",
      },
      "shell": true,
      "cwd": "./",
    },
  },
}
```

将YOUR-PRIVATE-KEY替换为您的EVM私钥。

## SSE模式

MEFS-MCP-SERVER支持SSE模式，您可以通过以下步骤启动SSE模式，并配置到您的个人AI代理中:

### 克隆项目

```bash
git clone https://github.com/memoio/mefs-mcp-server.git && cd mefs-mcp-server
```

### 安装依赖

```bash
pnpm install
```

### 编译

```bash
pnpm run build
```

### 以SSE模式启动

```bash
pnpm run start:sse
```

您可以在项目的`.env`中配置`MEFS_PRIVATE_KEY`参数，或者使用`export`将`MEFS_PRIVATE_KEY`参数配置到环境变量中，或者使用以下命令启动，从而制定您的个人EVM私钥：

```bash
MEFS_PRIVATE_KEY=<YOUR-PRIVATE-KEY> pnpm run start:sse
```

### 配置MCP服务 (SSE mode)

```
{
  "mcpServers": {
    "mefs-storage-server": {
      "url": "http://localhost:3001/sse",
    },
  },
}
```

## 使用MCP工具

MEFS MCP Server提供以下工具与去中心化存储网络MEFS交互

### 上传

上传文件到MEFS：

```js
// Example usage in an AI applicationconst 
result = await uploadFile({  
        file: base64EncodedContent,  
        name: "document.pdf", 
    });
```

参数：

- `file`: Base64编码的文件内容
- `name`: 带扩展名的文件名

### 检索

从MEFS网络检索文件：

```javascript
// Example retrieval by CID
const document = await retrieveFile({  cid: "bafybei...gq5a/document.pdf"});
```

参数：

- `cid`: 文件的唯一标识符
