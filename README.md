# coree for OpenClaw

Persistent memory and code intelligence for AI agents in [OpenClaw](https://openclaw.ai).

## Install

### Option A - Codex-compatible plugin format

If you use OpenClaw with Codex CLI compatibility mode, install via the Codex marketplace:

```sh
codex plugin marketplace add github:coree-ai/openclaw
codex plugin install coree
```

Or, since OpenClaw reads `.codex-plugin/plugin.json` natively, you can also install directly:

```sh
openclaw plugins install git:github.com/coree-ai/openclaw
```

### Option B - Manual MCP config (recommended for most users)

Add coree to your OpenClaw config. OpenClaw uses `mcp.servers` (not `mcpServers`).

Edit your `~/.openclaw/config.json` (user-scope) or `openclaw.json` at your project root
and merge in the `mcp.servers` block from [`openclaw.json`](./openclaw.json):

```json
{
  "mcp": {
    "servers": {
      "coree": {
        "command": "npx",
        "args": ["--yes", "@coree-ai/coree@0.15.0", "serve"],
        "env": {
          "COREE__MEMORY__REMOTE_AUTH_TOKEN": "${COREE__MEMORY__REMOTE_AUTH_TOKEN}",
          "COREE__MEMORY__REMOTE_URL": "${COREE__MEMORY__REMOTE_URL}",
          "COREE__INDEX__REMOTE_AUTH_TOKEN": "${COREE__INDEX__REMOTE_AUTH_TOKEN}",
          "COREE__INDEX__REMOTE_URL": "${COREE__INDEX__REMOTE_URL}"
        }
      }
    }
  }
}
```

Restart OpenClaw after saving.

### Step 2 - Context file

Copy `AGENTS.md` to your project root so the agent loads coree usage instructions:

```sh
curl -fsSL https://raw.githubusercontent.com/coree-ai/openclaw/main/AGENTS.md -o AGENTS.md
```

### Step 3 - Verify

Start a session in OpenClaw and ask: `What coree tools are available?`

The agent should list tools like `search`, `store_memories`, `capture_note`, etc.

## Notes on Hooks

OpenClaw's lifecycle hooks (like `llm_input`) require TypeScript/JavaScript plugin code and
cannot be declared as shell command configs. coree prompt injection is instead driven by the
MCP tools themselves - the agent calls coree tools in response to context instructions in
`AGENTS.md`.

## Codex Compatibility

If you also use Codex CLI, the `.codex-plugin/plugin.json` in this repo works with both
tools. You can use the `coree-ai/codex` repo as your single source of truth in that case.

## Environment Variables

Set these in your shell profile:

| Variable | Description |
|----------|-------------|
| `COREE__MEMORY__REMOTE_AUTH_TOKEN` | Auth token for remote memory sync |
| `COREE__MEMORY__REMOTE_URL` | Remote memory database URL |
| `COREE__INDEX__REMOTE_AUTH_TOKEN` | Auth token for remote index sync |
| `COREE__INDEX__REMOTE_URL` | Remote index database URL |

## Requirements

- [Node.js](https://nodejs.org) 18+ with `npx` on `PATH`
- OpenClaw with MCP server support (2026.1.0+)

## Version Bumping

```sh
node scripts/bump-version.mjs 0.14.0
```

Updates all version references in `.codex-plugin/plugin.json`, `.mcp.json`,
`openclaw.json`, and `README.md`.
