---
name: Marketplace Plugin Format
description: This skill should be used when the user asks about "marketplace plugin format", "plugin.json schema", "marketplace.json structure", "plugin directory structure", "create marketplace plugin", "convert plugin to marketplace format", or needs guidance on creating Claude Code plugins compatible with the marketplace. Provides comprehensive knowledge about marketplace-compatible plugin structure and configuration.
version: 0.1.0
---

# Marketplace Plugin Format

This skill provides knowledge about creating Claude Code plugins in marketplace-compatible format.

## Overview

Claude Code plugins follow a standardized structure for distribution via the marketplace. Understanding this format enables creating well-organized, discoverable, and installable plugins.

## Plugin Directory Structure

Every marketplace-compatible plugin follows this layout:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Required: Plugin manifest
├── commands/                 # Slash commands (.md files)
├── agents/                   # Subagent definitions (.md files)
├── skills/                   # Agent skills (subdirectories with SKILL.md)
├── hooks/
│   └── hooks.json           # Event handler configuration
├── .mcp.json                # MCP server definitions (optional)
├── scripts/                 # Helper scripts
└── README.md                # Plugin documentation
```

**Critical rules:**
1. Manifest MUST be at `.claude-plugin/plugin.json`
2. Component directories MUST be at plugin root (not inside `.claude-plugin/`)
3. Use kebab-case for all directory and file names
4. Only create directories for components actually used

## Plugin Manifest (plugin.json)

### Required Fields

```json
{
  "name": "plugin-name"
}
```

### Recommended Fields

```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Brief explanation of plugin purpose",
  "author": {
    "name": "Author Name",
    "email": "author@example.com",
    "url": "https://example.com"
  },
  "homepage": "https://docs.example.com",
  "repository": "https://github.com/user/plugin-name",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"]
}
```

**Name requirements:** kebab-case, unique, no spaces or special characters

## marketplace.json Entry

To publish a plugin, add an entry to `marketplace.json`:

```json
{
  "plugins": [
    {
      "name": "plugin-name",
      "description": "Brief description for marketplace listing",
      "version": "1.0.0",
      "author": "Author Name",
      "repository": "https://github.com/user/plugin-name",
      "tags": ["category1", "category2"],
      "installUrl": "https://github.com/user/plugin-name"
    }
  ]
}
```

### Required marketplace.json Fields

| Field | Description |
|-------|-------------|
| `name` | Plugin identifier (kebab-case) |
| `description` | Short description for listing |
| `version` | Current version (semver) |
| `author` | Author name or organization |
| `repository` | Source code URL |
| `installUrl` | URL for installation |

### Optional marketplace.json Fields

| Field | Description |
|-------|-------------|
| `tags` | Categories for discovery |
| `homepage` | Documentation URL |
| `license` | License type |

## Component Types

### Commands (`commands/`)

Slash commands as Markdown files with YAML frontmatter:

```markdown
---
name: command-name
description: What the command does
allowed-tools: [Read, Write, Bash]
argument-hint: "<required-arg> [optional-arg]"
---

Command instructions for Claude...
```

### Skills (`skills/skill-name/SKILL.md`)

Knowledge modules with frontmatter:

```markdown
---
name: Skill Name
description: Third-person description with trigger phrases
version: 1.0.0
---

Skill content in imperative form...
```

### Agents (`agents/`)

Subagent definitions:

```markdown
---
name: agent-name
model: sonnet
color: blue
tools: [Read, Write, Glob, Grep, Bash]
---

Agent system prompt and instructions...
```

### Hooks (`hooks/hooks.json`)

Event handlers:

```json
{
  "PreToolUse": [{
    "matcher": "Write|Edit",
    "hooks": [{
      "type": "command",
      "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
    }]
  }]
}
```

### MCP Servers (`.mcp.json`)

External integrations:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/servers/server.js"]
    }
  }
}
```

## Path References

Always use `${CLAUDE_PLUGIN_ROOT}` for portable paths:

```json
"command": "${CLAUDE_PLUGIN_ROOT}/scripts/tool.sh"
```

Never use hardcoded absolute paths or relative paths from working directory.

## Validation Checklist

Before publishing to marketplace:

- [ ] `.claude-plugin/plugin.json` exists with valid name
- [ ] All component directories at root level
- [ ] kebab-case naming throughout
- [ ] `${CLAUDE_PLUGIN_ROOT}` for all internal paths
- [ ] README.md with installation and usage instructions
- [ ] Version follows semver (MAJOR.MINOR.PATCH)

## Additional Resources

### Reference Files

For detailed schemas and examples:
- **`references/plugin-json-schema.md`** - Complete plugin.json schema
- **`references/marketplace-json-schema.md`** - marketplace.json format details
- **`references/component-templates.md`** - Templates for each component type

### Example Files

Working examples in `examples/`:
- **`minimal-plugin/`** - Minimal plugin structure
- **`full-plugin/`** - Complete plugin with all components
