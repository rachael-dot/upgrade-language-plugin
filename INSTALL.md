# Installation Guide

This guide will help you install and start using the Upgrade Language plugin for Claude Code.

## Prerequisites

- Claude Code CLI installed
- Git (optional, for cloning from repository)

## Installation Methods

### Method 1: Install from Local Directory

1. Navigate to the plugin directory:
   ```bash
   cd upgrade-language-plugin
   ```

2. Install the plugin:
   ```bash
   claude plugin install .
   ```

3. Verify installation:
   ```bash
   claude plugin list
   ```

   You should see `upgrade-language` in the list of installed plugins.

### Method 2: Install from GitHub (Future)

Once this plugin is published to a Git repository:

```bash
claude plugin install <github-url>
```

### Method 3: Install from Plugin Marketplace (Future)

Once this plugin is published to the Claude Code plugin marketplace:

```bash
/plugin install upgrade-language
```

## Verify Installation

After installation, verify the plugin is working:

1. Check that the slash command is available:
   ```bash
   /help
   ```

   Look for `/upgrade-language` in the list of available commands.

2. Try using the command:
   ```bash
   /upgrade-language --help
   ```

## Uninstallation

If you need to remove the plugin:

```bash
claude plugin uninstall upgrade-language
```

## Troubleshooting

### Plugin Not Showing Up

1. Check that the plugin directory structure is correct:
   ```
   upgrade-language-plugin/
   ├── .claude-plugin/
   │   └── plugin.json
   ├── commands/
   │   └── upgrade-language.md
   └── skills/
       └── upgrade-language/
           └── SKILL.md
   ```

2. Verify the `plugin.json` is valid JSON:
   ```bash
   cat .claude-plugin/plugin.json | jq .
   ```

3. Check Claude Code debug logs:
   ```bash
   claude --debug
   ```

### Command Not Working

1. Ensure you're in a project directory with a supported language
2. Check that you have necessary permissions to modify files
3. Verify git is configured if you're using git integration features

## Configuration

No additional configuration is needed. The plugin works out of the box with sensible defaults.

## Next Steps

Once installed, see the [README.md](README.md) for usage examples and documentation.

## Getting Help

- Check the [README.md](README.md) for full documentation
- Review the detailed process in `skills/upgrade-language/SKILL.md`
- Open an issue on the GitHub repository for bugs or feature requests
