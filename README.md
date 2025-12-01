# Upgrade Language Plugin

A comprehensive Claude Code plugin for upgrading programming language versions with proper analysis, testing, and documentation.

## Overview

This plugin helps you safely upgrade programming language versions (Java, Node.js, Python, Go, Rust, etc.) by following best practices including:

- **Smart Version Selection**: Automatically determines the best target version based on LTS status, support timelines, and project context
- **Comprehensive Analysis**: Evaluates whether to upgrade by checking EOL dates, security vulnerabilities, and compatibility
- **Complete Documentation**: Creates an `UPGRADE_EXPLANATION.md` file documenting all decisions and actions
- **Full Testing**: Runs builds and tests to verify the upgrade works correctly
- **Git Integration**: Creates feature branches, commits, and pull requests with detailed descriptions

## Features

### Slash Command: `/upgrade-language`

Manually invoke the upgrade process with this command:

```bash
/upgrade-language Java 17 to 21
/upgrade-language Node.js 16
/upgrade-language Python 3.9 to 3.11 for issue #42
```

### Skill: `upgrade-language`

Claude can autonomously use this skill when:
- You mention upgrading a language version
- EOL warnings are detected
- Security vulnerabilities in current version are found
- Build or dependency issues suggest a version upgrade might help

## What Gets Updated

The plugin automatically locates and updates:

- **Build Configuration**: `pom.xml`, `package.json`, `go.mod`, `pyproject.toml`, etc.
- **CI/CD Workflows**: GitHub Actions, GitLab CI, CircleCI configurations
- **Docker Images**: Base image versions in Dockerfiles
- **Documentation**: README and other docs
- **Version Managers**: `.tool-versions`, `.nvmrc`, `.python-version`, etc.

## Installation

### Option 1: Install from Directory

```bash
# Clone or download this plugin
git clone <your-repo-url> upgrade-language-plugin

# Install the plugin in Claude Code
cd upgrade-language-plugin
claude plugin install .
```

### Option 2: Install from GitHub

```bash
claude plugin install <github-url>
```

## Usage

### Basic Usage

Simply invoke the command with your language and versions:

```bash
/upgrade-language Java 17 to 21
```

### Autonomous Version Selection

If you don't specify a target version, Claude will analyze and choose the best version:

```bash
/upgrade-language Java 17
```

Claude will:
1. Check if Java 17 is still supported
2. Identify available LTS versions
3. Analyze your project context
4. Recommend the best target version
5. Explain the reasoning

### With GitHub Issue

Link the upgrade to a GitHub issue:

```bash
/upgrade-language Node.js 16 to 20 for issue #123
```

## The Upgrade Process

The plugin follows a structured 9-step process:

1. **Evaluate Whether to Upgrade**: Analyzes support status, EOL dates, and project requirements
2. **Choose Target Version**: Intelligently selects the best version based on LTS status and stability
3. **Create Feature Branch**: Sets up a properly named git branch
4. **Locate All Version References**: Finds every file that needs updating
5. **Update All References**: Modifies build configs, CI/CD, Docker, and docs
6. **Build and Test**: Verifies everything works with the new version
7. **Commit Changes**: Creates a descriptive commit with co-authorship
8. **Push and Create PR**: Opens a pull request with comprehensive documentation
9. **Report Results**: Provides summary of what was done

## UPGRADE_EXPLANATION.md

A unique feature of this plugin is that it creates and maintains an `UPGRADE_EXPLANATION.md` file that documents:

- **Decision rationale**: Why this version was chosen
- **Support timeline analysis**: Current and target version support status
- **All actions taken**: Every file modified and why
- **Build and test results**: Detailed output from verification steps
- **Issues encountered**: Problems found and how they were resolved

This file is included in the commit and referenced in the PR for full transparency.

## Supported Languages

- **Java**: Focuses on LTS versions (8, 11, 17, 21, 25)
- **Node.js**: Prioritizes LTS releases
- **Python**: Checks active support status
- **Go**: Leverages strong backward compatibility
- **Rust**: Follows stable release channel

## Decision Logic

### For LTS-Based Languages (Java, Node.js)

The plugin uses intelligent logic:

1. **Check current version**: Is it EOL soon? Still supported?
2. **Analyze project type**: Production app vs sample/learning project
3. **Look for hints**: Branch name, local environment version
4. **Select version**:
   - Production apps → Most mature LTS
   - Sample projects → Latest stable LTS
   - Respect user hints from branch names

### For Always-Stable Languages (Python, Go, Rust)

- Recommends latest stable version
- Checks active support status
- Avoids bleeding-edge releases unless specified

## Configuration Files Updated

### Java Projects
- `pom.xml` (Maven)
- `build.gradle` (Gradle)
- `.github/workflows/*.yml`
- `Dockerfile`
- `.tool-versions`
- `README.md`

### Node.js Projects
- `package.json`
- `.nvmrc`
- `.github/workflows/*.yml`
- `Dockerfile`
- `README.md`

### Python Projects
- `pyproject.toml`
- `setup.py`
- `.python-version`
- `Pipfile`
- `tox.ini`
- `.github/workflows/*.yml`
- `Dockerfile`

### Go Projects
- `go.mod`
- `.github/workflows/*.yml`
- `Dockerfile`
- `README.md`

## Safety Features

- **No Destructive Operations**: Won't force push or hard reset
- **Comprehensive Testing**: Runs full test suite before committing
- **Issue Documentation**: Records all warnings and deprecations
- **Rollback Friendly**: Clean git history allows easy reversion

## Troubleshooting

Common issues and solutions are documented in the skill file:

- **Tests fail with new version**: Update test dependencies
- **Dependencies incompatible**: Stage the upgrade (dependencies first)
- **CI/CD fails but local works**: Check platform support
- **Deprecated API warnings**: Document and create follow-up issues

## Contributing

Contributions are welcome! Please:

1. Test changes with multiple languages
2. Update documentation
3. Follow the existing code style
4. Add examples for new features

## License

MIT License - See LICENSE file for details

## Support

For issues, questions, or contributions:
- GitHub Issues: <your-repo-url>/issues
- Documentation: See `skills/upgrade-language/SKILL.md` for detailed process

## References

- [Java Support Roadmap](https://www.oracle.com/java/technologies/java-se-support-roadmap.html)
- [Node.js Release Schedule](https://nodejs.org/en/about/releases/)
- [Python Version Status](https://devguide.python.org/versions/)
- [Go Release History](https://go.dev/doc/devel/release)
- [Spring Boot System Requirements](https://docs.spring.io/spring-boot/system-requirements.html)

## Version History

### 1.0.0
- Initial release
- Support for Java, Node.js, Python, Go, Rust
- Autonomous version selection
- UPGRADE_EXPLANATION.md documentation
- Full CI/CD integration
- Comprehensive testing workflow
