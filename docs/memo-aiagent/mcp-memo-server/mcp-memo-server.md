**MEMO MCP Server** provides a standardized storage interface for AI agents, enabling them to save generated content to MEFS and offering data retrieval functionality. It provides decentralization for AI agents.

## Use Cases

- **Encrypted Data Storage**: Store AI-generated content on MEFS.
- **User Context Retrieval**: Store user behavior and preference data on MEFS. In subsequent requests, quickly retrieve relevant user data and provide it to the AI agent.
- **Seamless Data Sharing**: AI agents can share data without trust through content identifiers (CIDs).

## Free Quota

Start using memo-storage now and enjoy 10GB of free storage space.

## Installing the MCP Server

You can configure the MCP Remote Server to use it.

### Configuring the MCP Server

You can start the MCP server in your client with the following configuration.

**Standard Configuration**, suitable for most clients.

```json
{
  "mcpServers": {
    "storage-server-rest": {
      "url": "http://127.0.0.1:3001/rest"
    }
  }
}
```

## Using MCP Tools

MEMO Storage MCP provides the following tools to interact with the decentralized storage network MEFS.

### Upload

Upload a file to MEFS:

```javascript
// Example usage in an AI application
const result = await uploadFile({
  file: base64EncodedContent,
  name: "document.pdf",
});
```

Parameters:

- `file`: Base64-encoded file content
- `name`: Filename with extension

### Retrieve

Retrieve a file from the MEFS network:

```javascript
// Example retrieval by CID
const document = await retrieveFile({
  cid: "bafybeigdyrzt5sfp7udm7hu76uh7y26nf3efuylqabf3oclgtqy55fbzdi"
});
```

Parameters:

- `cid`: The unique identifier (Content Identifier) of the file.