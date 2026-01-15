# marketplace-plugin

Claude Code 플러그인을 marketplace 호환 형식으로 생성하고 관리하는 플러그인입니다.

Create and manage Claude Code plugins in marketplace-compatible format.

## Features

- **새 플러그인 생성 / Create new plugins**: marketplace 형식의 플러그인 구조를 처음부터 생성
- **기존 플러그인 변환 / Convert existing plugins**: 기존 플러그인을 marketplace 형식으로 변환
- **컴포넌트 추가 / Add components**: 기존 플러그인에 commands, skills, agents, hooks, MCP 추가
- **marketplace.json 자동 등록 / Auto-register**: 생성한 플러그인을 marketplace.json에 자동 추가

## Installation

```bash
# From local directory
claude --plugin-dir /path/to/marketplace-plugin

# Or clone and use
git clone https://github.com/k984530/marketplace-plugin.git
claude --plugin-dir ./marketplace-plugin
```

## Commands

### `/marketplace-plugin:create-plugin`
새로운 플러그인을 생성합니다. 대화형으로 컴포넌트를 선택할 수 있습니다.

Create a new plugin interactively with component selection.

```
/marketplace-plugin:create-plugin my-new-plugin
/marketplace-plugin:create-plugin my-plugin /path/to/output
```

### `/marketplace-plugin:convert-plugin`
기존 플러그인을 marketplace 형식으로 변환합니다.

Convert an existing plugin to marketplace-compatible format.

```
/marketplace-plugin:convert-plugin /path/to/existing-plugin
```

### `/marketplace-plugin:add-component`
기존 플러그인에 새 컴포넌트를 추가합니다.

Add a new component to an existing plugin.

```
/marketplace-plugin:add-component command my-command
/marketplace-plugin:add-component skill my-skill
/marketplace-plugin:add-component agent my-agent
/marketplace-plugin:add-component hook my-hook
/marketplace-plugin:add-component mcp my-server
```

## Components

| Type | Count | Description |
|------|-------|-------------|
| Commands | 3 | create-plugin, convert-plugin, add-component |
| Skills | 1 | marketplace-format (plugin structure knowledge) |
| Agents | 1 | plugin-scaffolder (autonomous plugin creation) |

## Plugin Structure Created

This plugin creates marketplace-compatible structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── commands/                 # Slash commands
├── agents/                   # Subagent definitions
├── skills/                   # Knowledge modules
│   └── skill-name/
│       └── SKILL.md
├── hooks/                    # Event handlers
│   └── hooks.json
├── .mcp.json                # MCP server config
└── README.md
```

## Examples

### Create a new plugin
```
/marketplace-plugin:create-plugin my-awesome-plugin
```
Then answer the interactive questions to select components.

### Convert existing plugin
```
/marketplace-plugin:convert-plugin ./my-old-plugin
```
The plugin will analyze and fix marketplace compatibility issues.

### Add a command to existing plugin
```
/marketplace-plugin:add-component command deploy
```

## Skill: marketplace-format

The plugin includes a skill that provides knowledge about:
- plugin.json schema and required fields
- marketplace.json entry format
- Component templates (commands, skills, agents, hooks, MCP)
- Naming conventions (kebab-case)
- Path references (`${CLAUDE_PLUGIN_ROOT}`)

Trigger the skill by asking about "marketplace plugin format" or "plugin structure".

## Agent: plugin-scaffolder

The plugin-scaffolder agent can autonomously create plugins. Trigger it by:
- "새로운 플러그인을 만들어줘"
- "Create a plugin for code analysis"
- "I need a plugin that helps with testing"

## License

MIT

## Repository

https://github.com/k984530/marketplace-plugin
