# github-agent

A repository created via the **GitHub MCP Server** to demonstrate how AI agents interact with GitHub through the **Model Context Protocol (MCP)**.

## What is the Model Context Protocol (MCP)?

The **Model Context Protocol (MCP)** is an open standard that connects AI applications (hosts/clients) to external data sources and tools (servers). It gives language models a consistent, secure way to discover capabilities and take action outside the chat window.

Think of MCP as a **USB-C port for AI**: one standard interface that lets assistants plug into many services without custom integrations for each one.

- **Specification & docs:** [modelcontextprotocol.io](https://modelcontextprotocol.io)
- **Reference servers:** [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)

## Core Concepts

### Host, Client, and Server

| Role | Description |
|------|-------------|
| **Host** | The AI application you use (e.g., Cursor, VS Code, Claude Desktop) |
| **Client** | Component inside the host that maintains a connection to an MCP server |
| **Server** | Program that exposes tools, resources, and prompts to the client |

### Primitives

MCP servers expose three main capability types:

1. **Tools** — Functions the model can invoke (e.g., create a repo, open a PR, search code)
2. **Resources** — Read-only data the model can fetch (e.g., file contents, API responses)
3. **Prompts** — Reusable, templated workflows the server defines for common tasks

### Transports

MCP supports multiple transport mechanisms:

- **stdio** — Local process communication via standard input/output (common for local servers)
- **Streamable HTTP** — Remote servers over HTTPS (used by GitHub's hosted MCP endpoint)
- **SSE (legacy)** — Older HTTP-based transport, being superseded by Streamable HTTP

## How GitHub MCP Fits In

The [GitHub MCP Server](https://github.com/github/github-mcp-server) exposes GitHub operations as MCP tools so agents can:

- Search repositories, issues, and pull requests
- Create and update files, branches, and pull requests
- Manage issues and review workflows
- Inspect CI/CD and release information

This repository was created using those tools from Cursor:

1. `create_repository` — created **github-agent** under [Yu-zzz](https://github.com/Yu-zzz)
2. `push_files` — added this README in a single commit

## Architecture (High Level)

```
┌─────────────┐     MCP      ┌──────────────┐     GitHub API     ┌─────────┐
│  AI Host    │ ◄──────────► │  MCP Server  │ ◄────────────────► │ GitHub  │
│  (Cursor)   │   tools /    │  (github)    │                    │         │
│             │   resources  │              │                    │         │
└─────────────┘              └──────────────┘                    └─────────┘
```

The host never talks to GitHub directly for MCP-enabled actions; the server handles authentication, API calls, and response shaping.

## Security Considerations

When using MCP with GitHub:

- **Use a Personal Access Token (PAT)** with the minimum scopes required (e.g., `repo`, `read:org` as needed)
- **Prefer fine-grained tokens** with explicit repository and permission limits
- **Never commit tokens** to source control; store them in host config or a secrets manager
- **Review tool calls** before approving high-impact actions (repo creation, merges, deletes)
- **Rotate tokens** if they are exposed or no longer needed

## Getting Started with GitHub MCP in Cursor

1. Open **Cursor Settings → Tools & Integrations → MCP**
2. Add the GitHub server (remote or Docker) — see the [official install guide](https://github.com/github/github-mcp-server/blob/main/docs/installation-guides/install-cursor.md)
3. Set your `GITHUB_PERSONAL_ACCESS_TOKEN` (or Bearer token in headers for the remote server)
4. Restart Cursor and verify the green status indicator
5. Ask the agent to perform GitHub tasks using natural language

### Example Remote Configuration

```json
{
  "mcpServers": {
    "github": {
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer YOUR_GITHUB_PAT"
      }
    }
  }
}
```

## Learn More

- [MCP Introduction](https://modelcontextprotocol.io/docs/getting-started/intro)
- [MCP Specification](https://spec.modelcontextprotocol.io)
- [GitHub MCP Server](https://github.com/github/github-mcp-server)
- [Cursor MCP Documentation](https://docs.cursor.com/context/model-context-protocol)

---

*Created with the GitHub MCP Server from Cursor.*
