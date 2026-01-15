---
name: convert-plugin
description: Convert an existing plugin to marketplace-compatible format
allowed-tools: [Read, Write, Bash, Glob, Grep, AskUserQuestion]
argument-hint: "[plugin-path-or-github-url]"
---

# Convert Plugin to Marketplace Format

Analyze an existing plugin and convert it to marketplace-compatible format.

## Arguments

- `$1`: Path to existing plugin or GitHub URL (required)

## Implementation

### Step 1: Validate Plugin Path

If `$1` is not provided:
- Ask user for plugin path or GitHub URL using AskUserQuestion

If `$1` is a GitHub URL (starts with `https://github.com/`):
- Clone the repository to a temp directory
- Continue with conversion

Check if path exists:
- If doesn't exist, report error and exit
- If not a directory, report error and exit

### Step 2: Analyze Current Structure

Scan the plugin directory to identify:

**Existing components:**
- Check for `.claude-plugin/plugin.json` (manifest)
- Check for `.claude-plugin/marketplace.json` (marketplace registry)
- Check for `plugin.json` in root (old format)
- Check for `commands/` directory
- Check for `agents/` directory
- Check for `skills/` directory
- Check for `hooks/` or `hooks.json`
- Check for `.mcp.json` or MCP configuration
- Check for `scripts/` directory

**Report findings:**
```
Plugin Analysis:
- Manifest: [found/missing] at [location]
- Marketplace: [found/missing]
- Commands: [count] files found
- Agents: [count] files found
- Skills: [count] directories found
- Hooks: [found/missing]
- MCP: [found/missing]
```

### Step 3: Identify Issues

Check for marketplace compatibility issues:

**Critical plugin.json issues:**
- Missing `.claude-plugin/` directory
- plugin.json in wrong location
- Invalid plugin name (not kebab-case)
- Missing required `name` field
- `repository` is object instead of string
- `author` is string instead of object
- Invalid `commands/agents/skills/hooks` object configs (should be auto-discovered)

**Critical marketplace.json issues:**
- Missing marketplace.json (required for /plugin install)
- `owner` is missing or not an object
- `author` is string instead of object
- `source` is invalid (e.g., `"."`, `"./"`, plain URL string)
- Using `tags` instead of `category`
- Using invalid fields (`installUrl`, `repository`)

**Warnings:**
- Hardcoded paths (should use `${CLAUDE_PLUGIN_ROOT}`)
- Missing version field
- Missing description
- Components in wrong directories
- Non-kebab-case file names

**Report all issues found.**

### Step 4: Gather Required Information

Use AskUserQuestion to collect:

1. **GitHub Repository URL** (if not already known):
   - "What is the GitHub repository URL for this plugin?"
   - Example: `https://github.com/username/plugin-name`

2. **Author name** (if not in plugin.json):
   - "What is your name or organization name?"

3. **Category**:
   - Options: development, productivity, security, learning, testing

4. **Confirm changes**:
   - Show list of proposed changes
   - Ask user to approve

### Step 5: Fix plugin.json

Read existing plugin.json and fix all issues:

**Correct plugin.json format:**
```json
{
  "name": "plugin-name",
  "version": "0.1.0",
  "description": "Plugin description",
  "author": {
    "name": "Author Name",
    "email": "author@example.com"
  },
  "repository": "https://github.com/owner/repo",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"]
}
```

**CRITICAL plugin.json rules:**
- `name`: Required, kebab-case string
- `version`: Recommended, semver string (e.g., "0.1.0")
- `description`: Recommended, string
- `author`: MUST be object `{ "name": "..." }`, NOT string
- `repository`: MUST be string URL, NOT object
- Do NOT include `commands`, `agents`, `skills`, `hooks` fields (auto-discovered)

**Fix these common mistakes:**

❌ Wrong:
```json
"author": "John Doe"
"repository": { "type": "git", "url": "..." }
"commands": { "source": "commands", "discover": true }
```

✅ Correct:
```json
"author": { "name": "John Doe" }
"repository": "https://github.com/owner/repo"
// commands field removed - auto-discovered
```

### Step 6: Create marketplace.json

Create `.claude-plugin/marketplace.json` with correct schema:

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "[plugin-name]-registry",
  "version": "1.0.0",
  "description": "Marketplace registry for [plugin-name]",
  "owner": {
    "name": "[author-name]"
  },
  "plugins": [
    {
      "name": "[plugin-name]",
      "description": "[plugin-description]",
      "version": "[plugin-version]",
      "author": {
        "name": "[author-name]"
      },
      "source": {
        "source": "url",
        "url": "https://github.com/[owner]/[repo].git"
      },
      "category": "[user-selected-category]"
    }
  ]
}
```

**CRITICAL marketplace.json rules:**
- `owner` MUST be object: `{ "name": "..." }`
- `author` MUST be object: `{ "name": "..." }` (NOT string)
- `source` MUST be an **OBJECT** with nested structure:
  ```json
  "source": {
    "source": "url",
    "url": "https://github.com/owner/repo.git"
  }
  ```
- Use `category` (single string), NOT `tags` (array)
- Do NOT use `installUrl`, `repository`, or `tags` fields

**❌ INVALID source formats (DO NOT USE):**
```json
"source": "."
"source": "./"
"source": "https://github.com/owner/repo"
"source": "github:owner/repo"
"source": "owner/repo"
"source": { "source": "github", "repo": "..." }  // Uses SSH, requires auth
```

**✅ ONLY valid source format for public repos:**
```json
"source": {
  "source": "url",
  "url": "https://github.com/owner/repo.git"
}
```

### Step 7: Fix Other Issues

**Fix paths in files:**
- Replace hardcoded paths with `${CLAUDE_PLUGIN_ROOT}`
- Update hooks.json, .mcp.json with portable paths

**Reorganize if needed:**
- Move misplaced files to correct directories
- Rename files to follow kebab-case

### Step 8: Validate Result

After changes, validate:
- `.claude-plugin/plugin.json` exists and is valid JSON
- `.claude-plugin/marketplace.json` exists and is valid JSON
- All component directories at root level
- All paths use `${CLAUDE_PLUGIN_ROOT}`
- File names follow kebab-case

**Validation checklist:**
- [ ] plugin.json has `name` field
- [ ] plugin.json `author` is object (not string)
- [ ] plugin.json `repository` is string (not object)
- [ ] plugin.json has NO `commands/agents/skills/hooks` object configs
- [ ] marketplace.json has `owner` as object
- [ ] marketplace.json has `author` as object
- [ ] marketplace.json `source` uses HTTPS URL object format
- [ ] marketplace.json has `category` (not `tags`)

### Step 9: Report Results

Display summary:
```
Conversion Complete!

Changes applied:
✓ Fixed .claude-plugin/plugin.json
  - Changed author from string to object
  - Changed repository from object to string
  - Removed invalid commands/agents/skills/hooks configs
✓ Created .claude-plugin/marketplace.json
  - Using HTTPS URL source format
  - Category: development

New structure:
plugin-name/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── commands/
├── agents/
└── README.md

Next steps:
1. Review changes: git diff
2. Commit: git add -A && git commit -m "Convert to marketplace format"
3. Push: git push
4. Install: /plugin owner/repo
```

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
