---
name: convert-plugin
description: Convert an existing plugin to marketplace-compatible format
allowed-tools: [Read, Write, Bash, Glob, Grep, AskUserQuestion]
argument-hint: "[plugin-path]"
---

# Convert Plugin to Marketplace Format

Analyze an existing plugin and convert it to marketplace-compatible format.

## Arguments

- `$1`: Path to existing plugin (required)

## Implementation

### Step 1: Validate Plugin Path

If `$1` is not provided:
- Ask user for plugin path using AskUserQuestion

Check if path exists:
- If doesn't exist, report error and exit
- If not a directory, report error and exit

### Step 2: Analyze Current Structure

Scan the plugin directory to identify:

**Existing components:**
- Check for `.claude-plugin/plugin.json` (manifest)
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
- Commands: [count] files found
- Agents: [count] files found
- Skills: [count] directories found
- Hooks: [found/missing]
- MCP: [found/missing]
```

### Step 3: Identify Issues

Check for marketplace compatibility issues:

**Critical issues:**
- Missing `.claude-plugin/` directory
- plugin.json in wrong location
- Invalid plugin name (not kebab-case)
- Missing required fields in manifest

**Warnings:**
- Hardcoded paths (should use `${CLAUDE_PLUGIN_ROOT}`)
- Missing version field
- Missing description
- Components in wrong directories
- Non-kebab-case file names

**Report all issues found.**

### Step 4: Propose Fixes

For each issue, propose a fix:

**Manifest issues:**
- Create `.claude-plugin/` if missing
- Move plugin.json to correct location
- Add missing required fields
- Fix plugin name to kebab-case

**Path issues:**
- Replace hardcoded paths with `${CLAUDE_PLUGIN_ROOT}`
- List all files that need updates

**Structure issues:**
- Move components to correct directories
- Rename files to follow conventions

### Step 5: Confirm Changes

Use AskUserQuestion to confirm:
- Show list of proposed changes
- Ask user to approve or customize

Options:
- Apply all changes
- Apply selectively
- Cancel conversion

### Step 6: Apply Changes

If user approves, apply changes:

**Create directories:**
```bash
mkdir -p .claude-plugin
```

**Move/create manifest:**
If plugin.json exists elsewhere, move it:
```bash
mv plugin.json .claude-plugin/plugin.json
```

Otherwise, create new manifest.

**Update manifest fields:**
- Ensure `name` is kebab-case
- Add `version` if missing (default: "0.1.0")
- Add `description` if missing (prompt user)

**Fix paths in files:**
For each file with hardcoded paths:
- Replace absolute paths with `${CLAUDE_PLUGIN_ROOT}`
- Preserve file functionality

**Reorganize components:**
- Move misplaced files to correct directories
- Rename files to follow conventions

### Step 7: Validate Result

After changes, validate:
- `.claude-plugin/plugin.json` exists and is valid JSON
- All component directories at root level
- All paths use `${CLAUDE_PLUGIN_ROOT}`
- File names follow kebab-case

### Step 8: Create or Update marketplace.json

Check if `.claude-plugin/marketplace.json` exists.

**If missing, create it with correct schema:**

Ask user to select category from: development, productivity, security, learning, testing

For GitHub repository source (when plugin path is a GitHub URL like `https://github.com/owner/repo`):
```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "[plugin-name]-registry",
  "version": "1.0.0",
  "owner": {
    "name": "[author-name from plugin.json]"
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
        "url": "[github-https-url].git"
      },
      "category": "[user-selected-category]"
    }
  ]
}
```

For local plugin (ask user for GitHub repo info):
```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "[plugin-name]-registry",
  "version": "1.0.0",
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
        "source": "github",
        "repo": "[owner/repo-name]"
      },
      "category": "[user-selected-category]"
    }
  ]
}
```

**CRITICAL marketplace.json schema rules:**
- `owner` MUST be object: `{ "name": "..." }`
- `author` MUST be object: `{ "name": "..." }` (NOT a string)
- `source` MUST be one of:
  - Relative path: `./plugins/name` (for multi-plugin repos)
  - GitHub object: `{ "source": "github", "repo": "owner/repo" }`
  - URL object: `{ "source": "url", "url": "https://..." }`
- Use `category` (single string), NOT `tags` (array)
- Do NOT use `installUrl`, `repository`, or `tags` fields

**If marketplace.json exists, update entry:**
- Update version, description from plugin.json
- Fix any schema issues (string author → object, invalid source format, etc.)

### Step 9: Report Results

Display summary:
- Changes made
- Files modified
- New structure overview
- Remaining manual tasks (if any)
- Testing instructions

```
Conversion Complete!

Changes applied:
✓ Created .claude-plugin/plugin.json
✓ Created .claude-plugin/marketplace.json
✓ Fixed 3 hardcoded paths
✓ Renamed 2 files to kebab-case

New structure:
plugin-name/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── commands/
├── agents/
└── README.md

Next steps:
1. Review changes with git diff
2. Test plugin: claude --plugin-dir [path]
3. Push to GitHub and install with /plugin command
```

## Validation Checks

Before conversion:
- Verify path is valid directory
- Check for existing .claude-plugin (avoid overwrite)
- Backup recommendation if major changes needed

During conversion:
- Validate JSON before writing
- Preserve original files when renaming
- Handle merge conflicts in manifest

After conversion:
- Run plugin validator if available
- Check all components load correctly

## Edge Cases

**Already marketplace-compatible:**
- Report "Plugin is already marketplace-compatible"
- Suggest minor improvements if any

**Multiple plugin.json files:**
- Ask user which one is the main manifest
- Merge configurations if appropriate

**Complex hook configurations:**
- Carefully update paths
- Preserve hook logic and matchers
- Test hooks after conversion

**MCP server configurations:**
- Update command paths
- Preserve environment variables
- Validate server definitions
