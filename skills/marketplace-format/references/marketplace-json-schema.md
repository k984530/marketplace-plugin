# marketplace.json Schema Reference

Complete schema for marketplace plugin registry.

## Overview

The `marketplace.json` file is the central registry for discovering and installing Claude Code plugins. Each plugin entry provides metadata for listing and installation.

## File Structure

```json
{
  "plugins": [
    {
      "name": "plugin-name",
      "description": "Brief description",
      "version": "1.0.0",
      "author": "Author Name",
      "repository": "https://github.com/user/plugin",
      "installUrl": "https://github.com/user/plugin",
      "tags": ["category1", "category2"],
      "homepage": "https://docs.example.com",
      "license": "MIT"
    }
  ]
}
```

## Required Fields

### name

Plugin identifier matching the plugin's `plugin.json` name.

**Requirements:**
- Must match plugin.json name exactly
- kebab-case format
- Unique in marketplace

### description

Short description for marketplace listing (1-2 sentences).

**Guidelines:**
- Clear and concise
- Describe what the plugin does
- Mention key features
- 50-150 characters recommended

### version

Current published version (semver format).

**Example:** `1.0.0`, `2.1.3`

### author

Author name or organization.

**Examples:**
- `"John Doe"`
- `"Acme Corp"`
- `"Claude Code Team"`

### repository

URL to source code repository.

**Supported formats:**
- GitHub: `https://github.com/user/repo`
- GitLab: `https://gitlab.com/user/repo`
- Bitbucket: `https://bitbucket.org/user/repo`

### installUrl

URL used for plugin installation.

Usually same as repository URL. Can be different for:
- npm packages: `npm:package-name`
- Custom registries
- Direct download URLs

## Optional Fields

### tags

Array of categories for discovery and filtering.

**Common tags:**
- `development` - Development tools
- `testing` - Test automation
- `documentation` - Doc generation
- `git` - Git workflows
- `ci-cd` - CI/CD integration
- `code-quality` - Linting and analysis
- `productivity` - Workflow optimization

**Example:**
```json
"tags": ["development", "testing", "automation"]
```

### homepage

URL to documentation or project website.

### license

SPDX license identifier.

## Complete Entry Example

```json
{
  "name": "test-runner-pro",
  "description": "Advanced test runner with parallel execution, coverage reports, and CI integration",
  "version": "2.3.1",
  "author": "DevTools Inc",
  "repository": "https://github.com/devtools/test-runner-pro",
  "installUrl": "https://github.com/devtools/test-runner-pro",
  "tags": ["testing", "ci-cd", "automation", "coverage"],
  "homepage": "https://test-runner-pro.dev",
  "license": "MIT"
}
```

## Adding a New Plugin

To add a plugin to marketplace:

1. **Locate marketplace.json** in the marketplace repository
2. **Add entry** to the `plugins` array
3. **Include all required fields**
4. **Add relevant tags** for discoverability
5. **Submit PR** to marketplace repository

## Validation Checklist

Before submitting to marketplace:

- [ ] `name` matches plugin.json exactly
- [ ] `description` is clear and concise
- [ ] `version` follows semver
- [ ] `repository` URL is accessible
- [ ] `installUrl` works for installation
- [ ] Tags are relevant and accurate
- [ ] No duplicate entries in marketplace
- [ ] Plugin passes validation tests

## Search and Discovery

Users find plugins via:

1. **Name search** - Exact or partial name match
2. **Tag filtering** - Filter by category tags
3. **Description search** - Keyword search in descriptions
4. **Author search** - Find plugins by author

Optimize discoverability by:
- Using descriptive plugin name
- Writing clear description with keywords
- Adding relevant tags
- Including homepage for documentation
