---
description: Search for and update dependencies with known security vulnerabilities across multiple languages and package managers
---

You are helping to identify and update dependencies with known security vulnerabilities in this project. Follow the comprehensive process defined in `.claude/skills/update-vulnerable-dependencies.md`.

**IMPORTANT:** You MUST create a file called `VULNERABILITY_REPORT.md` at the start of this process to document:
- Vulnerability scan results organized by severity (Critical, High, Medium, Low)
- Detailed CVE information for each vulnerability
- Update strategy and prioritization
- All dependencies updated with version changes
- Test results and verification
- Any remaining vulnerabilities and mitigation strategies

This file should be updated throughout the process and included in the final commit.

## Task Parameters

Extract from the user's request or detect automatically:
- **Language/Package Manager**: Automatically detect from project files
- **Severity Focus**: Which severity levels to address (default: Critical + High)
- **Update Scope**: All dependencies or specific packages
- **Issue Number**: GitHub issue number if applicable

## Execution Steps

### 1. Identify Project Type
Execute **Step 1: Identify Project Type and Package Manager**:

- Scan project for package manager files (pom.xml, package.json, etc.)
- Identify all active package managers in the project
- Document detected languages and package managers
- **Update `VULNERABILITY_REPORT.md`** with project information

### 2. Run Vulnerability Scan
Execute **Step 2: Run Vulnerability Scan**:

- Run appropriate vulnerability scanner for each detected package manager:
  - **Java (Maven)**: `mvn org.owasp:dependency-check-maven:check`
  - **Java (Gradle)**: `./gradlew dependencyCheckAnalyze`
  - **JavaScript/TypeScript**: `npm audit` or `yarn audit`
  - **Python**: `pip-audit` or `safety check`
  - **Go**: `govulncheck ./...`
  - **Ruby**: `bundle audit check --update`
  - **Rust**: `cargo audit`
  - **.NET**: `dotnet list package --vulnerable`
  - **PHP**: `composer audit`
- Review scan output and identify all vulnerabilities
- **Document scan results in `VULNERABILITY_REPORT.md`**

### 3. Analyze Vulnerabilities
Execute **Step 3: Analyze Vulnerability Report**:

- **Create/Update `VULNERABILITY_REPORT.md`** with structured vulnerability data:
  - Organize by severity (Critical, High, Medium, Low)
  - Include CVE numbers, CVSS scores, descriptions
  - List current versions and fixed versions
  - Document vulnerable packages
- Categorize vulnerabilities by severity guidelines
- Identify dependencies with no fix available

### 4. Prioritize Updates
Execute **Step 4: Prioritize Updates**:

- Apply decision matrix to determine update strategy
- **Priority 1 (Immediate)**: Critical and High severity with fixes available
- **Priority 2 (Next Sprint)**: Medium severity direct dependencies
- **Priority 3 (Backlog)**: Low severity or transitive dependencies
- Check for breaking changes in target versions
- Review changelogs for major version bumps
- **Document update strategy in `VULNERABILITY_REPORT.md`**

### 5. Create Feature Branch
Execute **Step 5: Update Dependencies** (branch creation):

- Create security branch: `security/update-vulnerable-dependencies-{DATE}`
- Verify branch creation

### 6. Update Dependencies
Execute **Step 5: Update Dependencies** (package updates):

- Update dependencies based on priority order
- For each package manager, apply appropriate update commands
- **Maven**: Update pom.xml versions or use `mvn versions:use-latest-versions`
- **Gradle**: Update build.gradle versions
- **npm**: Use `npm update` or `npm audit fix`
- **Python**: Update requirements.txt or use poetry/pipenv commands
- **Go**: Use `go get` commands and `go mod tidy`
- **Ruby**: Use `bundle update` commands
- **Rust**: Use `cargo update` commands
- **.NET**: Use `dotnet add package` commands
- **PHP**: Use `composer update` commands
- Document each update in `VULNERABILITY_REPORT.md`

### 7. Run Tests
Execute **Step 6: Test After Updates**:

- Run full test suite for the project
- Run integration tests if available
- Build the application
- Start the application and verify core functionality
- Check for runtime warnings or deprecations
- **Document test results in `VULNERABILITY_REPORT.MD`**

### 8. Re-scan for Vulnerabilities
Execute **Step 7: Re-scan for Vulnerabilities**:

- Run vulnerability scanner again to verify fixes
- Confirm vulnerabilities are resolved
- Check that no new vulnerabilities were introduced
- **Update `VULNERABILITY_REPORT.md`** with before/after comparison

### 9. Update Documentation
Execute **Step 8: Update Documentation**:

- **Finalize `VULNERABILITY_REPORT.md`** with:
  - Updates applied (package, old version, new version, CVE resolved)
  - Test results summary
  - Remaining issues (if any)
  - Mitigation strategies for unfixable vulnerabilities
- Update project documentation if breaking changes:
  - Update CHANGELOG.md with security updates
  - Update README.md if usage changes required

### 10. Commit Changes
Execute **Step 9: Commit Changes**:

- Review all changes with `git status` and `git diff`
- Stage dependency files (pom.xml, package.json, requirements.txt, etc.)
- Stage lock files (package-lock.json, Gemfile.lock, etc.)
- Stage documentation (VULNERABILITY_REPORT.md, CHANGELOG.md)
- Create detailed commit message listing:
  - Critical/High severity fixes
  - Medium/Low severity fixes
  - Package updates with versions
  - Test results
  - Reference to VULNERABILITY_REPORT.md

### 11. Create Pull Request
Execute **Step 10: Push and Create Pull Request**:

- Push branch to remote
- Create PR with comprehensive description including:
  - **Security fixes summary** organized by severity
  - Detailed CVE information for critical/high vulnerabilities
  - Changes made (direct and transitive dependencies)
  - Breaking changes (if any)
  - Complete test plan results
  - Vulnerability scan before/after comparison
  - Risk assessment
  - Rollback plan
  - References to CVE databases and changelogs

### 12. Post-Deployment Monitoring
Execute **Step 11: Monitor Post-Deployment**:

- Note monitoring requirements for deployment
- Recommend automated security scanning setup
- Suggest regular scan schedule

## Important Guidelines

1. **Always create `VULNERABILITY_REPORT.md`** - This is MANDATORY and provides complete transparency
2. **Prioritize by severity** - Critical and High vulnerabilities must be addressed immediately
3. **Test thoroughly** - Run full test suite after each batch of updates
4. **Check for breaking changes** - Review changelogs before major version updates
5. **Document everything** - Every vulnerability, update, and test result goes in VULNERABILITY_REPORT.md
6. **Handle no-fix scenarios** - Document workarounds for vulnerabilities without patches
7. **Verify fixes** - Always re-scan after updates to confirm resolution
8. **Consider transitive dependencies** - Don't forget indirect dependencies
9. **Stage updates carefully** - Update critical issues first, then high, then medium/low
10. **Include lock files** - Always commit package-lock.json, yarn.lock, Gemfile.lock, etc.

## Example Usage

```bash
/update-vulnerable-dependencies
/update-vulnerable-dependencies for issue #42
/update-vulnerable-dependencies --severity=critical,high
```

## Language-Specific Notes

### Java Projects
- Use OWASP Dependency-Check for comprehensive scanning
- Consider using Maven Versions Plugin for updates
- Check for Spring Boot updates if applicable

### JavaScript/TypeScript Projects
- Use npm audit or yarn audit (built-in)
- Consider npm-check-updates for major version bumps
- Be cautious with `npm audit fix` as it may cause breaking changes

### Python Projects
- Use pip-audit or safety for scanning
- Update requirements.txt or pyproject.toml appropriately
- Test with different Python versions if needed

### Multi-Language Projects
- Scan and update each package manager separately
- Document findings for each language in VULNERABILITY_REPORT.md
- Test each subsystem independently

## Error Handling

If issues arise:
- **Build failures**: Check API changes, update code to use new APIs
- **Test failures**: Review test assertions, update mocks/stubs
- **Dependency conflicts**: Use dependency management or resolutions
- **No fix available**: Document risk, implement workarounds, consider alternatives
- **Breaking changes**: Review migration guides, allocate more testing time

## Output Format

Use the TodoWrite tool to track progress through the vulnerability update steps:
1. Identify project type and package managers
2. Run vulnerability scans
3. Analyze and document vulnerabilities in VULNERABILITY_REPORT.md
4. Prioritize updates by severity
5. Create feature branch
6. Update dependencies (documenting each update)
7. Run comprehensive tests
8. Re-scan to verify fixes
9. Finalize documentation
10. Create commit with detailed message
11. Create comprehensive PR
12. Document monitoring requirements

Provide clear, structured output at each step with specific details about vulnerabilities found and actions taken.

**CRITICAL:** The `VULNERABILITY_REPORT.md` file is a living document that should be created at the start and updated throughout the entire process. It provides complete transparency into the vulnerability management process and serves as a permanent security record.

## Success Criteria

The vulnerability update is considered successful when:
- ✅ Vulnerability scan completed for all package managers
- ✅ All Critical and High vulnerabilities resolved or documented
- ✅ `VULNERABILITY_REPORT.md` created with complete analysis
- ✅ All tests pass (unit + integration)
- ✅ Application starts and functions correctly
- ✅ No new vulnerabilities introduced
- ✅ Re-scan confirms vulnerabilities are resolved
- ✅ All changes committed with clear messages
- ✅ PR created with comprehensive security analysis
- ✅ Ready for security review and deployment
