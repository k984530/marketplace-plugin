---
name: add-component
description: Add a new component (command, skill, agent, hook, mcp) to an existing plugin
allowed-tools: [Read, Write, Bash, Glob, Grep, AskUserQuestion]
argument-hint: "[component-type] [component-name] [plugin-path]"
---

# Add Component to Plugin

Add a new component to an existing Claude Code plugin.

## Arguments

- `$1`: Component type (command, skill, agent, hook, mcp)
- `$2`: Component name (kebab-case)
- `$3`: Plugin path (optional, defaults to current directory)

## Implementation

### Step 1: Validate Arguments

**Component type ($1):**
If not provided or invalid, ask user:
- command - Slash command
- skill - Knowledge module
- agent - Subagent
- hook - Event handler
- mcp - MCP server

**Component name ($2):**
If not provided, ask user for name.
Validate kebab-case format.

**Plugin path ($3):**
Default to current directory.
Verify it's a valid plugin (has `.claude-plugin/plugin.json`).

### Step 2: Verify Plugin Structure

Check plugin exists:
```bash
test -f "[plugin-path]/.claude-plugin/plugin.json"
```

If not a valid plugin:
- Report error
- Suggest running `/create-plugin` first

### Step 3: Create Component

Based on component type:

---

#### Command

**Directory:** `[plugin-path]/commands/`
**File:** `[name].md`

Create directory if missing:
```bash
mkdir -p [plugin-path]/commands
```

Ask user for command details:
- Description (one sentence)
- Tools needed (Read, Write, Bash, etc.)
- Arguments expected

Generate command file:
```markdown
---
name: [name]
description: [user-provided description]
allowed-tools: [user-selected tools]
argument-hint: "[argument pattern]"
---

# [Name] Command

## Purpose

[Prompt user to describe command purpose]

## Implementation

[Prompt user to describe what command should do]

## Usage

Example: /[name] [arguments]
```

---

#### Skill

**Directory:** `[plugin-path]/skills/[name]/`
**Files:** `SKILL.md`, optionally `references/`, `examples/`

Create directories:
```bash
mkdir -p [plugin-path]/skills/[name]/{references,examples}
```

Ask user for skill details:
- Display name
- Trigger phrases (when should skill load?)
- Main topics covered

Generate SKILL.md:
```markdown
---
name: [Display Name]
description: This skill should be used when the user asks to "[trigger1]", "[trigger2]", "[trigger3]". [Brief description of what skill provides].
version: 0.1.0
---

# [Display Name]

## Overview

[Prompt user for overview]

## Core Concepts

### [Concept 1]

[Description]

### [Concept 2]

[Description]

## Best Practices

- [Practice 1]
- [Practice 2]

## Additional Resources

### Reference Files

- **`references/guide.md`** - Detailed documentation

### Examples

- **`examples/example.md`** - Working example
```

Create placeholder reference file:
```markdown
# [Skill Name] Reference Guide

Detailed documentation goes here.
```

---

#### Agent

**Directory:** `[plugin-path]/agents/`
**File:** `[name].md`

Create directory if missing:
```bash
mkdir -p [plugin-path]/agents
```

Ask user for agent details:
- Agent purpose/role
- Model preference (haiku, sonnet, opus)
- Color (red, orange, yellow, green, blue, purple, pink, gray)
- Tools needed

Generate agent file:
```markdown
---
name: [name]
model: [model]
color: [color]
tools: [tools array]
---

# [Name] Agent

You are a specialized agent for [purpose].

## Capabilities

- [Capability 1]
- [Capability 2]
- [Capability 3]

## Behavior

[Describe expected behavior]

## Guidelines

1. [Guideline 1]
2. [Guideline 2]
3. [Guideline 3]

## Output Format

[Describe expected output format]
```

---

#### Hook

**Directory:** `[plugin-path]/hooks/`
**File:** `hooks.json` (create or update)

Create directory if missing:
```bash
mkdir -p [plugin-path]/hooks
```

Ask user for hook details:
- Event type (PreToolUse, PostToolUse, SessionStart, etc.)
- Matcher pattern (for tool events)
- Hook type (command or prompt)
- Command or prompt content

If `hooks.json` exists, read and merge:
- Add new hook to existing configuration
- Preserve existing hooks

If `hooks.json` doesn't exist, create new:
```json
{
  "[EventType]": [
    {
      "matcher": "[pattern]",
      "hooks": [
        {
          "type": "[command|prompt]",
          "command": "${CLAUDE_PLUGIN_ROOT}/scripts/[script].sh"
        }
      ]
    }
  ]
}
```

If command hook, create script file:
```bash
#!/bin/bash
# Hook script for [name]
# Event: [event-type]

# Script logic here
echo "Hook executed"
```

---

#### MCP Server

**File:** `[plugin-path]/.mcp.json` (create or update)

Ask user for MCP details:
- Server name
- Command (node, python, etc.)
- Script path
- Environment variables needed

If `.mcp.json` exists, read and merge:
- Add new server to existing configuration
- Preserve existing servers

If `.mcp.json` doesn't exist, create new:
```json
{
  "mcpServers": {
    "[server-name]": {
      "command": "[command]",
      "args": ["${CLAUDE_PLUGIN_ROOT}/servers/[script]"],
      "env": {
        "[VAR_NAME]": "${[VAR_NAME]}"
      }
    }
  }
}
```

Create servers directory and placeholder script if needed.

---

### Step 4: Validate Component

After creation, validate:
- File exists and has correct format
- JSON files are valid (hooks.json, .mcp.json)
- Paths use `${CLAUDE_PLUGIN_ROOT}`
- Names follow kebab-case

### Step 5: Report Results

Display summary:
```
Component Created Successfully!

Type: [type]
Name: [name]
Location: [file-path]

Files created:
- [list of files]

Next steps:
1. Edit [file] to customize content
2. Test with: claude --plugin-dir [plugin-path]
3. Try: /[command-name] (for commands)
```

## Component Templates Quick Reference

| Type | Directory | Main File | Extras |
|------|-----------|-----------|--------|
| command | commands/ | [name].md | - |
| skill | skills/[name]/ | SKILL.md | references/, examples/ |
| agent | agents/ | [name].md | - |
| hook | hooks/ | hooks.json | scripts/ |
| mcp | ./ | .mcp.json | servers/ |

## Validation Rules

**All components:**
- Names must be kebab-case
- Use `${CLAUDE_PLUGIN_ROOT}` for internal paths

**Commands:**
- Must have `name` in frontmatter
- `description` recommended

**Skills:**
- Must have `name` and `description` in frontmatter
- Description must use third-person
- Include specific trigger phrases

**Agents:**
- Must have `name` in frontmatter
- `model`, `color`, `tools` recommended

**Hooks:**
- Valid JSON format
- Valid event types
- Executable scripts if type=command

**MCP:**
- Valid JSON format
- Valid command paths
- Document required env vars
