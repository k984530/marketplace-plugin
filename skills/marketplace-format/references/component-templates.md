# Component Templates Reference

Templates for creating each type of plugin component.

## Command Template

**Location:** `commands/command-name.md`

```markdown
---
name: command-name
description: Brief description of what the command does
allowed-tools: [Read, Write, Bash, Glob, Grep]
argument-hint: "<required-arg> [optional-arg]"
---

# Command Name

## Purpose

Describe what this command accomplishes.

## Usage

Explain how to use the command with examples.

## Implementation

Instructions for Claude to execute this command:

1. First step
2. Second step
3. Final step

## Notes

Any additional considerations or warnings.
```

### Command Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Command identifier (kebab-case) |
| `description` | Yes | Brief description |
| `allowed-tools` | No | Tools command can use |
| `argument-hint` | No | Usage pattern hint |

## Skill Template

**Location:** `skills/skill-name/SKILL.md`

```markdown
---
name: Skill Name
description: This skill should be used when the user asks to "trigger phrase 1", "trigger phrase 2", "trigger phrase 3". Provides guidance for [domain].
version: 0.1.0
---

# Skill Name

## Overview

Brief description of what knowledge this skill provides.

## Core Concepts

### Concept 1

Explanation of key concept.

### Concept 2

Explanation of another concept.

## Procedures

### Common Task 1

1. Step one
2. Step two
3. Step three

### Common Task 2

Instructions for another common task.

## Best Practices

- Practice 1
- Practice 2
- Practice 3

## Additional Resources

### Reference Files

- **`references/detailed-guide.md`** - Comprehensive documentation
- **`references/examples.md`** - Additional examples

### Example Files

- **`examples/example1.sh`** - Working example
```

### Skill Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Display name |
| `description` | Yes | Third-person with trigger phrases |
| `version` | No | Skill version |

## Agent Template

**Location:** `agents/agent-name.md`

```markdown
---
name: agent-name
model: sonnet
color: blue
tools: [Read, Write, Glob, Grep, Bash]
---

# Agent Name

You are a specialized agent for [purpose].

## Capabilities

- Capability 1
- Capability 2
- Capability 3

## Behavior

Describe how the agent should behave and respond.

## Guidelines

1. Guideline 1
2. Guideline 2
3. Guideline 3

## Output Format

Describe expected output format.
```

### Agent Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Agent identifier |
| `model` | No | Model to use (sonnet, opus, haiku) |
| `color` | No | Display color |
| `tools` | No | Available tools |

### Agent Colors

Available colors: `red`, `orange`, `yellow`, `green`, `blue`, `purple`, `pink`, `gray`

## Hooks Template

**Location:** `hooks/hooks.json`

```json
{
  "PreToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "command",
          "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh",
          "timeout": 30
        }
      ]
    }
  ],
  "PostToolUse": [
    {
      "matcher": "Bash",
      "hooks": [
        {
          "type": "command",
          "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/log.sh"
        }
      ]
    }
  ]
}
```

### Hook Events

| Event | When Triggered |
|-------|----------------|
| `PreToolUse` | Before tool executes |
| `PostToolUse` | After tool executes |
| `Stop` | When agent stops |
| `SubagentStop` | When subagent stops |
| `SessionStart` | When session begins |
| `SessionEnd` | When session ends |
| `UserPromptSubmit` | When user submits prompt |
| `PreCompact` | Before context compaction |
| `Notification` | On notifications |

### Hook Types

- `command` - Execute shell command
- `prompt` - Return prompt to Claude

## MCP Server Template

**Location:** `.mcp.json`

```json
{
  "mcpServers": {
    "server-name": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/servers/server.js"],
      "env": {
        "API_KEY": "${API_KEY}",
        "DEBUG": "false"
      }
    }
  }
}
```

### MCP Server Fields

| Field | Required | Description |
|-------|----------|-------------|
| `command` | Yes | Executable command |
| `args` | No | Command arguments |
| `env` | No | Environment variables |

## Directory Structure Summary

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json           # Required manifest
├── commands/
│   ├── command-one.md
│   └── command-two.md
├── agents/
│   └── agent-name.md
├── skills/
│   └── skill-name/
│       ├── SKILL.md
│       ├── references/
│       └── examples/
├── hooks/
│   ├── hooks.json
│   └── scripts/
├── .mcp.json
├── scripts/
└── README.md
```

## Naming Conventions

| Component | Convention | Example |
|-----------|------------|---------|
| Plugin | kebab-case | `my-plugin` |
| Commands | kebab-case | `run-tests.md` |
| Agents | kebab-case | `code-reviewer.md` |
| Skills | kebab-case directory | `api-testing/` |
| Scripts | kebab-case | `validate-input.sh` |
