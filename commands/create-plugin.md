---
name: create-plugin
description: Create a new Claude Code plugin in marketplace-compatible format
allowed-tools: [Read, Write, Bash, Glob, Grep, AskUserQuestion]
argument-hint: "[plugin-name] [output-path]"
---

# Create New Plugin

Create a new Claude Code plugin with marketplace-compatible structure.

## Arguments

- `$1`: Plugin name (kebab-case, required)
- `$2`: Output path (optional, defaults to current directory)

## Implementation

### Step 1: Validate Input

If plugin name `$1` is not provided:
- Ask user for plugin name using AskUserQuestion
- Ensure name follows kebab-case format (lowercase, hyphens only)

Validate plugin name:
- Must be kebab-case (e.g., `my-plugin`, `code-analyzer`)
- No spaces, underscores, or special characters
- 2-50 characters

### Step 2: Determine Output Location

Output path: `$2` if provided, otherwise current working directory.

Check if directory already exists:
- If exists and not empty, warn user and ask to confirm overwrite
- If exists and empty, proceed
- If doesn't exist, create it

### Step 3: Ask for Components

Use AskUserQuestion to ask which components to include:

**Question 1: Components**
- Commands (slash commands)
- Skills (knowledge modules)
- Agents (subagents)
- Hooks (event handlers)
- MCP servers (external integrations)

Allow multiple selections.

**Question 2: Plugin Metadata**
Ask for:
- Description (one sentence)
- Author name
- GitHub repository URL (e.g., `https://github.com/username/plugin-name`)
- License (MIT, Apache-2.0, etc.)
- Category (development, productivity, security, learning, testing)

### Step 4: Create Directory Structure

Create the base structure:
```
[plugin-name]/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
└── README.md
```

Add component directories based on user selection:
- If commands selected: `commands/`
- If skills selected: `skills/`
- If agents selected: `agents/`
- If hooks selected: `hooks/` and `scripts/`
- If MCP selected: create `.mcp.json`

### Step 5: Generate plugin.json

Create `.claude-plugin/plugin.json` with:
```json
{
  "name": "[plugin-name]",
  "version": "0.1.0",
  "description": "[user-provided description]",
  "author": {
    "name": "[user-provided author]"
  },
  "repository": "https://github.com/[username]/[plugin-name]",
  "license": "[user-provided license]",
  "keywords": []
}
```

**CRITICAL plugin.json rules:**
- `name`: Required, kebab-case string
- `author`: MUST be object `{ "name": "..." }`, NOT string
- `repository`: MUST be string URL, NOT object
- Do NOT include `commands`, `agents`, `skills`, `hooks` fields (auto-discovered)

### Step 6: Generate README.md

Create README.md with:
- Plugin name and description
- Features list based on selected components
- Installation instructions
- Usage section for each component type
- License information

### Step 7: Generate Example Components

For each selected component type, create an example file:

**Commands**: `commands/hello.md`
```markdown
---
name: hello
description: Example hello command
---

Print a hello message.
```

**Skills**: `skills/example-skill/SKILL.md`
```markdown
---
name: Example Skill
description: This skill should be used when the user asks about "example topic".
version: 0.1.0
---

# Example Skill

Example skill content...
```

**Agents**: `agents/helper.md`
```markdown
---
name: helper
model: haiku
color: green
tools: [Read, Glob, Grep]
---

You are a helper agent...
```

**Hooks**: `hooks/hooks.json`
```json
{
  "SessionStart": [
    {
      "hooks": [
        {
          "type": "command",
          "command": "echo 'Plugin loaded'"
        }
      ]
    }
  ]
}
```

**MCP**: `.mcp.json`
```json
{
  "mcpServers": {}
}
```

### Step 8: Create marketplace.json (Required)

**marketplace.json is REQUIRED for plugins to be installable via `/plugin` command.**

Using the GitHub URL and category from Step 3, create `.claude-plugin/marketplace.json` with **correct schema**:

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "[plugin-name]-registry",
  "version": "1.0.0",
  "description": "Marketplace for [plugin-name]",
  "owner": {
    "name": "[user-provided author]"
  },
  "plugins": [
    {
      "name": "[plugin-name]",
      "description": "[user-provided description]",
      "version": "0.1.0",
      "author": {
        "name": "[user-provided author]"
      },
      "source": {
        "source": "url",
        "url": "https://github.com/[username]/[repo-name].git"
      },
      "category": "[user-selected category]"
    }
  ]
}
```

**CRITICAL marketplace.json rules:**
- `owner` MUST be an object: `{ "name": "..." }`
- `author` MUST be an object: `{ "name": "..." }` (NOT a string)
- `source` MUST be an **OBJECT** with nested structure:
  ```json
  "source": {
    "source": "url",
    "url": "https://github.com/owner/repo.git"
  }
  ```
- Use `category` (single string), NOT `tags` (array)
- Do NOT use `repository`, `installUrl`, or `tags` fields

**❌ INVALID source formats (DO NOT USE):**
```json
"source": "."
"source": "./"
"source": "https://github.com/owner/repo"
"source": "github:owner/repo"
"source": "owner/repo"
```

**✅ ONLY valid source format:**
```json
"source": {
  "source": "url",
  "url": "https://github.com/owner/repo.git"
}
```

**Available categories:**
- `development` - Development tools
- `productivity` - Workflow optimization
- `security` - Security tools
- `learning` - Educational plugins
- `testing` - Test automation

### Step 9: Report Results

Display summary:
- Plugin location
- Created files list
- Next steps:
  1. Create actual commands/skills/agents
  2. Test with `claude --plugin-dir [path]`
  3. Push to GitHub and register with `/plugin` command

## Notes

- Always use `${CLAUDE_PLUGIN_ROOT}` for paths in hooks and MCP configs
- Follow kebab-case naming throughout
- Skill descriptions must use third-person format
- Commands are instructions FOR Claude, not TO user

## Common Mistakes Reference

### plugin.json Mistakes

| Wrong | Correct |
|-------|---------|
| `"author": "John"` | `"author": { "name": "John" }` |
| `"repository": { "type": "git", ... }` | `"repository": "https://..."` |
| `"commands": { "source": "..." }` | Remove field (auto-discovered) |
| `"hooks": { "source": "..." }` | Remove field (auto-discovered) |

### marketplace.json Mistakes

| Wrong | Correct |
|-------|---------|
| `"author": "John"` | `"author": { "name": "John" }` |
| `"source": "."` | `{ "source": "url", "url": "https://..." }` |
| `"source": "https://..."` | `{ "source": "url", "url": "https://..." }` |
| `"tags": [...]` | `"category": "development"` |
| Missing `owner` | `"owner": { "name": "..." }` |
| `"installUrl": "..."` | Use `source` object instead |

## Validation Checklist

Before finishing:
- [ ] plugin.json has `name` field (kebab-case)
- [ ] plugin.json `author` is object (not string)
- [ ] plugin.json `repository` is string (not object)
- [ ] plugin.json has NO `commands/agents/skills/hooks` object configs
- [ ] marketplace.json has `owner` as object
- [ ] marketplace.json has `author` as object
- [ ] marketplace.json `source` uses HTTPS URL object format
- [ ] marketplace.json has `category` (not `tags`)
