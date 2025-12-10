MEMO MCP Server为AI代理提供标准的存储接口，允许AI代理将生成的内容保存到MEFS并提供数据检索功能。为AI代理提供去中心化

## 用例

- 数据加密存储：将AI生成的内容存储到MEFS。
- 用户上下文检索：将用户的行为和偏好数据存储到MEFS，并在后续请求中，快速检索相关的用户数据并提供给AI代理。
- 数据无缝分享：AI代理之间能够通过内容标识符 (CID) 实现了无需信任的数据分享。

## 免费额度

立即开始使用memo-storage，即可免费享受10GB的存储空间。

## 安装MCP服务器

您可以使用配置MCP远程服务器

### 配置 MCP 服务器

您可以在您的客户端中，使用以下配置启动MCP服务器

**标准配置**适用于大多数客户端

```json
{
  "mcpServers": {
    "storage-server-rest": {
      "url": "http://127.0.0.1:3001/rest"
    }
  }
}
```

## 使用MCP工具

MEMO Storage MCP提供以下工具与去中心化存储网络MEFS交互

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
// Example retrieval by MD5
const document = await retrieveFile({  cid: "cf54c8af4c353927cc424d4331c47f5b"});
```

参数：

- `MD5`: 文件的唯一标识符
