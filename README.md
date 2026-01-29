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

### 4. Safe Commit and PR Plugin

Safely commits code changes, runs all tests, checks for security issues, pushes to GitHub, and creates a pull request with comprehensive validation.

**Key Features**: Complete safety workflow with automated checks:
- Never pushes directly to master/main (always uses feature branches)
- Runs complete test suite - all tests must pass 100%
- Checks for security vulnerabilities and code issues
- Blocks push if critical/high security issues found
- Creates detailed COMMIT_ISSUES.md if any problems detected
- Generates comprehensive PR descriptions with test and security status

#### Slash Command: `/safe-commit-pr`

Manually invoke the safe commit and PR workflow:

```bash
/safe-commit-pr
/safe-commit-pr feat(auth): add JWT authentication
/safe-commit-pr fix(api): resolve rate limiting for issue #42
/safe-commit-pr security(db): fix SQL injection vulnerability
```

#### Skill: `safe-commit-pr`

Claude can autonomously use this skill when:
- You ask to commit and push changes
- You request a pull request be created
- You want to ensure tests pass before pushing
- You need security validation before committing
- You mention creating a PR or pushing to GitHub

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

### Safe Commit and PR Plugin

Provides a comprehensive, safe workflow for committing and pushing code:

- **Branch Safety**: Automatically creates feature branch if on master/main, never pushes directly to main branch
- **Test Validation**: Runs complete test suite for all frameworks (npm, pytest, maven, gradle, go, rust, etc.)
- **Test Requirements**: ALL tests must pass (100% pass rate) - any failure blocks the push
- **Security Scanning**: Checks for vulnerabilities, hardcoded secrets, common security issues (SQL injection, XSS, etc.)
- **Security Requirements**: Critical and high severity issues block the push
- **Issue Documentation**: Creates comprehensive `COMMIT_ISSUES.md` when tests fail or security issues found with:
  - Detailed test failure information with error messages and locations
  - Complete security issue analysis with CVEs and remediation steps
  - Specific recommendations and action items
  - File-by-file change analysis
- **Commit Creation**: Generates properly formatted conventional commits with test and security status
- **PR Generation**: Creates detailed pull requests with comprehensive descriptions including test results and security scan status

## Installation

### Option 1: Install from Directory

```bash
# Clone or download this plugin repository
git clone <your-repo-url> tlm-plugin-repo

# Install the plugins in Claude Code
cd tlm-plugin-repo
claude plugin install .
```

This will install all four plugins: upgrade-language, upgrade-springboot, update-vulnerable-dependencies, and safe-commit-pr.

### Option 2: Install from GitHub

```bash
claude plugin install <github-url>
```

All four plugins will be available as slash commands and skills after installation.

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

### Safe Commit and PR Plugin

#### Basic Usage

Invoke the command to safely commit, test, and create a PR:

```bash
/safe-commit-pr
```

The plugin will automatically handle the complete workflow from testing through PR creation.

#### With Commit Type and Description

Specify the type of commit using conventional commit format:

```bash
/safe-commit-pr feat(auth): add JWT authentication
/safe-commit-pr fix(api): resolve rate limiting issue
/safe-commit-pr security(db): fix SQL injection vulnerability
```

#### With GitHub Issue

Link the changes to a GitHub issue:

```bash
/safe-commit-pr fix(validation): correct email regex for issue #42
```

#### What the Plugin Does

1. **Check Current Branch**: Verifies not on master/main, creates feature branch if needed
2. **Review Changes**: Examines all modified files and checks for sensitive data
3. **Run All Tests**: Executes complete test suite for detected framework (npm, pytest, maven, gradle, go, rust, etc.)
4. **Test Validation**: ALL tests must pass (100% success rate) - any failure blocks the push
5. **Security Scanning**: Checks for vulnerabilities, hardcoded secrets, and common security issues
6. **Security Validation**: Critical and high severity issues block the push
7. **Create Issue Report**: If tests fail or security issues found, creates detailed `COMMIT_ISSUES.md` with:
   - Complete test failure information with error messages
   - Detailed security issue analysis with CVEs
   - Specific remediation recommendations
   - No commit or push is performed
8. **Stage and Commit**: Creates properly formatted conventional commit with test and security status
9. **Push to Remote**: Pushes to feature branch (never to master/main)
10. **Create Pull Request**: Generates comprehensive PR with test results and security scan status

#### Safety Guarantees

- **Branch Protection**: Never pushes directly to master or main branch
- **Test Requirements**: All tests must pass - zero failures tolerated
- **Security Requirements**: Critical and high severity issues block the push
- **Detailed Documentation**: COMMIT_ISSUES.md created for any blocking issues
- **Transparency**: All test results and security findings documented in commit and PR

#### When Tests Fail or Security Issues Found

If any tests fail or critical/high security issues are detected:

1. Plugin stops immediately before committing
2. Creates detailed `COMMIT_ISSUES.md` file with:
   - Test failure details (which tests failed, error messages, locations)
   - Security issue details (CVEs, affected code, remediation steps)
   - Specific action items to resolve issues
3. Reports failure to user with clear next steps
4. No commit, push, or PR is created

After fixing the issues, simply run `/safe-commit-pr` again to retry the workflow.

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

### Safe Commit and PR Plugin Process

The plugin follows a structured 9-step safety-first process:

1. **Check Current Branch**: Verifies not on master/main, creates feature branch if needed
2. **Review Changes**: Examines all modified, added, and deleted files, checks for sensitive data
3. **Run All Tests**: Executes complete test suite based on detected framework
   - **MANDATORY**: All tests must pass (100% success rate)
   - **BLOCKS on failure**: Any test failure stops the workflow immediately
4. **Check for Security Issues**: Runs security scanners and checks for common vulnerabilities
   - **MANDATORY**: No critical or high severity issues allowed
   - **BLOCKS on critical/high**: Security issues stop the workflow immediately
5. **Create Explanation File**: If tests fail or security issues found, creates detailed `COMMIT_ISSUES.md`
   - Includes test failure details with error messages and locations
   - Includes security issue details with CVEs and remediation steps
   - Provides specific action items to resolve issues
   - **Workflow stops here if issues found - no commit or push**
6. **Stage and Commit Changes**: Only if all tests pass and no critical security issues
   - Creates properly formatted conventional commit
   - Documents test results and security status in commit message
7. **Push to Remote**: Pushes to feature branch with upstream tracking
8. **Create Pull Request**: Generates comprehensive PR with test and security details
9. **Report Results**: Provides success summary with PR URL or failure details with COMMIT_ISSUES.md reference

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

### COMMIT_ISSUES.md (Safe Commit and PR Plugin)

The safe commit plugin creates `COMMIT_ISSUES.md` when tests fail or security issues are found:

- **Status summary**: Whether workflow is blocked and why
- **Test results**: Complete test execution details with pass/fail counts
- **Failed tests**: Detailed information for each failed test including:
  - Test file and line number
  - Test name and error message
  - Stack trace and location of issue
- **Security issues organized by severity**:
  - Critical issues (BLOCKS push)
  - High severity issues (BLOCKS push)
  - Medium severity issues (WARNING)
  - Low severity issues (INFO)
- **For each security issue**: File location, issue type, risk description, required fix, code examples
- **Files changed**: List of all modified files
- **Blocking issues**: All issues that prevent push to remote
- **Action items**: Specific steps to resolve each issue
- **Next steps**: How to proceed after fixing issues

**Note**: This file is created ONLY when issues are found. If all tests pass and no security issues exist, the workflow proceeds directly to commit and push without creating this file.

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
