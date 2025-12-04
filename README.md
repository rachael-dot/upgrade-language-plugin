# TLM Plugin Repository

A comprehensive collection of Claude Code plugins for version upgrades and security vulnerability management with proper analysis, testing, and documentation.

## Overview

This plugin repository provides tools to safely manage software dependencies and versions following best practices including:

- **Smart Version Selection**: Automatically determines the best target version based on LTS status, support timelines, and project context
- **Security Vulnerability Management**: Identifies and updates dependencies with known CVEs across multiple languages
- **Comprehensive Analysis**: Evaluates upgrades and security risks by checking EOL dates, vulnerabilities, and compatibility
- **Complete Documentation**: Creates detailed documentation files tracking all decisions and actions
- **Full Testing**: Runs builds and tests to verify changes work correctly
- **Git Integration**: Creates feature branches, commits, and pull requests with detailed descriptions

## Plugins

### 1. Upgrade Language Plugin

Upgrades programming language versions (Java, Node.js, Python, Go, Rust, etc.)

#### Slash Command: `/upgrade-language`

Manually invoke the upgrade process:

```bash
/upgrade-language Java 17 to 21
/upgrade-language Node.js 16
/upgrade-language Python 3.9 to 3.11 for issue #42
```

#### Skill: `upgrade-language`

Claude can autonomously use this skill when:
- You mention upgrading a language version
- EOL warnings are detected
- Security vulnerabilities in current version are found
- Build or dependency issues suggest a version upgrade might help

### 2. Upgrade Spring Boot Plugin

Upgrades Spring Boot framework versions using **OpenRewrite** for automated code migration, with comprehensive validation and testing.

**Key Feature**: Uses OpenRewrite recipes to automatically apply Spring Boot migrations including:
- POM version updates
- Package namespace migrations (javax.* → jakarta.*)
- Deprecated API replacements
- Configuration property updates
- Dependency version updates

#### Slash Command: `/upgrade-springboot`

Manually invoke the Spring Boot upgrade process:

```bash
/upgrade-springboot 3.6.0
/upgrade-springboot 3.5.8 for issue #42
```

#### Skill: `upgrade-springboot`

Claude can autonomously use this skill when:
- You mention upgrading Spring Boot
- You need to upgrade to a specific Spring Boot version
- Spring Boot EOL warnings are detected
- Dependencies require a newer Spring Boot version

### 3. Update Vulnerable Dependencies Plugin

Identifies and updates dependencies with known security vulnerabilities across multiple programming languages and package managers.

**Key Feature**: Multi-language security vulnerability management supporting:
- Java (Maven, Gradle)
- JavaScript/TypeScript (npm, yarn, pnpm)
- Python (pip, poetry, pipenv)
- Go, Ruby, Rust, .NET, PHP

#### Slash Command: `/update-vulnerable-dependencies`

Manually invoke the vulnerability update process:

```bash
/update-vulnerable-dependencies
/update-vulnerable-dependencies for issue #42
/update-vulnerable-dependencies --severity=critical,high
```

#### Skill: `update-vulnerable-dependencies`

Claude can autonomously use this skill when:
- Security vulnerabilities are detected in dependencies
- You mention updating vulnerable dependencies
- CVE warnings appear in builds
- You need to perform security updates

## What Gets Updated

### Upgrade Language Plugin

Automatically locates and updates:

- **Build Configuration**: `pom.xml`, `package.json`, `go.mod`, `pyproject.toml`, etc.
- **CI/CD Workflows**: GitHub Actions, GitLab CI, CircleCI configurations
- **Docker Images**: Base image versions in Dockerfiles
- **Documentation**: README and other docs
- **Version Managers**: `.tool-versions`, `.nvmrc`, `.python-version`, etc.

### Upgrade Spring Boot Plugin

Uses **OpenRewrite** to automatically handle:

- **POM Configuration**: Spring Boot parent version in `pom.xml`
- **Package Migrations**: javax.* → jakarta.* (for 2.x → 3.x upgrades)
- **Dependencies**: Spring ecosystem dependencies (spring-data, springdoc, etc.)
- **Configuration**: Application properties and YAML files
- **Deprecated APIs**: Updates code using deprecated Spring Boot APIs
- **Code Transformations**: Applies Spring Boot migration patterns
- **Tests**: Fixes test compatibility issues
- **Documentation**: Creates comprehensive `UPGRADE_SPRING_EXPLANATION.md` with OpenRewrite results

### Update Vulnerable Dependencies Plugin

Automatically handles security vulnerability management:

- **Vulnerability Scanning**: Runs security scanners for all detected package managers
- **CVE Analysis**: Identifies CVEs with CVSS scores and severity levels
- **Dependency Updates**: Updates vulnerable packages across all languages
- **Testing & Verification**: Runs tests and re-scans to confirm fixes
- **Documentation**: Creates comprehensive `VULNERABILITY_REPORT.md` with:
  - Detailed CVE information
  - Severity classification (Critical, High, Medium, Low)
  - Before/after vulnerability comparison
  - Update strategy and prioritization
  - Test results and verification

## Installation

### Option 1: Install from Directory

```bash
# Clone or download this plugin repository
git clone <your-repo-url> tlm-plugin-repo

# Install the plugins in Claude Code
cd tlm-plugin-repo
claude plugin install .
```

This will install all three plugins: upgrade-language, upgrade-springboot, and update-vulnerable-dependencies.

### Option 2: Install from GitHub

```bash
claude plugin install <github-url>
```

All three plugins will be available as slash commands and skills after installation.

## Usage

### Upgrade Language Plugin

#### Basic Usage

Invoke the command with your language and versions:

```bash
/upgrade-language Java 17 to 21
```

#### Autonomous Version Selection

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

#### With GitHub Issue

Link the upgrade to a GitHub issue:

```bash
/upgrade-language Node.js 16 to 20 for issue #123
```

### Upgrade Spring Boot Plugin

#### Basic Usage

Invoke the command with the target Spring Boot version:

```bash
/upgrade-springboot 3.6.0
```

Claude will prompt you for the target version if not specified:
```
What Spring Boot version would you like to upgrade to? (e.g., 3.6.0)
> 3.6.0
```

#### With GitHub Issue

Link the upgrade to a GitHub issue:

```bash
/upgrade-springboot 3.6.0 for issue #123
```

#### What the Plugin Does

1. Detects current Spring Boot version from pom.xml
2. Validates target version compatibility
3. Determines appropriate OpenRewrite recipe for the upgrade
4. Creates comprehensive `UPGRADE_SPRING_EXPLANATION.md` documentation
5. **Executes OpenRewrite migration recipe** to automatically:
   - Update Spring Boot parent version
   - Migrate package namespaces (javax.* → jakarta.*)
   - Replace deprecated APIs
   - Update configuration properties
   - Update Spring ecosystem dependencies
6. Reviews and documents all OpenRewrite changes
7. Resolves remaining dependency conflicts manually
8. Fixes any remaining configuration issues
9. Runs full test suite and fixes failing tests
10. Verifies application functionality
11. Creates detailed pull request with OpenRewrite results

### Update Vulnerable Dependencies Plugin

#### Basic Usage

Invoke the command to scan and update vulnerable dependencies:

```bash
/update-vulnerable-dependencies
```

You can also specify severity levels:
```bash
/update-vulnerable-dependencies --severity=critical,high
```

#### With GitHub Issue

Link the security update to a GitHub issue:

```bash
/update-vulnerable-dependencies for issue #123
```

#### What the Plugin Does

1. Automatically detects all package managers in the project
2. Runs appropriate security scanners (OWASP, npm audit, pip-audit, etc.)
3. Creates comprehensive `VULNERABILITY_REPORT.md` with CVE details
4. Organizes vulnerabilities by severity (Critical, High, Medium, Low)
5. Prioritizes updates based on severity and availability of fixes
6. Updates vulnerable dependencies across all languages
7. Runs full test suite to verify updates
8. Re-scans to confirm vulnerabilities are resolved
9. Documents before/after comparison
10. Creates detailed pull request with security analysis

#### Supported Languages and Tools

- **Java**: Maven (OWASP Dependency-Check), Gradle
- **JavaScript/TypeScript**: npm audit, yarn audit, pnpm audit
- **Python**: pip-audit, safety
- **Go**: govulncheck, nancy
- **Ruby**: bundler-audit
- **Rust**: cargo-audit
- **.NET**: dotnet list package --vulnerable
- **PHP**: composer audit

## The Upgrade Process

### Upgrade Language Plugin Process

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

### Upgrade Spring Boot Plugin Process

The plugin follows a structured 15-step process:

1. **Input Validation and Version Detection**: Prompts for target version and validates compatibility
2. **Initialize Upgrade Documentation**: Creates `UPGRADE_SPRING_EXPLANATION.md`
3. **Analyze Release Notes**: Identifies breaking changes and determines OpenRewrite recipe
4. **Run OpenRewrite Migration Recipe**: Executes automated code migration
5. **Update pom.xml Version (if needed)**: Verifies/updates Spring Boot parent version
6. **Initial Build and Dependency Resolution**: Fixes compilation issues
7. **Update Dependencies**: Updates remaining Spring ecosystem dependencies
8. **Configuration Updates**: Updates remaining configuration properties
9. **Run Comprehensive Tests**: Fixes failing tests after upgrade
10. **Verify Application Functionality**: Tests application startup and endpoints
11. **Code Quality and Security Checks**: Runs static analysis and security scans
12. **Performance Comparison**: Documents performance changes
13. **Finalize Documentation**: Adds executive summary with OpenRewrite results
14. **Create Pull Request**: Pushes changes and creates comprehensive PR
15. **Post-PR Validation**: Updates documentation with PR details

### Update Vulnerable Dependencies Plugin Process

The plugin follows a structured 11-step process:

1. **Identify Project Type**: Detects all package managers in use
2. **Run Vulnerability Scan**: Executes security scanners for each package manager
3. **Analyze Vulnerabilities**: Creates VULNERABILITY_REPORT.md with CVE details
4. **Prioritize Updates**: Categorizes by severity and determines update strategy
5. **Create Feature Branch**: Sets up security update branch
6. **Update Dependencies**: Updates vulnerable packages following priority order
7. **Run Tests**: Executes full test suite and verifies functionality
8. **Re-scan for Vulnerabilities**: Confirms all vulnerabilities are resolved
9. **Update Documentation**: Finalizes VULNERABILITY_REPORT.md with results
10. **Create Pull Request**: Pushes changes and creates comprehensive security PR
11. **Post-Deployment Monitoring**: Documents monitoring requirements

## Upgrade Documentation

### UPGRADE_EXPLANATION.md (Language Plugin)

The language upgrade plugin creates and maintains an `UPGRADE_EXPLANATION.md` file that documents:

- **Decision rationale**: Why this version was chosen
- **Support timeline analysis**: Current and target version support status
- **All actions taken**: Every file modified and why
- **Build and test results**: Detailed output from verification steps
- **Issues encountered**: Problems found and how they were resolved

### UPGRADE_SPRING_EXPLANATION.md (Spring Boot Plugin)

The Spring Boot upgrade plugin creates a comprehensive `UPGRADE_SPRING_EXPLANATION.md` file that documents:

- **Upgrade summary**: Current and target versions, upgrade type
- **Compatibility analysis**: Java version requirements, breaking changes expected
- **OpenRewrite recipe selection**: Which recipe was chosen and why
- **Pre-upgrade state**: Build status, test results, application functionality
- **Breaking changes analysis**: Detailed analysis from release notes
- **OpenRewrite migration results**: All changes made by OpenRewrite including:
  - Recipe executed and execution status
  - Files modified by OpenRewrite
  - POM updates, package migrations, deprecated API replacements
  - Configuration property updates
  - Manual changes still required after OpenRewrite
- **All changes made**: Every file modified (by OpenRewrite and manually) with explanations
- **Build issues encountered**: Every issue with root cause and resolution
- **Test results**: Complete test execution results and fixes
- **Application verification**: Endpoint testing and functionality verification
- **Code quality and security**: Static analysis and security scan results
- **Executive summary**: Final status, metrics, OpenRewrite results, and recommendations

### VULNERABILITY_REPORT.md (Update Vulnerable Dependencies Plugin)

The vulnerable dependencies plugin creates a comprehensive `VULNERABILITY_REPORT.md` file that documents:

- **Scan information**: Date, project name, detected languages and package managers
- **Vulnerability details organized by severity**:
  - Critical (CVSS 9.0-10.0)
  - High (CVSS 7.0-8.9)
  - Medium (CVSS 4.0-6.9)
  - Low (CVSS 0.1-3.9)
- **For each vulnerability**: Package name, current version, fixed version, CVE ID, CVSS score, description
- **Update strategy**: Prioritized list of updates (Priority 1: Immediate, Priority 2: Next Sprint, Priority 3: Backlog)
- **Dependencies with no fix**: Documented workarounds and mitigations
- **Updates applied**: Before/after versions, CVEs resolved, status
- **Test results**: Complete test execution results and verification
- **Remaining issues**: Any unfixed vulnerabilities with mitigation plans

All documentation files are included in commits and referenced in PRs for full transparency.

## Supported Languages and Frameworks

### Languages (Upgrade Language Plugin)

- **Java**: Focuses on LTS versions (8, 11, 17, 21, 25)
- **Node.js**: Prioritizes LTS releases
- **Python**: Checks active support status
- **Go**: Leverages strong backward compatibility
- **Rust**: Follows stable release channel

### Frameworks (Upgrade Spring Boot Plugin)

- **Spring Boot**: All versions (2.x and 3.x)
  - Handles major version upgrades (2.x → 3.x) including Jakarta EE migration
  - Handles minor version upgrades with new features
  - Handles patch upgrades for bug fixes

### Security Tools (Update Vulnerable Dependencies Plugin)

- **Java**: Maven (OWASP Dependency-Check), Gradle (dependencyCheckAnalyze)
- **JavaScript/TypeScript**: npm audit, yarn audit, pnpm audit, Snyk
- **Python**: pip-audit, safety, Snyk
- **Go**: govulncheck (official), nancy
- **Ruby**: bundler-audit
- **Rust**: cargo-audit
- **.NET**: dotnet list package --vulnerable
- **PHP**: composer audit, local-php-security-checker

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
- Documentation:
  - See `skills/upgrade-language/SKILL.md` for detailed language upgrade process
  - See `skills/upgrade-springboot/SKILL.md` for detailed Spring Boot upgrade process
  - See `skills/update-vulnerable-dependencies/SKILL.md` for detailed security vulnerability management process

## References

### Language References

- [Java Support Roadmap](https://www.oracle.com/java/technologies/java-se-support-roadmap.html)
- [Node.js Release Schedule](https://nodejs.org/en/about/releases/)
- [Python Version Status](https://devguide.python.org/versions/)
- [Go Release History](https://go.dev/doc/devel/release)

### Framework References

- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Spring Boot Release Notes](https://github.com/spring-projects/spring-boot/wiki)
- [Spring Boot System Requirements](https://docs.spring.io/spring-boot/system-requirements.html)
- [Spring Boot Migration Guides](https://github.com/spring-projects/spring-boot/wiki#release-notes)

### Security References

- [National Vulnerability Database (NVD)](https://nvd.nist.gov/)
- [CVE Database](https://cve.mitre.org/)
- [Snyk Vulnerability Database](https://security.snyk.io/)
- [GitHub Advisory Database](https://github.com/advisories)
- [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/)

## Version History

### 1.0.0
- Initial release with three plugins:
  - **Upgrade Language Plugin**:
    - Support for Java, Node.js, Python, Go, Rust
    - Autonomous version selection
    - UPGRADE_EXPLANATION.md documentation
    - Full CI/CD integration
    - Comprehensive testing workflow
  - **Upgrade Spring Boot Plugin**:
    - Support for all Spring Boot versions (2.x and 3.x)
    - OpenRewrite integration for automated migrations
    - Comprehensive UPGRADE_SPRING_EXPLANATION.md documentation
    - Automatic dependency resolution
    - Configuration property updates
    - Full test suite validation
    - Application functionality verification
    - PR creation with detailed documentation
  - **Update Vulnerable Dependencies Plugin**:
    - Multi-language security vulnerability management
    - Support for Java, JavaScript/TypeScript, Python, Go, Ruby, Rust, .NET, PHP
    - Automatic vulnerability scanning with industry-standard tools
    - VULNERABILITY_REPORT.md with CVE details and severity classification
    - Prioritized update strategy based on CVSS scores
    - Comprehensive testing and verification
    - Before/after vulnerability comparison
    - PR creation with security analysis
