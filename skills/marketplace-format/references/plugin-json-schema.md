# plugin.json Schema Reference

Complete schema for the plugin manifest file.

## Location

The manifest must be at `.claude-plugin/plugin.json` relative to plugin root.

## Full Schema

```json
{
  "name": "string (required)",
  "version": "string (semver)",
  "description": "string",
  "author": {
    "name": "string",
    "email": "string",
    "url": "string"
  },
  "homepage": "string (url)",
  "repository": "string (url)",
  "license": "string",
  "keywords": ["string"],
  "commands": "string (path) | [string (paths)]",
  "agents": "string (path) | [string (paths)]",
  "skills": "string (path) | [string (paths)]",
  "hooks": "string (path)",
  "mcpServers": "string (path)"
}
```

## Field Details

### name (required)

Plugin identifier used for namespacing commands and references.

**Requirements:**
- kebab-case format (lowercase with hyphens)
- Unique across installed plugins
- No spaces or special characters
- 2-50 characters

**Examples:**
- Good: `code-review`, `test-runner`, `api-docs`
- Bad: `CodeReview`, `test runner`, `my_plugin`

### version

Semantic versioning format: MAJOR.MINOR.PATCH

**Examples:**
- `1.0.0` - Initial stable release
- `1.2.3` - Minor updates and patches
- `2.0.0` - Breaking changes

### description

Brief explanation of plugin purpose (one sentence).

**Example:**
```json
"description": "Generate and manage API documentation from code comments"
```

### author

Author information object.

```json
"author": {
  "name": "John Doe",
  "email": "john@example.com",
  "url": "https://johndoe.dev"
}
```

All fields are optional but `name` is recommended.

### homepage

URL to plugin documentation or website.

### repository

URL to source code repository.

### license

SPDX license identifier.

**Common values:** `MIT`, `Apache-2.0`, `GPL-3.0`, `ISC`

### keywords

Array of tags for categorization and discovery.

**Example:**
```json
"keywords": ["testing", "automation", "ci-cd", "quality"]
```

### Custom Component Paths

Override default component locations:

```json
{
  "commands": "./custom-commands",
  "agents": ["./agents", "./specialized-agents"],
  "hooks": "./config/hooks.json",
  "mcpServers": "./.mcp.json"
}
```

**Rules:**
- Paths are relative to plugin root
- Must start with `./`
- Arrays supported for multiple locations
- Custom paths supplement defaults (don't replace)

## Minimal Example

```json
{
  "name": "my-plugin"
}
```

## Complete Example

```json
{
  "name": "code-analyzer",
  "version": "1.2.0",
  "description": "Analyze code quality and suggest improvements",
  "author": {
    "name": "Jane Smith",
    "email": "jane@example.com",
    "url": "https://janesmith.io"
  },
  "homepage": "https://code-analyzer.docs.example.com",
  "repository": "https://github.com/janesmith/code-analyzer",
  "license": "MIT",
  "keywords": ["analysis", "quality", "linting", "best-practices"]
}
```

## Validation

To validate plugin.json:

1. Check JSON syntax is valid
2. Verify `name` field exists and follows kebab-case
3. If `version` exists, verify semver format
4. If paths specified, verify they start with `./`
5. Verify referenced paths exist
