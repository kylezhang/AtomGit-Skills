# Setup And Safety

Read this file when the runtime cannot find AtomGit tools, the request needs authentication, or the task touches destructive, organization-wide, or enterprise-wide changes.

## When Setup Happens

Install and configure AtomGit MCP before the first AtomGit task in a given client or runtime.

- If the runtime already exposes AtomGit tools, do not reinstall the server; continue with the business workflow.
- If the runtime does not expose AtomGit tools, pause the business workflow and complete MCP setup first.
- If tools exist but calls fail with auth or scope errors, keep the server and fix the token or permissions.

## Dependencies

- [Node.js](https://nodejs.org/) `>= 18`
- AtomGit MCP Server:
  `npx -y @atomgit.com/atomgit-mcp-server`
- Personal access token:
  `ATOMGIT_TOKEN`

The official README uses `npx` as the primary startup path. Global installation is optional, not required for the standard setup flow.

## Minimal Runtime Bring-Up

1. Create an AtomGit personal access token.
2. Configure `ATOMGIT_TOKEN` in the MCP client environment.
3. Start the server with `npx -y @atomgit.com/atomgit-mcp-server`.
4. Restart the MCP client if needed so the new server appears in the runtime tool list.

## Client Configuration Pattern

The official README configures AtomGit MCP at the client layer rather than inside each task.

```json
{
  "mcpServers": {
    "atomgit": {
      "command": "npx",
      "args": ["-y", "@atomgit.com/atomgit-mcp-server"],
      "env": {
        "ATOMGIT_TOKEN": "your_ATOMGIT_TOKEN",
        "ATOMGIT_ENABLE_DANGEROUS_TOOLS": "false"
      }
    }
  }
}
```

On Windows clients that require it, use `npx.cmd` instead of `npx`.

## Token Setup

Create a token at [AtomGit personal token settings](https://atomgit.com/setting/token-classic).

```bash
# Linux or macOS
export ATOMGIT_TOKEN="your-personal-access-token"

# Windows PowerShell
$env:ATOMGIT_TOKEN="your-personal-access-token"
```

## Recommended Permission Scope

| Task | Typical permission |
| --- | --- |
| Read public repositories | Basic access |
| Read private repositories | `repo` read |
| Create or update repository content | `repo` write |
| Manage organization members | `write:org` |
| Enterprise administration | `admin:enterprise` |

## Safety Rules

- Never paste the token into chat transcripts or commit it into a repository.
- Prefer discovery calls before mutation calls.
- Confirm destructive, irreversible, or broad-scope changes with the user before executing them.
- Use a dedicated high-privilege token for org or enterprise administration instead of reusing a general token.
- If the server exposes dangerous operations behind `ATOMGIT_ENABLE_DANGEROUS_TOOLS=true`, treat that as an extra gate, not as permission to skip confirmation.
- The runtime-exposed tool set depends on `ATOMGIT_ENABLE_DANGEROUS_TOOLS`; docs may describe more tools than the current session actually exposes.

## Common Failure Modes

### Tool not found

- Confirm the AtomGit MCP server is installed and connected to the current runtime.
- Confirm the client was restarted or reloaded after adding the MCP server entry.
- Compare the runtime tool list with the canonical names used in this skill.
- If the runtime wraps MCP tools in a namespace, use the wrapped name shown by the runtime.

### `401 Unauthorized`

- Confirm `ATOMGIT_TOKEN` is set in the environment visible to the runtime.
- Regenerate the token if it expired or was revoked.

### `403 Forbidden`

- The token is valid but does not have enough scope.
- Recreate or replace it with the minimum scope needed for the requested action.

## Cross-Platform Notes

- Keep frontmatter minimal: `name` and `description` only.
- Keep examples transport-agnostic: use canonical method names such as `atomgit_get_repository` in docs, but follow the runtime-exposed tool name at execution time.
- Avoid baking runtime-specific parser hints into the frontmatter or tool examples.
