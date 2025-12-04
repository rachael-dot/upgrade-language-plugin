---
name: update-vulnerable-dependencies
description: Search for and update dependencies with known security vulnerabilities across multiple languages and package managers
---

# Update Vulnerable Dependencies

A comprehensive, language-agnostic guide for identifying, analyzing, and updating dependencies with known security vulnerabilities across different programming languages and package managers.

## Overview

This skill supports vulnerability management for:
- **Java**: Maven, Gradle
- **JavaScript/TypeScript**: npm, yarn, pnpm
- **Python**: pip, poetry, pipenv
- **Go**: go modules
- **Ruby**: bundler
- **Rust**: cargo
- **.NET**: NuGet
- **PHP**: composer

## Step 1: Identify Project Type and Package Manager

### Automatic Detection

Scan the project to identify which package managers are in use:

```bash
# Check for various package manager files
ls -la | grep -E "pom.xml|build.gradle|package.json|requirements.txt|pyproject.toml|go.mod|Gemfile|Cargo.toml|*.csproj|composer.json"
```

### Package Manager Files

| Language | Package Manager | File(s) |
|----------|----------------|---------|
| Java | Maven | `pom.xml` |
| Java | Gradle | `build.gradle`, `build.gradle.kts` |
| JavaScript/TypeScript | npm/yarn/pnpm | `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml` |
| Python | pip | `requirements.txt`, `requirements.in` |
| Python | Poetry | `pyproject.toml`, `poetry.lock` |
| Python | Pipenv | `Pipfile`, `Pipfile.lock` |
| Go | Go Modules | `go.mod`, `go.sum` |
| Ruby | Bundler | `Gemfile`, `Gemfile.lock` |
| Rust | Cargo | `Cargo.toml`, `Cargo.lock` |
| .NET | NuGet | `*.csproj`, `packages.config` |
| PHP | Composer | `composer.json`, `composer.lock` |

## Step 2: Run Vulnerability Scan

### Java (Maven)

```bash
# Using Maven Dependency Check
mvn org.owasp:dependency-check-maven:check

# Using Maven Versions Plugin
mvn versions:display-dependency-updates

# Using Snyk (if available)
snyk test --file=pom.xml

# Check for outdated dependencies
mvn versions:display-dependency-updates
```

**Output Location**: `target/dependency-check-report.html`

### Java (Gradle)

```bash
# Using Gradle Dependency Check
./gradlew dependencyCheckAnalyze

# Check for outdated dependencies
./gradlew dependencyUpdates

# Using Snyk
snyk test --file=build.gradle
```

### JavaScript/TypeScript (npm)

```bash
# Built-in npm audit
npm audit

# Get detailed report
npm audit --json > npm-audit.json

# Check for outdated packages
npm outdated

# Using Snyk
snyk test

# Using yarn
yarn audit

# Using pnpm
pnpm audit
```

### Python (pip)

```bash
# Using pip-audit
pip-audit

# Using safety
safety check

# Using Snyk
snyk test --file=requirements.txt

# Check for outdated packages
pip list --outdated
```

### Python (Poetry)

```bash
# Check for vulnerabilities
poetry audit

# Using Snyk
snyk test --file=pyproject.toml

# Check for outdated packages
poetry show --outdated
```

### Go

```bash
# Using govulncheck (official Go vulnerability scanner)
govulncheck ./...

# Using nancy (Sonatype)
go list -json -m all | nancy sleuth

# Check for outdated modules
go list -u -m all
```

### Ruby

```bash
# Using bundler-audit
bundle audit check --update

# Check for outdated gems
bundle outdated

# Using Snyk
snyk test --file=Gemfile.lock
```

### Rust

```bash
# Using cargo-audit
cargo audit

# Check for outdated crates
cargo outdated
```

### .NET

```bash
# Using dotnet list package
dotnet list package --vulnerable

# Include transitive dependencies
dotnet list package --vulnerable --include-transitive

# Check for outdated packages
dotnet list package --outdated
```

### PHP (Composer)

```bash
# Using composer audit
composer audit

# Check for outdated packages
composer outdated

# Using local-php-security-checker
local-php-security-checker
```

## Step 3: Analyze Vulnerability Report

### Create Vulnerability Summary Document

Create a file `VULNERABILITY_REPORT.md` to track findings:

```markdown
# Dependency Vulnerability Report

**Scan Date**: [Date]
**Project**: [Project Name]
**Language/Package Manager**: [Language]

## Critical Vulnerabilities (CVSS 9.0-10.0)

| Package | Current Version | Fixed Version | CVE | CVSS Score | Description |
|---------|----------------|---------------|-----|------------|-------------|
| [name] | [version] | [version] | CVE-XXXX-XXXXX | 9.5 | [description] |

## High Vulnerabilities (CVSS 7.0-8.9)

| Package | Current Version | Fixed Version | CVE | CVSS Score | Description |
|---------|----------------|---------------|-----|------------|-------------|

## Medium Vulnerabilities (CVSS 4.0-6.9)

| Package | Current Version | Fixed Version | CVE | CVSS Score | Description |
|---------|----------------|---------------|-----|------------|-------------|

## Low Vulnerabilities (CVSS 0.1-3.9)

| Package | Current Version | Fixed Version | CVE | CVSS Score | Description |
|---------|----------------|---------------|-----|------------|-------------|

## Update Strategy

### Priority 1: Critical and High (Immediate)
- [List packages to update immediately]

### Priority 2: Medium (Next Sprint)
- [List packages to update soon]

### Priority 3: Low (Backlog)
- [List packages for future updates]

## Dependencies with No Fix Available
- [List any vulnerabilities without patches]
- [Note required workarounds or mitigations]
```

### Severity Guidelines

**Critical (CVSS 9.0-10.0)**:
- Remote code execution
- Authentication bypass
- Data exfiltration
- **Action**: Update immediately, even if breaking changes

**High (CVSS 7.0-8.9)**:
- Privilege escalation
- SQL injection
- Cross-site scripting (XSS)
- **Action**: Update within 1 week

**Medium (CVSS 4.0-6.9)**:
- Denial of service
- Information disclosure
- **Action**: Update within 1 month

**Low (CVSS 0.1-3.9)**:
- Minor issues with limited impact
- **Action**: Update during regular maintenance

## Step 4: Prioritize Updates

### Decision Matrix

Use this to decide update strategy:

```
IF (Vulnerability is Critical OR High) AND (Fix Available):
  → Update immediately, even with breaking changes

ELSE IF (Vulnerability is Critical OR High) AND (No Fix Available):
  → Implement workarounds/mitigations
  → Consider alternative packages
  → Document risk acceptance if necessary

ELSE IF (Vulnerability is Medium) AND (Direct Dependency):
  → Update in next sprint

ELSE IF (Vulnerability is Low) OR (Transitive Dependency with Low Risk):
  → Schedule for regular maintenance
```

### Check for Breaking Changes

Before updating, review the changelog:

```bash
# For npm packages
npm view <package-name> versions
npm view <package-name>@<version> --json

# For Maven packages (check on Maven Central)
# https://mvnrepository.com/artifact/<groupId>/<artifactId>

# For Python packages
pip show <package-name>
```

Look for:
- Major version bumps (semantic versioning: MAJOR.MINOR.PATCH)
- BREAKING CHANGE notes in changelogs
- Migration guides
- Deprecated features

## Step 5: Update Dependencies

### Create Feature Branch

```bash
# Create descriptive branch
git checkout -b security/update-vulnerable-dependencies-$(date +%Y-%m-%d)
```

### Java (Maven)

**Update Single Dependency:**
```xml
<!-- In pom.xml, update version -->
<dependency>
    <groupId>com.example</groupId>
    <artifactId>vulnerable-package</artifactId>
    <version>1.2.3</version> <!-- Update to 1.2.4 -->
</dependency>
```

**Update Using Versions Plugin:**
```bash
# Update single dependency
mvn versions:use-latest-versions -Dincludes=com.example:vulnerable-package

# Update all dependencies to latest releases
mvn versions:use-latest-releases

# Update parent POM
mvn versions:update-parent
```

### Java (Gradle)

```groovy
// In build.gradle, update version
dependencies {
    implementation 'com.example:vulnerable-package:1.2.4' // Updated from 1.2.3
}
```

```bash
# Apply dependency updates plugin
./gradlew useLatestVersions
```

### JavaScript/TypeScript (npm)

**Update Single Package:**
```bash
# Update to specific version
npm install <package-name>@<version>

# Update to latest version
npm install <package-name>@latest

# Update to latest within semver range
npm update <package-name>
```

**Update Multiple Packages:**
```bash
# Update all packages within semver constraints
npm update

# Update package.json to latest versions
npx npm-check-updates -u
npm install

# Fix vulnerabilities automatically (may cause breaking changes)
npm audit fix

# Fix only non-breaking vulnerabilities
npm audit fix --only=prod
```

### Python (pip)

```bash
# Update single package
pip install --upgrade <package-name>

# Update to specific version
pip install <package-name>==<version>

# Update all packages in requirements.txt
pip install --upgrade -r requirements.txt

# Generate new requirements.txt
pip freeze > requirements.txt
```

### Python (Poetry)

```bash
# Update single package
poetry update <package-name>

# Update all packages
poetry update

# Update to specific version
poetry add <package-name>@^<version>
```

### Go

```bash
# Update single module
go get <module-path>@latest

# Update to specific version
go get <module-path>@v1.2.3

# Update all direct dependencies
go get -u ./...

# Update all dependencies (including indirect)
go get -u all

# Tidy up go.mod and go.sum
go mod tidy
```

### Ruby

```bash
# Update single gem
bundle update <gem-name>

# Update all gems
bundle update

# Update to specific version (edit Gemfile first)
gem '<gem-name>', '~> 1.2.3'
bundle install
```

### Rust

```bash
# Update single crate
cargo update -p <crate-name>

# Update all crates
cargo update

# Update to specific version (edit Cargo.toml first)
cargo update
```

### .NET

```bash
# Update single package
dotnet add package <PackageName> --version <version>

# Update all packages in a project
dotnet list package --outdated | grep ">" | awk '{print $2}' | xargs -I {} dotnet add package {}
```

### PHP (Composer)

```bash
# Update single package
composer update <vendor/package>

# Update all packages
composer update

# Update to specific version
composer require <vendor/package>:<version>
```

## Step 6: Test After Updates

### Run Full Test Suite

```bash
# Java (Maven)
mvn clean test

# Java (Gradle)
./gradlew clean test

# JavaScript/TypeScript
npm test

# Python
pytest
# or
python -m unittest discover

# Go
go test ./...

# Ruby
bundle exec rspec
# or
bundle exec rake test

# Rust
cargo test

# .NET
dotnet test

# PHP
./vendor/bin/phpunit
```

### Run Integration Tests

```bash
# Run integration tests if available
npm run test:integration
mvn verify
./gradlew integrationTest
```

### Manual Testing

1. **Build the application**:
   ```bash
   # Java
   mvn clean package

   # Node.js
   npm run build

   # Python
   python setup.py build

   # Go
   go build ./...
   ```

2. **Start the application**:
   ```bash
   # Spring Boot
   mvn spring-boot:run

   # Node.js
   npm start

   # Python
   python app.py
   ```

3. **Verify core functionality**:
   - Test main user flows
   - Check API endpoints
   - Verify database connections
   - Test authentication/authorization
   - Check external integrations

### Check for Runtime Warnings

Look for:
- Deprecation warnings
- Security warnings
- Compatibility issues
- Performance degradation

## Step 7: Re-scan for Vulnerabilities

After updates, verify vulnerabilities are resolved:

```bash
# Java (Maven)
mvn org.owasp:dependency-check-maven:check

# Java (Gradle)
./gradlew dependencyCheckAnalyze

# JavaScript/TypeScript
npm audit

# Python
pip-audit
# or
safety check

# Go
govulncheck ./...

# Ruby
bundle audit check

# Rust
cargo audit

# .NET
dotnet list package --vulnerable

# PHP
composer audit
```

### Verify No New Vulnerabilities

Ensure that updating dependencies didn't introduce new vulnerabilities.

## Step 8: Update Documentation

### Update VULNERABILITY_REPORT.md

Add resolution notes:

```markdown
## Updates Applied

| Package | Old Version | New Version | CVE Resolved | Status |
|---------|-------------|-------------|--------------|--------|
| [name] | [old] | [new] | CVE-XXXX-XXXXX | ✅ Resolved |

## Test Results
- [x] All unit tests pass (X tests, 0 failures)
- [x] Integration tests pass
- [x] Manual testing completed
- [x] No new vulnerabilities introduced
- [x] Application starts successfully

## Remaining Issues
[List any vulnerabilities that could not be fixed]
```

### Update Project Documentation

If there are breaking changes that affect usage:

```markdown
# In README.md or CHANGELOG.md

## [Date] Dependency Updates

### Breaking Changes
- **[Package Name]**: Updated from vX.X.X to vY.Y.Y
  - Changes required: [description]
  - Migration guide: [link]

### Security Updates
- Fixed CVE-XXXX-XXXXX in [package-name]
- Fixed CVE-XXXX-XXXXX in [package-name]
```

## Step 9: Commit Changes

### Review Changes

```bash
# Check what changed
git status
git diff

# For lock files, check significant changes only
git diff package-lock.json
git diff pom.xml
```

### Stage Files

```bash
# Stage dependency files
git add pom.xml                    # Maven
git add build.gradle               # Gradle
git add package.json package-lock.json  # npm
git add requirements.txt           # Python pip
git add pyproject.toml poetry.lock # Python Poetry
git add go.mod go.sum             # Go
git add Gemfile Gemfile.lock      # Ruby
git add Cargo.toml Cargo.lock     # Rust
git add *.csproj                  # .NET
git add composer.json composer.lock # PHP

# Stage documentation
git add VULNERABILITY_REPORT.md
git add README.md CHANGELOG.md
```

### Create Commit

```bash
git commit -m "$(cat <<'EOF'
security: update dependencies with known vulnerabilities

Security updates to address known vulnerabilities:

Critical/High Severity Fixed:
- [Package Name]: CVE-XXXX-XXXXX (CVSS X.X) - [Brief Description]
- [Package Name]: CVE-XXXX-XXXXX (CVSS X.X) - [Brief Description]

Medium/Low Severity Fixed:
- [Package Name]: CVE-XXXX-XXXXX (CVSS X.X)
- [Package Name]: CVE-XXXX-XXXXX (CVSS X.X)

Changes:
- Updated [package] from vX.X.X to vY.Y.Y
- Updated [package] from vX.X.X to vY.Y.Y

Testing:
- All unit tests pass (X tests, 0 failures)
- Integration tests pass
- Manual smoke testing completed
- Re-scan confirms vulnerabilities resolved

See VULNERABILITY_REPORT.md for detailed analysis.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

## Step 10: Push and Create Pull Request

### Push Branch

```bash
git push -u origin security/update-vulnerable-dependencies-$(date +%Y-%m-%d)
```

### Create Pull Request

```bash
gh pr create --title "security: update dependencies with known vulnerabilities" --body "$(cat <<'EOF'
## Summary
This PR updates dependencies with known security vulnerabilities identified in the latest security scan.

## Security Fixes

### Critical/High Severity (Immediate Action Required)
- **[Package Name]** (vX.X.X → vY.Y.Y)
  - CVE: CVE-XXXX-XXXXX
  - CVSS Score: X.X (Critical/High)
  - Description: [Brief description of vulnerability]
  - Impact: [What could be exploited]

### Medium/Low Severity
- **[Package Name]** (vX.X.X → vY.Y.Y)
  - CVE: CVE-XXXX-XXXXX
  - CVSS Score: X.X (Medium/Low)

## Changes

### Direct Dependencies Updated
- `[package-name]`: vX.X.X → vY.Y.Y

### Transitive Dependencies Updated
- `[package-name]`: vX.X.X → vY.Y.Y (via [parent-package])

## Breaking Changes
- [ ] No breaking changes
- [ ] Breaking changes (see below)

[If breaking changes, describe them here]

## Test Plan
- [x] All unit tests pass (X tests, 0 failures)
- [x] Integration tests pass
- [x] Manual smoke testing completed
- [x] Application starts successfully
- [x] Core functionality verified
- [x] Re-scanned for vulnerabilities (0 critical/high remaining)

## Vulnerability Scan Results

### Before Update
```
Critical: X
High: Y
Medium: Z
Low: W
```

### After Update
```
Critical: 0
High: 0
Medium: [number if any remain]
Low: [number if any remain]
```

## Documentation
- [x] VULNERABILITY_REPORT.md updated with detailed findings
- [x] CHANGELOG.md updated (if applicable)
- [x] README.md updated (if breaking changes)

## Risk Assessment
**Risk Level**: [Low/Medium/High]

**Rationale**: [Why this update is safe/necessary]

## Rollback Plan
If issues arise:
1. Revert this PR
2. [Any specific rollback steps]

## Follow-up Actions
- [ ] [Any remaining vulnerabilities to address]
- [ ] [Any monitoring to set up]
- [ ] [Any additional testing needed post-deployment]

## References
- [Link to CVE database entries]
- [Link to package changelogs]
- [Link to migration guides if applicable]

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

## Step 11: Monitor Post-Deployment

### Set Up Monitoring

After deployment, monitor for:

1. **Application Health**:
   - Error rates
   - Response times
   - Crash reports

2. **Security Alerts**:
   - New vulnerabilities in updated packages
   - Runtime security warnings

3. **Performance**:
   - Memory usage
   - CPU usage
   - Database query performance

### Schedule Regular Scans

Set up automated vulnerability scanning:

```yaml
# Example: GitHub Actions workflow (.github/workflows/security-scan.yml)
name: Security Scan

on:
  schedule:
    - cron: '0 0 * * 1'  # Weekly on Mondays
  workflow_dispatch:

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # For npm
      - name: Run npm audit
        run: npm audit

      # For Maven
      - name: Run OWASP Dependency Check
        run: mvn org.owasp:dependency-check-maven:check

      # For Python
      - name: Run pip-audit
        run: pip install pip-audit && pip-audit
```

## Common Issues and Solutions

### Issue: Update causes build failures

**Solution:**
1. Review error messages carefully
2. Check for API changes in package changelog
3. Update code to use new APIs
4. Consider updating in smaller increments
5. If persistent, roll back and investigate alternatives

### Issue: Tests fail after update

**Solution:**
1. Check if test framework needs updating
2. Review test assertions for changes in behavior
3. Check for timing issues or race conditions
4. Update mocks/stubs if interfaces changed
5. Consider if tests were relying on buggy behavior

### Issue: Transitive dependency conflict

**Solution:**
```bash
# Maven: Use dependency management
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>com.example</groupId>
      <artifactId>conflicting-package</artifactId>
      <version>1.2.3</version>
    </dependency>
  </dependencies>
</dependencyManagement>

# npm: Use resolutions in package.json
"resolutions": {
  "conflicting-package": "1.2.3"
}

# yarn: Use resolutions
"resolutions": {
  "conflicting-package": "1.2.3"
}
```

### Issue: No fix available for critical vulnerability

**Solutions:**
1. **Find alternative package**: Look for maintained alternatives
2. **Apply workarounds**:
   - Disable vulnerable functionality if not used
   - Add security middleware/wrappers
   - Apply input validation
3. **Patch locally**:
   - Fork the package and apply fix
   - Use patch-package (npm) to apply patches
4. **Document risk acceptance**:
   - If no other option, document the risk
   - Get security team approval
   - Implement additional monitoring

### Issue: Update breaks production behavior

**Solution:**
1. Have a rollback plan ready
2. Use feature flags for gradual rollout
3. Deploy to staging first
4. Run smoke tests in production
5. Have on-call team ready during deployment

### Issue: Major version update required

**Solution:**
1. Review migration guide thoroughly
2. Create separate branch for major update
3. Update incrementally (if possible, update minor versions first)
4. Allocate sufficient testing time
5. Consider if the update can wait for next major release cycle

## Best Practices

### Regular Maintenance

1. **Schedule weekly scans**: Don't wait for security alerts
2. **Update dependencies regularly**: Small updates are easier than large jumps
3. **Monitor security advisories**: Subscribe to security mailing lists
4. **Keep lock files committed**: Ensures reproducible builds

### Update Strategy

1. **Test in staging first**: Never update directly in production
2. **Update one major change at a time**: Easier to identify issues
3. **Read changelogs**: Understand what's changing
4. **Keep CI/CD green**: Don't let failing builds accumulate
5. **Document decisions**: Track why updates were made or deferred

### Security Posture

1. **Prioritize direct dependencies**: You have more control
2. **Minimize dependencies**: Fewer dependencies = smaller attack surface
3. **Use security scanning tools**: Automate vulnerability detection
4. **Train team members**: Ensure everyone understands security basics
5. **Have incident response plan**: Know what to do when vulnerability is exploited

## Tools and Resources

### Vulnerability Databases
- [National Vulnerability Database (NVD)](https://nvd.nist.gov/)
- [CVE Database](https://cve.mitre.org/)
- [Snyk Vulnerability Database](https://security.snyk.io/)
- [GitHub Advisory Database](https://github.com/advisories)

### Scanning Tools
- **Multi-language**: Snyk, WhiteSource, Sonatype Nexus
- **Java**: OWASP Dependency-Check, Maven Versions Plugin
- **JavaScript**: npm audit, yarn audit, RetireJS
- **Python**: pip-audit, safety, bandit
- **Go**: govulncheck, nancy
- **Ruby**: bundler-audit, brakeman
- **Rust**: cargo-audit
- **.NET**: dotnet list package, OWASP Dependency-Check
- **PHP**: local-php-security-checker

### Package Registries
- [Maven Central](https://search.maven.org/)
- [npm Registry](https://www.npmjs.com/)
- [PyPI](https://pypi.org/)
- [crates.io](https://crates.io/)
- [RubyGems](https://rubygems.org/)
- [NuGet Gallery](https://www.nuget.org/)

## Success Criteria

The vulnerability remediation is complete when:
- ✅ All critical and high vulnerabilities resolved or documented
- ✅ Vulnerability scan shows significant reduction in issues
- ✅ All tests pass successfully
- ✅ Application functions correctly with updates
- ✅ No new vulnerabilities introduced
- ✅ Documentation updated (VULNERABILITY_REPORT.md, CHANGELOG.md)
- ✅ Changes committed with clear description
- ✅ Pull request created with detailed analysis
- ✅ Code reviewed and approved
- ✅ Deployed to production successfully
- ✅ Post-deployment monitoring shows no issues
- ✅ Team notified of changes

---

*This skill provides comprehensive vulnerability management across all major programming languages and package managers.*
