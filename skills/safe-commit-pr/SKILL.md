---
name: safe-commit-pr
description: Safely commit code changes, run tests, check security, push to GitHub, and create a PR with comprehensive validation
---

# Safe Commit and PR Creation

A comprehensive workflow for safely committing code changes, running all tests, checking for security issues, pushing to GitHub, and creating a pull request. This skill ensures code quality and security before any changes reach the remote repository.

## Overview

This skill provides a safe, automated workflow that:
- Creates a new branch (never pushes directly to master/main)
- Runs complete test suite and validates all tests PASS
- Checks for major security vulnerabilities
- Creates detailed explanation files if issues are found
- Commits changes with proper formatting
- Pushes to GitHub only if all checks pass
- Creates a pull request with comprehensive details

## Safety Guarantees

- **Never pushes directly to master/main branch**
- **Tests must pass 100% before pushing**
- **Security issues block the push**
- **Detailed explanations for any failures**
- **All failures documented in `COMMIT_ISSUES.md`**

## Step 1: Check Current Branch

### Verify Not on Main Branch

```bash
# Get current branch name
git branch --show-current

# Get the main branch name (master or main)
git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@'
```

### Decision Logic

```
IF current branch IS master OR main:
  → Create a new feature branch
  → Move uncommitted changes to new branch

ELSE IF current branch is already a feature branch:
  → Continue with current branch
  → Verify branch name is descriptive
```

### Create Feature Branch (if needed)

```bash
# Or create a descriptive branch based on changes
git checkout -b fix/security-improvements
git checkout -b feature/add-authentication
```

## Step 2: Review Changes

### Check Git Status

```bash
# See all changes
git status

# See detailed diff of all changes
git diff

# See staged changes
git diff --cached

# Check for untracked files that should be added
git status --short
```

### Identify Changed Files

Document all changes:
```markdown
## Files Modified
- `src/main.js` - Added authentication logic
- `tests/auth.test.js` - Added authentication tests
- `package.json` - Updated dependencies

## Files Added
- `src/auth/provider.js` - New authentication provider
- `.env.example` - Environment variable template

## Files Deleted
- `src/old-auth.js` - Removed deprecated authentication
```

### Check for Sensitive Files

Look for files that should NOT be committed:

```bash
# Check for common sensitive files
git status | grep -E "\.env$|credentials|secrets|\.key|\.pem|\.p12|config\.local"
```

**DO NOT commit:**
- `.env` (environment variables with secrets)
- `credentials.json`, `secrets.yml`, `config.local.js`
- Private keys: `.key`, `.pem`, `.p12`, `*.pfx`
- Database dumps with sensitive data
- API keys or tokens in config files
- `node_modules/`, `target/`, `build/`, `dist/` (build artifacts)

## Step 3: Run All Tests

### Detect Test Framework

Identify which test framework is used:

```bash
# Check package.json for test script
cat package.json | grep '"test":'

# Check for test frameworks
ls -la | grep -E "pytest|jest|mocha|junit|gradle|maven|cargo|go.mod"
```

### Run Tests Based on Language/Framework

#### JavaScript/TypeScript (npm/yarn/pnpm)

```bash
# Using npm
npm test

# Or run all test variations if available
npm run test:unit
npm run test:integration
npm run test:e2e

# Using yarn
yarn test

# Using pnpm
pnpm test
```

#### Java (Maven)

```bash
# Run all tests
mvn clean test

# Run with full verification
mvn clean verify

# Skip integration tests if they're slow (for initial check)
mvn test -DskipITs
```

#### Java (Gradle)

```bash
# Run all tests
./gradlew clean test

# Run with full check
./gradlew clean check

# Run specific test suite
./gradlew test --tests '*UnitTest'
```

#### Python (pytest)

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=. --cov-report=term

# Run specific test directory
pytest tests/

# Run with verbose output
pytest -v
```

#### Python (unittest)

```bash
# Discover and run all tests
python -m unittest discover

# Run with verbose output
python -m unittest discover -v
```

#### Go

```bash
# Run all tests
go test ./...

# Run with coverage
go test -cover ./...

# Run with race detection
go test -race ./...

# Run with verbose output
go test -v ./...
```

#### Rust

```bash
# Run all tests
cargo test

# Run with output
cargo test -- --nocapture

# Run specific test
cargo test test_name
```

#### Ruby

```bash
# Using RSpec
bundle exec rspec

# Using Minitest
bundle exec rake test

# Run with specific file
bundle exec rspec spec/models/
```

#### .NET

```bash
# Run all tests
dotnet test

# Run with detailed output
dotnet test --logger "console;verbosity=detailed"

# Run in specific project
dotnet test tests/UnitTests/
```

#### PHP

```bash
# Using PHPUnit
./vendor/bin/phpunit

# Run with coverage
./vendor/bin/phpunit --coverage-html coverage/

# Run specific test suite
./vendor/bin/phpunit tests/Unit/
```

### Analyze Test Results

**Test Output Should Show:**
- Total number of tests run
- Number of passed tests
- Number of failed tests
- Number of skipped tests
- Execution time

### Success Criteria

```
✅ ALL TESTS MUST PASS
✅ Zero failures
✅ Zero errors
✅ Exit code 0

❌ BLOCK PUSH IF:
- Any test fails
- Tests exit with non-zero code
- Tests timeout or hang
- Tests are skipped (unless explicitly allowed)
```

### Document Test Results

If tests pass:
```markdown
## Test Results ✅

**All tests passed successfully**

- Framework: Jest
- Total Tests: 247
- Passed: 247
- Failed: 0
- Skipped: 0
- Duration: 12.4s
- Exit Code: 0
```

If tests fail:
```markdown
## Test Results ❌

**Tests failed - blocking push**

- Framework: Jest
- Total Tests: 247
- Passed: 245
- Failed: 2
- Skipped: 0
- Duration: 11.2s
- Exit Code: 1

### Failed Tests

1. **auth.test.js:42** - `should validate JWT tokens`
   - Error: Expected token to be valid, but was invalid
   - Location: src/auth/validator.js:15

2. **api.test.js:128** - `should handle rate limiting`
   - Error: Request was not rate limited as expected
   - Location: src/middleware/rateLimit.js:23

### Action Required
Fix these test failures before proceeding with commit and push.
```

## Step 4: Check for Security Issues

### Run Security Scanners

#### Check for Known Vulnerabilities

```bash
# JavaScript/TypeScript - npm audit
npm audit

# Get detailed vulnerability report
npm audit --json > security-audit.json

# Check only production dependencies
npm audit --production

# Python - safety
pip install safety
safety check

# Python - bandit (for code security issues)
pip install bandit
bandit -r . -f json -o security-bandit.json

# Ruby - bundler-audit
bundle audit check --update

# Rust - cargo-audit
cargo install cargo-audit
cargo audit

# Go - govulncheck
go install golang.org/x/vuln/cmd/govulncheck@latest
govulncheck ./...

# .NET - dotnet list package
dotnet list package --vulnerable --include-transitive

# PHP - composer audit
composer audit

# Java - OWASP dependency check
mvn org.owasp:dependency-check-maven:check
```

#### Check for Secrets in Code

```bash
# Search for common secret patterns
git diff --cached | grep -E "(api[_-]?key|password|secret|token|aws[_-]?access)" -i

# Check for hardcoded credentials
git diff --cached | grep -E "password\s*=|api_key\s*=|secret\s*=" -i

# Look for private keys
git diff --cached | grep -E "BEGIN (RSA|DSA|EC|OPENSSH) PRIVATE KEY"

# Check for AWS keys
git diff --cached | grep -E "AKIA[0-9A-Z]{16}"

# Check for GitHub tokens
git diff --cached | grep -E "ghp_[a-zA-Z0-9]{36}"
```

#### Use Automated Secret Scanners (if available)

```bash
# Using gitleaks (if installed)
gitleaks detect --source . --verbose

# Using truffleHog (if installed)
trufflehog filesystem . --json

# Using git-secrets (if installed)
git secrets --scan
```

### Security Analysis

Check for common security issues:

1. **SQL Injection**
   - Look for string concatenation in SQL queries
   - Ensure parameterized queries are used

2. **Cross-Site Scripting (XSS)**
   - Check for unescaped user input in HTML
   - Verify proper sanitization

3. **Command Injection**
   - Look for shell command execution with user input
   - Ensure proper escaping

4. **Path Traversal**
   - Check file operations with user-supplied paths
   - Verify path sanitization

5. **Authentication/Authorization Issues**
   - Check for missing authentication
   - Verify proper permission checks

6. **Insecure Cryptography**
   - Look for weak algorithms (MD5, SHA1)
   - Check for hardcoded encryption keys

7. **Dependency Vulnerabilities**
   - Check for outdated dependencies
   - Verify no known CVEs in dependencies

### Security Decision Matrix

```
IF critical/high vulnerabilities found:
  → BLOCK push
  → Create COMMIT_ISSUES.md with details
  → Require fixes before proceeding

IF medium vulnerabilities found:
  → WARN but allow push
  → Document in commit message
  → Create tracking issue

IF low vulnerabilities found:
  → LOG in commit message
  → Continue with push
```

### Document Security Issues

If security issues found:

```markdown
## Security Issues Found ⚠️

### Critical Issues (BLOCKS PUSH)

1. **SQL Injection Vulnerability**
   - File: `src/database/queries.js:45`
   - Issue: User input concatenated directly into SQL query
   - Risk: Attacker could execute arbitrary SQL commands
   - Fix Required: Use parameterized queries
   ```javascript
   // Current (VULNERABLE):
   db.query(`SELECT * FROM users WHERE id = ${userId}`)

   // Fixed:
   db.query('SELECT * FROM users WHERE id = ?', [userId])
   ```

2. **Exposed API Key**
   - File: `src/config.js:12`
   - Issue: API key hardcoded in source code
   - Risk: API key could be extracted from repository
   - Fix Required: Move to environment variables
   ```javascript
   // Current (VULNERABLE):
   const API_KEY = 'sk-1234567890abcdef'

   // Fixed:
   const API_KEY = process.env.API_KEY
   ```

### High Severity Issues

3. **Known Vulnerability in Dependency**
   - Package: `lodash@4.17.15`
   - CVE: CVE-2020-8203
   - CVSS Score: 7.4 (High)
   - Issue: Prototype pollution vulnerability
   - Fix Required: Update to lodash@4.17.21 or higher

### Medium Severity Issues

4. **Missing Input Validation**
   - File: `src/api/users.js:78`
   - Issue: User input not validated before processing
   - Risk: Potential for malformed data causing errors
   - Recommendation: Add input validation schema

### Action Required

**Cannot proceed with push due to critical security issues.**

Please fix the critical issues above before attempting to commit and push.
```

## Step 5: Create Explanation File

### When to Create COMMIT_ISSUES.md

Create this file if:
- Any tests fail
- Critical or high security issues found
- Major issues block the push

### Explanation File Template

```markdown
# Commit Issues Report

**Generated**: [Date and Time]
**Branch**: [branch-name]
**Status**: ❌ BLOCKED / ⚠️ WARNING / ✅ PASSED

## Summary

[Brief summary of the status - whether push is blocked and why]

## Test Results

### Status: [PASSED / FAILED]

[Detailed test results from Step 3]

## Security Analysis

### Status: [PASSED / HAS ISSUES]

[Detailed security findings from Step 4]

## Files Changed

[List of all modified files from Step 2]

## Blocking Issues

[List all issues that prevent pushing to remote]

## Warnings

[List all warnings that don't block but should be addressed]

## Recommendations

[Specific recommendations for fixing issues]

## Next Steps

1. [Action item 1]
2. [Action item 2]
3. [Action item 3]

---

*This report was generated automatically to document issues preventing safe code commit and push.*
```

## Step 6: Stage and Commit Changes

### Only Proceed If Tests Pass and No Critical Security Issues

```
IF tests failed OR critical security issues:
  → CREATE COMMIT_ISSUES.md
  → STOP - do not commit or push
  → Report issues to user
  → Exit with error status

ELSE:
  → Proceed with commit
```

### Review What Will Be Committed

```bash
# See all changes that will be committed
git status

# See detailed diff
git diff

# See which files are tracked vs untracked
git status --short
```

### Stage Files for Commit

```bash
# Stage specific files (recommended - explicit control)
git add src/main.js
git add tests/auth.test.js
git add package.json

# Or stage all modified files (be careful)
git add -u

# Or stage everything including untracked (use with caution)
git add .

# See what's staged
git diff --cached
```

**Important: Verify staged files**
```bash
# List all staged files
git diff --cached --name-only

# Review each staged file
git diff --cached
```

### Create Commit Message

Use conventional commit format:

```bash
git commit -m "$(cat <<'EOF'
<type>(<scope>): <short description>

<detailed description of changes>

Changes:
- <change 1>
- <change 2>
- <change 3>

Testing:
- All unit tests pass (X tests, 0 failures)
- All integration tests pass
- Manual testing completed
- Security scan: No critical issues

Security:
- Dependency vulnerabilities: None critical/high
- Code security check: Passed
- No secrets detected

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
EOF
)"
```

### Commit Types

- `feat`: New feature
- `fix`: Bug fix
- `security`: Security improvement
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `docs`: Documentation changes
- `chore`: Maintenance tasks
- `perf`: Performance improvement
- `style`: Code style/formatting
- `build`: Build system changes
- `ci`: CI/CD changes

### Example Commit Messages

**Feature Addition:**
```
feat(auth): add JWT authentication middleware

Implemented JWT-based authentication for API endpoints:
- Created authentication middleware
- Added token validation logic
- Implemented refresh token mechanism
- Added authentication tests

Changes:
- src/middleware/auth.js (new)
- src/utils/jwt.js (new)
- tests/auth.test.js (new)
- package.json (updated dependencies)

Testing:
- All unit tests pass (247 tests, 0 failures)
- All integration tests pass (45 tests)
- Manual API testing completed
- Security scan: No critical issues

Security:
- Using industry-standard JWT library (jsonwebtoken)
- Tokens expire after 1 hour
- Refresh tokens stored securely
- No hardcoded secrets

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

**Security Fix:**
```
security(api): fix SQL injection vulnerability in user queries

Fixed critical SQL injection vulnerability in user query endpoint:
- Replaced string concatenation with parameterized queries
- Added input validation and sanitization
- Updated all database query functions
- Added security tests

Changes:
- src/database/queries.js (parameterized queries)
- src/api/users.js (input validation)
- tests/security.test.js (new security tests)

Testing:
- All unit tests pass (250 tests, 0 failures)
- Security-specific tests added (12 tests)
- Penetration testing performed
- Security scan: No critical issues

Security:
- Fixed CVE-equivalent SQL injection risk
- All user inputs now validated
- SQL queries use parameterized statements
- No other security issues found

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

**Bug Fix:**
```
fix(validation): correct email validation regex

Fixed email validation allowing invalid email formats:
- Updated regex to follow RFC 5322 standard
- Added comprehensive test cases
- Fixed edge cases with special characters

Changes:
- src/utils/validation.js (updated regex)
- tests/validation.test.js (added 15 new test cases)

Testing:
- All unit tests pass (262 tests, 0 failures)
- Validation tests cover edge cases
- Manual testing with various email formats
- Security scan: No issues

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

### Verify Commit

```bash
# Check the commit was created
git log -1

# See the commit details
git show HEAD

# Check commit message format
git log -1 --pretty=format:"%s%n%n%b"
```

## Step 7: Push to Remote

### Verify Remote Configuration

```bash
# Check remote URL
git remote -v

# Verify we're not on master/main
git branch --show-current

# Check if remote branch exists
git ls-remote --heads origin $(git branch --show-current)
```

### Push Branch

```bash
# Push to remote with upstream tracking
git push -u origin $(git branch --show-current)

# Or specify branch name explicitly
git push -u origin feature/my-feature-branch
```

### Handle Push Failures

If push fails:

```bash
# Check if remote has changes
git fetch origin

# Check status
git status

# If behind remote, rebase or merge
git pull --rebase origin $(git branch --show-current)

# Then push again
git push -u origin $(git branch --show-current)
```

## Step 8: Create Pull Request

### Gather Information for PR

Collect:
- List of all changes
- Test results summary
- Security scan results
- Any breaking changes
- Migration notes if applicable

### Create PR Using GitHub CLI

```bash
# Create PR with title and body
gh pr create --title "<type>: <description>" --body "$(cat <<'EOF'
## Summary

[Brief description of what this PR does - 2-3 sentences]

## Changes

- **[File/Module 1]**: [Description of changes]
- **[File/Module 2]**: [Description of changes]
- **[File/Module 3]**: [Description of changes]

## Type of Change

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Security improvement
- [ ] Performance improvement
- [ ] Code refactoring
- [ ] Documentation update
- [ ] Dependency update

## Testing

### Test Results ✅

All tests pass successfully:
- **Unit Tests**: X passed, 0 failed
- **Integration Tests**: Y passed, 0 failed
- **E2E Tests**: Z passed, 0 failed
- **Total Duration**: Xs

### Manual Testing

- [x] Tested feature A
- [x] Tested feature B
- [x] Verified no regressions
- [x] Checked edge cases

## Security

### Security Scan Results ✅

- **Dependency Vulnerabilities**: 0 critical, 0 high
- **Code Security Issues**: None found
- **Secrets Scan**: No secrets detected
- **SAST Analysis**: Passed

### Security Considerations

- [List any security implications or improvements]
- [Mention any security-related changes]

## Breaking Changes

- [ ] No breaking changes
- [ ] Contains breaking changes (see below)

[If breaking changes, list them with migration guide]

## Additional Notes

[Any other relevant information]

## Related Issues

Closes #[issue-number]

## Checklist

- [x] Code follows project style guidelines
- [x] All tests pass locally
- [x] Added tests for new functionality
- [x] Updated documentation if needed
- [x] No security issues detected
- [x] Branch is up to date with base branch
- [x] Commit messages follow conventional commits
- [x] Self-review completed

## Deployment Notes

[Any special considerations for deployment]

## Rollback Plan

[How to rollback if issues are discovered]

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

### PR Title Format

Follow conventional commit format:
- `feat: add user authentication`
- `fix: resolve memory leak in cache`
- `security: patch SQL injection vulnerability`
- `refactor: simplify database connection logic`

### Verify PR Creation

```bash
# List open PRs
gh pr list

# View the PR you just created
gh pr view

# Get PR URL
gh pr view --web
```

## Step 9: Report Results to User

### Success Report

```markdown
## ✅ Commit and PR Created Successfully

### Summary
- **Branch**: feature/add-authentication
- **Commit**: abc1234 - "feat(auth): add JWT authentication middleware"
- **PR**: #42 - "feat: add user authentication"
- **PR URL**: https://github.com/user/repo/pull/42

### Test Results
- Unit Tests: 247 passed, 0 failed
- Integration Tests: 45 passed, 0 failed
- Duration: 12.4s

### Security Status
- Dependency vulnerabilities: 0 critical, 0 high
- Code security: Passed
- No secrets detected

### Files Changed
- 4 files modified
- 3 files added
- 0 files deleted

### Next Steps
1. Wait for CI/CD pipeline to complete
2. Request code review from team
3. Address any review comments
4. Merge when approved
```

### Failure Report

```markdown
## ❌ Push Blocked - Issues Found

### Summary
Cannot proceed with commit and push due to failing tests and security issues.

### Test Results
- Unit Tests: 245 passed, 2 failed ❌
- Failed: auth.test.js:42, api.test.js:128

### Security Issues
- Critical: 1 issue found (SQL injection)
- High: 1 issue found (exposed API key)

### Detailed Report
See `COMMIT_ISSUES.md` for complete details and recommendations.

### Action Required
1. Fix failing tests in auth.test.js and api.test.js
2. Fix SQL injection vulnerability in src/database/queries.js
3. Move API key to environment variables
4. Re-run this workflow after fixes

### Files Not Committed
- All changes remain uncommitted
- Working directory unchanged
- No remote push attempted
```

## Common Issues and Solutions

### Issue: Tests fail intermittently

**Symptoms:**
- Tests pass locally but fail in workflow
- Tests pass sometimes, fail other times

**Solutions:**
1. Check for race conditions in tests
2. Look for timing-dependent tests
3. Check for tests that depend on external services
4. Use test retry mechanisms
5. Fix flaky tests before proceeding

### Issue: Security scanner reports false positives

**Symptoms:**
- Security issues flagged that aren't real vulnerabilities
- Dependencies flagged that are safe

**Solutions:**
1. Review the specific CVE details
2. Check if the vulnerable code path is actually used
3. Document why it's a false positive
4. Consider adding to allowlist if appropriate
5. Upgrade dependency if possible anyway

### Issue: Cannot push - remote has changes

**Symptoms:**
- Push rejected - remote has commits not in local branch

**Solutions:**
1. Fetch latest changes: `git fetch origin`
2. Rebase on top of remote: `git pull --rebase origin branch-name`
3. Resolve any conflicts
4. Re-run tests after rebase
5. Push again

### Issue: Commit message too long

**Symptoms:**
- Git rejects commit message as too long
- PR description truncated

**Solutions:**
1. Shorten the subject line (< 72 characters)
2. Move details to body
3. Use bullet points for clarity
4. Link to external documentation if needed

### Issue: Accidentally on master branch

**Symptoms:**
- Current branch is master or main
- About to commit directly to main branch

**Solutions:**
1. Stop immediately - don't commit
2. Create feature branch: `git checkout -b feature/my-changes`
3. Changes move with you to new branch
4. Proceed with workflow from new branch

### Issue: Tests take too long

**Symptoms:**
- Test suite runs for > 10 minutes
- Workflow times out

**Solutions:**
1. Run only fast unit tests first
2. Skip slow integration/e2e tests initially
3. Use test parallelization if available
4. Consider running full suite in CI only
5. Optimize slow tests

### Issue: Secrets detected in code

**Symptoms:**
- Security scanner finds API keys or passwords
- Can't determine if real secret or test data

**Solutions:**
1. If real secret - STOP immediately
2. Remove secret from code
3. Move to environment variables
4. Rotate the secret (it's compromised)
5. Add to .gitignore
6. Consider using secret management service

## Best Practices

### Always

1. **Run tests before committing** - never skip this step
2. **Check security before pushing** - protect your codebase
3. **Use feature branches** - never commit directly to main
4. **Write clear commit messages** - explain why, not just what
5. **Include Co-Authored-By** - credit collaboration properly
6. **Review diff before committing** - catch accidental changes
7. **Keep commits atomic** - one logical change per commit
8. **Test after rebasing** - ensure rebase didn't break anything

### Never

1. **Never push directly to master/main** - always use feature branches
2. **Never commit without testing** - tests exist for a reason
3. **Never commit secrets** - use environment variables
4. **Never ignore security warnings** - investigate all alerts
5. **Never force push** - unless you're absolutely certain
6. **Never commit commented-out code** - delete it instead
7. **Never commit build artifacts** - add to .gitignore
8. **Never use `-m` for complex commits** - use heredoc for proper formatting

### Guidelines

1. **Commit early, commit often** - small commits are easier to review
2. **One feature per PR** - keep PRs focused and reviewable
3. **Update documentation** - keep docs in sync with code
4. **Add tests for new features** - maintain test coverage
5. **Clean up after yourself** - delete old branches after merge
6. **Be responsive to reviews** - address feedback promptly
7. **Keep PRs small** - aim for < 400 lines changed
8. **Link to issues** - provide context with issue references

## Success Criteria

The safe commit and PR workflow is successful when:

- ✅ Not on master/main branch (using feature branch)
- ✅ All tests pass (100% success rate)
- ✅ No critical or high security issues found
- ✅ No secrets detected in code
- ✅ Changes reviewed and appropriate
- ✅ Commit created with proper message format
- ✅ Push to remote succeeds
- ✅ Pull request created with comprehensive description
- ✅ PR includes test results and security status
- ✅ No COMMIT_ISSUES.md file created (no blocking issues)
- ✅ All checks automated and documented

The workflow FAILS and must be stopped if:

- ❌ Any tests fail
- ❌ Critical or high severity security issues found
- ❌ Secrets detected in code
- ❌ Currently on master/main branch (without creating feature branch)
- ❌ Push would be directly to master/main
- ❌ No tests exist or can't run tests

---

*This skill ensures safe and secure code commits and pull requests with comprehensive automated checks.*
