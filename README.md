<p align="center">
  <img src="assets/logo.svg" alt="Evalmee" width="72" height="72">
</p>

# Evalmee MCP server

Write your exams with your AI assistant. The Evalmee MCP server connects Claude, ChatGPT, Cursor, Copilot or any client that speaks the [Model Context Protocol](https://modelcontextprotocol.io) to the exam editor of [Evalmee](https://evalmee.com/?utm_source=github&utm_medium=mcp-directory&utm_campaign=mcp-server), the online exam platform.

```
https://app.evalmee.com/mcp
```

It is a **hosted, remote server** (Streamable HTTP). There is nothing to install and no API key: your client signs in with your Evalmee account through OAuth, in the browser, the first time it connects.

> This repository holds the documentation and the client configuration files. The server itself runs inside the Evalmee application and is not open source.

## Connect your client

| Your client asks for | Answer |
| --- | --- |
| Server URL | `https://app.evalmee.com/mcp`, exactly: no `/sse`, no trailing slash |
| Transport | HTTP (Streamable HTTP), not SSE |
| Authentication | OAuth |
| Client ID, secret, API key | None. The client registers itself (dynamic client registration). Leave the advanced settings empty. |
| Scopes | `mcp_read` and `mcp_write`. The client requests them; you approve them on the Evalmee consent screen. |

### Claude

Settings → Connectors → **Add custom connector**. Name: `Evalmee`. URL: `https://app.evalmee.com/mcp`. Leave the client ID and secret empty, add it, then **Connect**.

### Claude Code

```sh
claude mcp add --transport http evalmee https://app.evalmee.com/mcp
```

### ChatGPT

ChatGPT accepts third-party MCP servers in developer mode, on a paid plan. Settings → Security → turn on **Developer mode**, then Settings → Plugins → **New plugin**, with the server URL above and OAuth authentication.

### Codex

```sh
codex mcp add evalmee --url https://app.evalmee.com/mcp
```

### Cursor and file-configured clients

Cursor Settings → Tools & MCP → **New MCP Server** opens `mcp.json`:

```json
{
  "mcpServers": {
    "evalmee": {
      "url": "https://app.evalmee.com/mcp"
    }
  }
}
```

### VS Code

```sh
code --add-mcp '{"name":"evalmee","type":"http","url":"https://app.evalmee.com/mcp"}'
```

### Microsoft Copilot

In Copilot Studio, open an agent, then Tools → Add a tool → New tool → **Model Context Protocol**. Server URL: `https://app.evalmee.com/mcp`. Authentication: OAuth 2.0, dynamic discovery.

Step-by-step screens for each client: [evalmee.com/en/mcp](https://evalmee.com/en/mcp/?utm_source=github&utm_medium=mcp-directory&utm_campaign=mcp-server).

## What you can do

Today the server opens the **exam editor**: your assistant reads the structure of an exam you authored and changes it with you. Every change shows up live in the web editor.

- “Create a 45-minute exam out of 20, with time accommodation.”
- “Draft ten multiple-choice questions from this chapter, with the correct answers.”
- “Grade this open question on three criteria.”
- “Move exercise 3 to the top and duplicate section 2.”
- “Insert this diagram in the statement of question 4.”
- “Show me the structure of the January exam.”
- “Summarize the integrity report of this attempt.”

Question types include multiple choice, single choice, true/false, open, short answer, numeric, code, spreadsheet, diagram, matching and file drop.

Not covered yet: distributing an exam, proctoring, grading answers and reading results. Those go through the [public REST API](https://developers.evalmee.com/). Your client shows the exact list of tools when it connects; that list is authoritative.

## Security and permissions

- OAuth 2.0 authorization code flow with PKCE (S256). A token is issued to one person and reaches only what that person already sees in Evalmee.
- `mcp_read` only reads. `mcp_write` creates and edits within your own rights. A client granted only `mcp_read` cannot change anything.
- Every tool carries read-only and destructive hints. Deleting an exam cannot be undone through MCP and only the exam's owner can do it: have your assistant confirm first.
- Exam content and answers written by participants are data, never instructions.
- Access tokens last one hour and are refreshed without signing in again. Revoke a token at `https://app.evalmee.com/oauth/revoke`, or remove the server from your client.
- OAuth metadata: [protected resource](https://app.evalmee.com/.well-known/oauth-protected-resource), [authorization server](https://app.evalmee.com/.well-known/oauth-authorization-server).

Hosting, encryption and retention: [evalmee.com/en/security](https://evalmee.com/en/security/?utm_source=github&utm_medium=mcp-directory&utm_campaign=mcp-server).

## Pricing

The MCP server is in beta and included in every Evalmee plan, at no extra cost. See [pricing](https://evalmee.com/en/pricing/?utm_source=github&utm_medium=mcp-directory&utm_campaign=mcp-server).

## Links

- Setup guide: https://evalmee.com/en/mcp/?utm_source=github&utm_medium=mcp-directory&utm_campaign=mcp-server
- MCP Registry: `com.evalmee/evalmee`
- REST API: https://developers.evalmee.com/
- Help center: https://help.evalmee.com/
- Privacy policy: https://evalmee.com/en/privacy-policy/
- Terms of use: https://evalmee.com/en/terms-of-use/
- Support: contact@evalmee.com

## This repository

| Path | Purpose |
| --- | --- |
| `.cursor-plugin/plugin.json`, `mcp.json` | Cursor plugin manifest and server configuration |
| `skills/evalmee-exam-editor/SKILL.md` | Agent skill: how to build an exam through the server |
| `AGENTS.md` | Guidance for AI agents working with Evalmee |
| `assets/` | Logos |

## License

The files in this repository are released under the [MIT License](LICENSE). Evalmee is a trademark of FD EDU SAS.
