# Config File Formats Reference

> Last updated: April 2, 2026
>
> Derived from actual file analysis on a real development machine. These are the formats Naqi's 10 client parsers must handle.

---

## Claude Desktop

### Config path

| OS | Path |
|----|------|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/claude/claude_desktop_config.json` |

### Schema

```jsonc
{
  // MCP servers — the primary scan target
  "mcpServers": {
    "<server-name>": {
      "command": "npx",                    // Required: executable
      "args": ["-y", "package-name"],      // Optional: command arguments
      "env": {                             // Optional: environment variables
        "API_KEY": "value"
      }
    }
  },

  // User preferences — not scanned, but useful metadata
  "preferences": {
    "quickEntryDictationShortcut": { "accelerator": "Alt+Cmd+C" },
    "coworkScheduledTasksEnabled": true,
    "sidebarMode": "task"
  }
}
```

### Parser notes

- Transport is **always stdio** — Claude Desktop does not support HTTP/SSE MCP servers
- The `mcpServers` key may be absent (no servers configured)
- The `preferences` key is independent — the file may contain only preferences
- File may be missing entirely if Claude Desktop has never been configured
- Env var values are stored in plaintext

---

## Claude Code

Claude Code has the most complex config layout — multiple files, multiple locations, layered overrides.

### Config paths

| File | Path | Purpose |
|------|------|---------|
| Global MCP | `~/.claude/mcp.json` | MCP server definitions |
| Global settings | `~/.claude/settings.json` | Plugin toggles, update channel |
| Local settings | `~/.claude/settings.local.json` | Permissions (machine-specific) |
| State file | `~/.claude.json` | Runtime state (not user config) |
| Global memory | `~/.claude/MEMORY.md` | Persistent cross-session memory |
| Project memory | `<project>/.claude/projects/<encoded-path>/memory/*.md` | Per-project memory files |
| Project MCP | `<project>/.mcp.json` | Project-scoped MCP servers |
| Project config | `<project>/.claude/settings.local.json` | Project-scoped permissions |
| Project context | `<project>/CLAUDE.md` | Project instructions (read by Claude) |
| Project rules | `<project>/.claude/rules/*.md` | Conditional rules with YAML frontmatter |
| Skills directory | `~/.claude/skills/` | Community/user skills |
| Agent skills | `~/.claude/agent-skills/` | Agent skill sets (e.g., WordPress) |

### Managed MCP config (system-level)

| OS | Path |
|----|------|
| macOS | `/Library/Application Support/ClaudeCode/managed-mcp.json` |
| Linux | `/etc/claude-code/managed-mcp.json` |
| Windows | `C:\Program Files\ClaudeCode\managed-mcp.json` |

Admin-managed MCP servers. Same schema as user MCP config below.

### MCP config (`~/.claude/mcp.json`)

```jsonc
{
  "mcpServers": {
    // stdio transport
    "<server-name>": {
      "type": "stdio",                      // Explicit transport type
      "command": "npx",
      "args": ["-y", "package-name@latest"],
      "cwd": "/path/to/working/dir",        // Optional: working directory
      "env": {                               // Optional: environment variables
        "API_KEY": "${API_KEY}",             // Supports ${VAR} expansion
        "FALLBACK": "${MISSING:-default}"    // Supports ${VAR:-default} syntax
      }
    },

    // HTTP transport
    "<http-server>": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "headers": {                           // Optional: request headers
        "Authorization": "Bearer ${TOKEN}"
      },
      "headersHelper": "/path/to/script.sh", // Optional: script that generates headers
      "oauth": {                             // Optional: OAuth configuration
        "clientId": "my-client-id",
        "callbackPort": 8080,
        "authServerMetadataUrl": "https://auth.example.com/.well-known/openid-configuration"
      }
    },

    // SSE transport
    "<sse-server>": {
      "type": "sse",
      "url": "https://mcp.example.com/sse",
      "headers": {
        "Authorization": "Bearer ${TOKEN}"
      }
    }
  }
}
```

Claude Code supports **all three transport types**: stdio, HTTP, and SSE. The `type` field can be set explicitly to `"stdio"`, `"http"`, or `"sse"`.

**Environment variable expansion:** `${VAR}` and `${VAR:-default}` syntax is supported in `command`, `args`, `env`, `url`, and `headers` fields.

### Settings (`~/.claude/settings.json`)

```jsonc
{
  "enabledPlugins": {
    "plugin-name@marketplace-name": true,  // Boolean toggle
    "another-plugin@marketplace": false
  },
  "autoUpdatesChannel": "latest"           // "latest" | "stable"
}
```

### Local settings (`~/.claude/settings.local.json`)

```jsonc
{
  "permissions": {
    "allow": [
      "Bash(python3:*)",                   // Glob-pattern permission grants
      "WebFetch(domain:github.com)",
      "Read(path:/some/specific/path)"
    ],
    "deny": [],
    "ask": []
  }
}
```

Permission format: `ToolName(parameter:pattern)` with glob matching.

### Plugins

> **Note:** There is no documented `installed_plugins.json` file. Plugins are tracked via the `enabledPlugins` key in `settings.json` (see above). The `~/.claude/plugins/` directory is used for plugin cache storage only.

### Project rules (`<project>/.claude/rules/*.md`)

```markdown
---
paths:
  - "src/**/*.ts"
  - "tests/**/*.test.ts"
---

Rule content here. Only loaded when the active file matches one of the
glob patterns in the `paths` frontmatter field.
```

Rules without a `paths` field are loaded unconditionally. Each `.md` file in the directory is a separate rule with optional YAML frontmatter.

### Memory files

**Global memory (`~/.claude/MEMORY.md`):**
Plain Markdown, no frontmatter. Free-form content written by Claude across sessions.

**Project memory files (`~/.claude/projects/<path>/memory/*.md`):**
```markdown
---
name: memory-name
description: one-line description
type: user | project | feedback | reference
---

Memory content here. May include **Why:** and **How to apply:** lines
for feedback and project types.
```

Each memory is a separate `.md` file with YAML frontmatter.

### Skills

**Community skills (`~/.claude/skills/`):**
Flat directory of skill folders. Each skill folder typically contains:
```
skill-name/
├── skill.md              # Skill definition (instructions, trigger patterns)
├── README.md             # Optional description
└── ...                   # Supporting files
```

**Agent skills (`~/.claude/agent-skills/`):**
Git-managed skill sets (e.g., WordPress skills). Structured as:
```
agent-skills/
└── skills/
    └── skill-name/
        └── ...           # Skill documentation and instructions
```

### Project-scoped MCP (`<project>/.mcp.json`)

```jsonc
{
  "mcpServers": {
    "<server-name>": {
      "command": "npx",
      "args": ["-y", "@org/mcp-server@latest"],
      "env": {
        "API_URL": "https://example.com/api",
        "USERNAME": "user",
        "PASSWORD": "plaintext-password"     // ⚠️ Security concern
      }
    }
  }
}
```

Same `mcpServers` schema. Used by both Claude Code and VS Code Copilot. Often contains plaintext credentials — a key target for Naqi's security audit (Phase 3).

---

## Cursor

### Config paths

| File | Path | Purpose |
|------|------|---------|
| Global MCP | `~/.cursor/mcp.json` | MCP server definitions |
| CLI config | `~/.cursor/cli-config.json` | Editor prefs, permissions |
| Project rules | `<project>/.cursor/rules/` | Project rules (`.mdc` or `.md` files with YAML frontmatter) |
| Project MCP | `<project>/.cursor/mcp.json` | Project-scoped MCP |

### MCP config (`~/.cursor/mcp.json`)

```jsonc
{
  "mcpServers": {
    // HTTP/SSE transport — uses "url" key
    "<server-name>": {
      "url": "https://mcp.example.com/mcp",
      "headers": {                           // Optional: request headers
        "Authorization": "Bearer ${env:API_TOKEN}"
      },
      "auth": {                              // Optional: OAuth configuration
        "CLIENT_ID": "my-client-id",
        "CLIENT_SECRET": "${env:CLIENT_SECRET}",
        "scopes": ["read", "write"]
      }
    },

    // stdio transport — uses "command" key
    "<server-name>": {
      "command": "npx -y package-name@latest",
      "env": {},
      "args": [],
      "envFile": ".env"                      // Optional: path to .env file (Cursor-specific)
    }
  }
}
```

**Transport type is inferred** — there is no explicit `type` field:

| Key present | Transport | Example |
|-------------|-----------|---------|
| `"url"` | HTTP/SSE (remote) | `"url": "https://mcp.figma.com/mcp"` |
| `"command"` | stdio | `"command": "npx -y @org/server"` |

The parser must detect which transport is used by checking which key is present.

**Variable interpolation:** Cursor supports `${env:NAME}`, `${userHome}`, `${workspaceFolder}`, `${workspaceFolderBasename}`, and `${pathSeparator}` in config values.

**Hot reload:** Cursor hot-reloads MCP config changes — no restart required.

**Note:** Cursor's stdio format puts the full command as a single string (not split into `command` + `args` like Claude). Some entries do use the split format. Parser must handle both:

```jsonc
// Format A: single command string
{ "command": "npx -y https://api.example.dev/registry.tgz?package=name&version=latest" }

// Format B: split command + args (Claude-compatible)
{ "command": "npx", "args": ["-y", "package-name"], "env": {} }
```

### CLI config (`~/.cursor/cli-config.json`)

```jsonc
{
  "version": 1,
  "editor": {
    "vimMode": false
  },
  "hasChangedDefaultModel": false,
  "permissions": {
    "allow": ["Shell(ls)"],
    "deny": []
  }
}
```

Permission format is similar to Claude Code: `Tool(pattern)`.

### Rules

Project rules live in `.cursor/rules/` with `.mdc` or `.md` extensions. Rules support YAML frontmatter for conditional loading (similar to Claude Code's `.claude/rules/`).

---

## VS Code

### Config paths

| File | Path | Purpose |
|------|------|---------|
| User settings | `~/Library/Application Support/Code/User/settings.json` (macOS) | Editor settings, extension configs, MCP via `mcp.servers` |
| Project MCP | `<project>/.vscode/mcp.json` | Project-scoped MCP servers (dedicated file) |
| Project settings | `<project>/.vscode/settings.json` | Project-scoped settings (can also contain MCP) |

### MCP config (`<project>/.vscode/mcp.json`)

The dedicated MCP config file uses `"servers"` as the top-level key (**not** `"mcpServers"`):

```jsonc
{
  "inputs": [                                // Optional: prompted secrets
    {
      "type": "promptString",
      "id": "api-key",
      "description": "API key for the service",
      "password": true                       // Masks input in UI
    }
  ],
  "servers": {
    // stdio transport (inferred from "command")
    "<server-name>": {
      "command": "npx",
      "args": ["-y", "package-name@latest"],
      "cwd": "${workspaceFolder}",           // Optional: working directory
      "env": {
        "API_KEY": "${input:api-key}"        // References prompted input
      },
      "envFile": "${workspaceFolder}/.env",  // Optional: path to .env file
      "sandboxEnabled": true,                // Optional: sandbox mode
      "sandbox": {                           // Optional: sandbox config
        "allow": ["read"],
        "deny": ["write"]
      }
    },

    // HTTP transport
    "<http-server>": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${input:api-key}"
      }
    },

    // SSE transport
    "<sse-server>": {
      "type": "sse",
      "url": "https://mcp.example.com/sse"
    }
  }
}
```

### MCP in settings.json

MCP servers can also be defined in `settings.json` with the `"mcp"` wrapper key:

```jsonc
{
  "mcp": {
    "servers": {
      "<server-name>": {
        "command": "npx",
        "args": ["-y", "package-name"],
        "env": {
          "KEY": "${input:my-key}"
        }
      }
    }
  },

  // Other VS Code settings
  "claudeCode.selectedModel": "claude-opus-4-6",
  "workbench.colorTheme": "GitHub Dark Default"
}
```

### Parser notes

- VS Code now has **native MCP support** through GitHub Copilot — not just via extensions
- The dedicated MCP file is `.vscode/mcp.json` with `"servers"` as the top-level key
- Supports all three transports: stdio (inferred from `command`), `"type": "http"`, `"type": "sse"`
- **Input prompts:** The `inputs` array allows defining prompted secrets referenced as `${input:id}` — unique to VS Code
- **Predefined variables:** Supports `${workspaceFolder}`, `${userHome}` in config values
- **Hot reload:** VS Code hot-reloads MCP config changes (no restart required)
- The `sync/mcp/` path in user data suggests VS Code is building MCP sync — watch for format changes

---

## Windsurf (Codeium)

### Config paths

| File | Path | Purpose |
|------|------|---------|
| Global config | `~/.codeium/` | Windsurf/Codeium config root |
| MCP config | `~/.codeium/windsurf/mcp_config.json` | MCP server definitions (global only) |

### MCP config (`~/.codeium/windsurf/mcp_config.json`)

```jsonc
{
  "mcpServers": {
    // stdio transport
    "<server-name>": {
      "command": "npx",
      "args": ["-y", "package-name@latest"],
      "env": {
        "API_KEY": "${env:API_KEY}"          // Supports ${env:VARIABLE_NAME} interpolation
      },
      "disabledTools": ["tool-to-skip"]      // Optional: disable specific tools
    },

    // HTTP/SSE transport
    "<remote-server>": {
      "serverUrl": "https://mcp.example.com/mcp",  // Can also use "url"
      "headers": {
        "Authorization": "Bearer ${env:TOKEN}"
      }
    }
  }
}
```

### Parser notes

- Uses `mcpServers` as top-level key (same as Claude Desktop)
- Supports stdio (`command` + `args` + `env`) and HTTP/SSE (`serverUrl` or `url` + `headers`)
- Supports `${env:VARIABLE_NAME}` interpolation syntax
- Supports `disabledTools` field per server to exclude specific tools
- **Global only** — no workspace-level MCP config
- **100 tool limit** across all servers combined
- Lower priority for MVP — smaller user base than Claude/Cursor

---

## GitHub Copilot

### Config path

| OS | Path |
|----|------|
| macOS/Linux | `~/.config/github-copilot/hosts.json` |
| Windows | `%LOCALAPPDATA%\github-copilot\hosts.json` |

### Schema

```jsonc
{
  "github.com": {
    "user": "username",
    "oauth_token": "gho_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
  }
}
```

### Parser notes

- **Auth/detection only** — this file proves GitHub Copilot is installed and authenticated, but does not contain MCP server definitions
- Used by Naqi to detect Copilot as an active AI client in the workspace inventory
- The `oauth_token` is a secret and must be masked during scanning (never stored or sent to APIs)
- No MCP server configuration — Copilot manages its own server connections internally

---

## JetBrains IDEs

### Config path

| Product | Path |
|---------|------|
| IntelliJ IDEA | `~/Library/Application Support/JetBrains/IntelliJIdea<version>/options/mcp.json` |
| WebStorm | `~/Library/Application Support/JetBrains/WebStorm<version>/options/mcp.json` |
| PyCharm | `~/Library/Application Support/JetBrains/PyCharm<version>/options/mcp.json` |
| Other | `~/Library/Application Support/JetBrains/<Product><Version>/options/mcp.json` |

Product directories are **discovered dynamically** — the parser scans all directories matching `~/Library/Application Support/JetBrains/*/options/mcp.json` to find active IDE installations.

### Schema

```jsonc
{
  "mcpServers": {
    "<server-name>": {
      "command": "npx",
      "args": ["-y", "package-name@latest"],
      "env": {
        "API_KEY": "value"
      }
    }
  }
}
```

### Parser notes

- Uses the standard `mcpServers` format (same as Claude Desktop)
- Each JetBrains product version has its own config directory — a machine may have multiple (e.g., `IntelliJIdea2025.1` and `WebStorm2025.2`)
- The parser scans all product directories dynamically, reporting each as a separate client source (e.g., "JetBrains IntelliJ IDEA 2025.1")
- Supports stdio transport only (command + args + env)
- Config file may not exist if the user has not configured MCP servers in that IDE

---

## Zed

### Config path

| OS | Path |
|----|------|
| macOS/Linux | `~/.config/zed/settings.json` |

### Schema

```jsonc
{
  // Zed uses "context_servers" as its MCP key
  "context_servers": {
    "<server-name>": {
      "command": "npx",
      "args": ["-y", "package-name@latest"],
      "env": {
        "API_KEY": "value"
      }
    }
  },

  // Other Zed settings
  "theme": "One Dark",
  "buffer_font_family": "JetBrains Mono"
}
```

### Parser notes

- Uses `context_servers` as the top-level key (unique among all clients)
- The parser wraps `context_servers` entries into the standard `mcpServers` format for unified processing
- Config is embedded in the main `settings.json` — must be extracted without disturbing other Zed settings
- Supports stdio transport (command + args + env)
- The settings file always exists if Zed is installed, but `context_servers` key may be absent

---

## Cross-Client Format Comparison

| Aspect | Claude Desktop | Claude Code | Cursor | VS Code | Windsurf | GitHub Copilot | JetBrains | Zed |
|--------|---------------|-------------|--------|---------|----------|----------------|-----------|-----|
| **MCP key** | `mcpServers` | `mcpServers` | `mcpServers` | `servers` | `mcpServers` | N/A (auth only) | `mcpServers` | `context_servers` |
| **Transport: stdio** | `command` + `args` | `command` + `args` | `command` (string) or `command` + `args` | `command` + `args` | `command` + `args` | N/A | `command` + `args` | `command` + `args` |
| **Transport: HTTP** | Not supported | Supported (`type: "http"`) | Supported (inferred from `url`) | Supported (`type: "http"`) | Supported (`serverUrl` or `url`) | N/A | Not supported | Not supported |
| **Transport: SSE** | Not supported | Supported (`type: "sse"`) | Supported (inferred from `url`) | Supported (`type: "sse"`) | Supported (`serverUrl` or `url`) | N/A | Not supported | Not supported |
| **Variable interpolation** | None | `${VAR}`, `${VAR:-default}` | `${env:NAME}`, `${userHome}`, `${workspaceFolder}` | `${input:id}`, `${workspaceFolder}`, `${userHome}` | `${env:VARIABLE_NAME}` | None | None | None |
| **Input prompts** | None | None | None | `inputs` array with `${input:id}` | None | None | None | None |
| **Env vars** | `env` object | `env` object | `env` object, `envFile` | `env` object, `envFile` | `env` object | N/A | `env` object | `env` object |
| **Secrets storage** | Plaintext in JSON | Plaintext in JSON | Plaintext in JSON | Prompted via `inputs` or plaintext | Plaintext in JSON | OAuth token in JSON | Plaintext in JSON | Plaintext in JSON |
| **Hot reload** | No | No | Yes | Yes | No | N/A | No | No |
| **Memory system** | None | MEMORY.md + project memories | None | None | None | None | None | None |
| **Rules/Skills** | None | `.claude/rules/*.md`, `~/.claude/skills/` | `.cursor/rules/` (`.mdc`/`.md`) | None | None | None | None | None |
| **Plugins** | None | `enabledPlugins` in settings.json | Extensions | Extensions | Extensions | Extensions | Plugins | Extensions |
| **Project-level MCP** | None | `.mcp.json` | `.cursor/mcp.json` | `.vscode/mcp.json` | None (global only) | None | None | None |
| **Tool limits** | None | None | None | None | 100 tools total | None | None | None |
| **Discovery** | Fixed path | Fixed path | Fixed path | Fixed path | Fixed path | Fixed path | Dynamic (scan all product dirs) | Fixed path |

---

## Unified Parser Strategy

Given the format differences, the parser strategy is:

1. **Normalize MCP key:**
   ```
   if "mcpServers" key → use directly (Claude Desktop, Claude Code, Cursor, Windsurf, JetBrains)
   if "servers" key → use directly (VS Code)
   if "context_servers" key → wrap to mcpServers (Zed)
   if hosts.json with oauth_token → auth-only client detection (GitHub Copilot)
   ```

2. **Normalize transport detection:**
   ```
   if explicit "type" field → use it (Claude Code, VS Code)
   if "url" or "serverUrl" key present → Transport::Http (or SSE)
   if "command" key present → Transport::Stdio
   ```

3. **Normalize command format:**
   ```
   if command contains spaces and args is empty → split command into (binary, args)
   if command is single word and args present → use as-is
   ```

4. **Server identity for dedup:**
   Two servers are considered "the same" if:
   - Same transport type AND
   - Same resolved command/binary (after splitting) OR same URL AND
   - Across different clients

5. **JetBrains dynamic discovery:**
   ```
   scan ~/Library/Application Support/JetBrains/*/options/mcp.json
   extract product name and version from directory name
   report each as separate client source
   ```

6. **Env var extraction:**
   - Extract key names always
   - Store masked values for display: `"ghp_abc123xyz" → "ghp_****xyz"`
   - Full values only kept in backup files (never in Naqi's working state)
