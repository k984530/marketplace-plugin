# marketplace.json Schema Reference

Complete schema for marketplace plugin registry.

## Overview

The `marketplace.json` file is the central registry for discovering and installing Claude Code plugins. It must be located at `.claude-plugin/marketplace.json` within the marketplace repository.

## File Location

```
marketplace-repo/
├── .claude-plugin/
│   └── marketplace.json    # Required: Marketplace registry
└── plugins/                # Optional: Plugin directories
    ├── plugin-one/
    └── plugin-two/
```

## Complete Schema

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "marketplace-name",
  "version": "1.0.0",
  "description": "Brief description of this marketplace",
  "owner": {
    "name": "Owner Name",
    "email": "owner@example.com"
  },
  "plugins": [
    {
      "name": "plugin-name",
      "description": "Plugin description",
      "version": "1.0.0",
      "author": {
        "name": "Author Name",
        "email": "author@example.com"
      },
      "source": "./plugins/plugin-name",
      "category": "development"
    }
  ]
}
```

**Important**: See [Plugin sources](#plugin-sources) section for all valid source formats.

## Top-Level Fields

### $schema (recommended)

Schema URL for validation.

```json
"$schema": "https://anthropic.com/claude-code/marketplace.schema.json"
```

### name (required)

Marketplace identifier.

```json
"name": "my-plugin-marketplace"
```

### version (required)

Marketplace version (semver format).

```json
"version": "1.0.0"
```

### description (optional)

Brief description of the marketplace.

### owner (required)

**MUST be an object**, not a string.

```json
"owner": {
  "name": "Owner Name",
  "email": "owner@example.com"
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Owner name or organization |
| `email` | No | Contact email |

### plugins (required)

Array of plugin entries.

## Plugin Entry Fields

### name (required)

Plugin identifier matching the plugin's `plugin.json` name.

**Requirements:**
- Must match plugin.json name exactly
- kebab-case format
- Unique in marketplace

### description (required)

Short description for marketplace listing (1-2 sentences).

### version (optional)

Plugin version (semver format).

### author (required)

**MUST be an object**, not a string.

```json
"author": {
  "name": "Author Name",
  "email": "author@example.com"
}
```

❌ **Wrong:**
```json
"author": "John Doe"
```

✅ **Correct:**
```json
"author": {
  "name": "John Doe"
}
```

### source (required)

Source can be a **string** (relative path) or an **object** (GitHub/Git URL).

#### Format 1: Relative Path (string)

For plugins in the same repository:

```json
"source": "./plugins/my-plugin"
```

**Note**: Must be `./path/to/plugin` format. Cannot use just `.` or `./`.

#### Format 2: GitHub Repository (object)

For plugins in separate GitHub repos:

```json
"source": {
  "source": "github",
  "repo": "owner/repo-name"
}
```

#### Format 3: Git URL (object)

For plugins in GitLab or other git hosts:

```json
"source": {
  "source": "url",
  "url": "https://gitlab.com/team/plugin.git"
}
```

#### Common Mistakes

❌ **Wrong:**
```json
"source": "."                              // Invalid
"source": "./"                             // Invalid
"source": "https://github.com/user/repo"   // Must use object format
```

✅ **Correct:**
```json
"source": "./plugins/plugin-name"          // Relative path
"source": { "source": "github", "repo": "user/repo" }  // GitHub object
```

### category (required)

Single category string for plugin classification.

**Available categories:**
- `development` - Development tools
- `productivity` - Workflow optimization
- `security` - Security tools
- `learning` - Educational plugins
- `testing` - Test automation

❌ **Wrong (using tags array):**
```json
"tags": ["development", "testing"]
```

✅ **Correct (single category):**
```json
"category": "development"
```

## Complete Working Example

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "my-plugin-marketplace",
  "version": "1.0.0",
  "description": "Collection of useful Claude Code plugins",
  "owner": {
    "name": "My Organization"
  },
  "plugins": [
    {
      "name": "code-analyzer",
      "description": "Analyze code quality and suggest improvements",
      "version": "1.0.0",
      "author": {
        "name": "Jane Smith",
        "email": "jane@example.com"
      },
      "source": "./plugins/code-analyzer",
      "category": "development"
    },
    {
      "name": "test-helper",
      "description": "Generate and run tests automatically",
      "version": "0.5.0",
      "author": {
        "name": "John Doe"
      },
      "source": "./plugins/test-helper",
      "category": "testing"
    }
  ]
}
```

## Single Plugin Repository

If your repository contains only one plugin at the root, use GitHub object format:

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "my-plugin-registry",
  "version": "1.0.0",
  "owner": {
    "name": "Your Name"
  },
  "plugins": [
    {
      "name": "my-plugin",
      "description": "Description of my plugin",
      "version": "1.0.0",
      "author": {
        "name": "Your Name"
      },
      "source": {
        "source": "github",
        "repo": "your-username/my-plugin"
      },
      "category": "development"
    }
  ]
}
```

**Note**: You cannot use `"source": "."` - relative paths must be like `./plugins/name`.

## Common Mistakes

### 1. Author as String

❌ **Wrong:**
```json
"author": "John Doe"
```

✅ **Correct:**
```json
"author": { "name": "John Doe" }
```

### 2. Source as Plain URL String

❌ **Wrong:**
```json
"source": "https://github.com/user/repo"
"source": "."
"source": "./"
```

✅ **Correct:**
```json
"source": "./plugins/my-plugin"
"source": { "source": "github", "repo": "user/repo" }
```

### 3. Tags Instead of Category

❌ **Wrong:**
```json
"tags": ["development", "testing"]
```

✅ **Correct:**
```json
"category": "development"
```

### 4. Missing Owner Object

❌ **Wrong:**
```json
{
  "name": "marketplace",
  "plugins": [...]
}
```

✅ **Correct:**
```json
{
  "name": "marketplace",
  "owner": { "name": "Owner Name" },
  "plugins": [...]
}
```

### 5. installUrl or repository

❌ **Wrong (not valid fields):**
```json
"repository": "https://github.com/user/repo",
"installUrl": "https://github.com/user/repo"
```

✅ **Correct:**
```json
"source": "./plugins/my-plugin"
```

## Validation Checklist

Before publishing marketplace:

- [ ] `$schema` field is included
- [ ] `name` is present and kebab-case
- [ ] `version` follows semver
- [ ] `owner` is an object with `name` field
- [ ] Each plugin has `name`, `description`, `author`, `source`, `category`
- [ ] `author` is an object with `name` field (not string)
- [ ] `source` is either:
  - Relative path: `./plugins/name` (not `.` or `./`)
  - GitHub object: `{ "source": "github", "repo": "owner/repo" }`
  - Git URL object: `{ "source": "url", "url": "https://..." }`
- [ ] `category` is a single string value
- [ ] No `tags`, `repository`, or `installUrl` fields

## Adding Marketplace to Claude Code

```bash
# Using GitHub repo
/plugin
# Then enter: owner/repo

# Or use full URL
/plugin
# Then enter: https://github.com/owner/repo
```
