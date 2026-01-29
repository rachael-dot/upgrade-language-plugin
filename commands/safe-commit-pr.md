---
description: Safely commit code, run tests, check security, and create a PR with comprehensive validation
---

You are helping to safely commit code changes, run all tests, check for security issues, push to GitHub, and create a pull request. Follow the comprehensive workflow defined in `.claude/skills/safe-commit-pr.md`.

**IMPORTANT:** This workflow includes strict safety checks:
- All tests MUST pass before pushing
- Security issues MUST be resolved before pushing
- NEVER push directly to master/main branch
- Create `COMMIT_ISSUES.md` if any blocking issues are found

## Safety Guarantees

This workflow enforces:
1. **Branch Safety**: Never commits directly to master/main
2. **Test Validation**: All tests must pass 100%
3. **Security Checks**: No critical/high security issues allowed
4. **Documentation**: All issues documented in COMMIT_ISSUES.md
5. **Traceability**: Clear commit messages and PR descriptions

## Task Parameters

Extract from the user's request (or use defaults):
- **Commit Type**: feat, fix, security, refactor, etc.
- **Scope**: Which part of the codebase is affected
- **Description**: Brief description of changes
- **Issue Number**: GitHub issue number if applicable

## Execution Steps

### 1. Check Current Branch (CRITICAL)
Execute **Step 1** from the skill:

- Check if currently on master/main branch
- If on master/main, create a feature branch immediately
- If on feature branch, verify it's descriptive
- NEVER proceed if staying on master/main
- **Document branch strategy in COMMIT_ISSUES.md if issues found**

### 2. Review Changes
Execute **Step 2** from the skill:

- Run `git status` to see all changes
- Run `git diff` to review detailed changes
- Identify all modified, added, and deleted files
- Check for sensitive files (.env, credentials, keys)
- **BLOCK if sensitive files detected** and document in COMMIT_ISSUES.md

### 3. Run All Tests (MANDATORY)
Execute **Step 3** from the skill:

- Detect test framework (npm, pytest, maven, gradle, etc.)
- Run complete test suite for the project
- Capture test output with counts (passed/failed/skipped)
- **ALL TESTS MUST PASS** - zero failures allowed
- If any test fails:
  - STOP immediately
  - Create COMMIT_ISSUES.md with detailed test failure information
  - Include error messages and stack traces
  - Document which tests failed and why
  - DO NOT proceed with commit or push
  - Report to user and exit

### 4. Check for Security Issues (MANDATORY)
Execute **Step 4** from the skill:

- Run security vulnerability scanner (npm audit, safety, etc.)
- Check for hardcoded secrets in code changes
- Scan for common vulnerabilities (SQL injection, XSS, etc.)
- Run dependency security checks
- Analyze severity levels (critical, high, medium, low)
- If critical or high security issues found:
  - STOP immediately
  - Create or update COMMIT_ISSUES.md with:
    - Detailed security issue descriptions
    - Affected files and line numbers
    - Specific vulnerabilities found (CVEs if applicable)
    - Required fixes and recommendations
  - DO NOT proceed with commit or push
  - Report to user and exit

### 5. Create Explanation File (if needed)
Execute **Step 5** from the skill:

- If tests failed OR security issues found:
  - Create comprehensive COMMIT_ISSUES.md file
  - Include all test results
  - Include all security findings
  - Provide specific recommendations for fixes
  - Document blocking issues clearly
- File format should include:
  - Summary of blocking issues
  - Complete test results
  - Detailed security analysis
  - List of files changed
  - Specific action items to resolve issues
  - Next steps

### 6. Stage and Commit Changes (only if all checks pass)
Execute **Step 6** from the skill:

- **Pre-condition check**:
  - Verify all tests passed (from Step 3)
  - Verify no critical/high security issues (from Step 4)
  - If either check failed, STOP and report
- Review what will be committed
- Stage appropriate files (not build artifacts or node_modules)
- Create commit with proper format:
  - Use conventional commit format
  - Include detailed description
  - List all changes
  - Document test results in commit message
  - Document security status in commit message
  - Add Co-Authored-By line
- Use heredoc for proper formatting
- Verify commit was created successfully

### 7. Push to Remote (only if commit succeeded)
Execute **Step 7** from the skill:

- Verify remote configuration
- Double-check we're not on master/main
- Push branch with upstream tracking
- Handle any push failures (fetch, rebase, retry)
- Confirm push succeeded

### 8. Create Pull Request (only if push succeeded)
Execute **Step 8** from the skill:

- Gather all information for PR description
- Create PR using gh CLI with comprehensive details:
  - Summary of changes
  - Type of change checkboxes
  - Complete test results
  - Security scan results
  - Breaking changes section
  - Checklist
  - Issue references
- Use proper PR title (conventional format)
- Verify PR was created
- Get PR URL

### 9. Report Results to User
Execute **Step 9** from the skill:

- If successful:
  - Provide success report with PR URL
  - Include test summary
  - Include security status
  - List next steps
- If failed:
  - Provide failure report
  - Reference COMMIT_ISSUES.md
  - List specific issues that blocked
  - Provide action items
  - DO NOT include PR URL (no PR created)

## Critical Safety Rules

### MUST STOP if:
1. ❌ Any test fails (even 1 out of 1000)
2. ❌ Critical severity security issues found
3. ❌ High severity security issues found
4. ❌ Secrets or credentials detected in code
5. ❌ Currently on master/main branch and cannot create feature branch
6. ❌ Tests cannot be run or found

### MUST CREATE COMMIT_ISSUES.md if:
- Tests fail
- Security issues found (critical or high)
- Any blocking issue prevents push
- Need to document what went wrong

### MUST DOCUMENT in Commit Message:
- Test results (number passed/failed)
- Security scan results
- All files changed
- Why changes were made
- Co-Authored-By line

### NEVER:
- ❌ Push directly to master/main
- ❌ Skip running tests
- ❌ Ignore security warnings
- ❌ Commit without running checks
- ❌ Proceed if tests fail
- ❌ Proceed if critical/high security issues exist

## Example Usage

```bash
# Basic usage - commit and create PR
/safe-commit-pr

# With specific commit type and scope
/safe-commit-pr feat(auth): add JWT authentication

# With issue reference
/safe-commit-pr fix(api): resolve rate limiting for issue #42

# Security fix
/safe-commit-pr security(db): fix SQL injection vulnerability
```

## Error Handling

If issues arise:
- Consult the skill file for solutions
- Create detailed COMMIT_ISSUES.md file
- Document all blocking issues
- Provide specific recommendations
- DO NOT proceed with push
- Report clearly to user

## Output Format

Use the TodoWrite tool to track progress through the workflow:
1. Check current branch and create feature branch if needed
2. Review all changes and check for sensitive files
3. Run complete test suite (MUST PASS)
4. Run security checks (NO CRITICAL/HIGH ISSUES)
5. Create COMMIT_ISSUES.md if any failures (or proceed if all pass)
6. Stage and commit changes (only if checks passed)
7. Push to remote (only if committed)
8. Create pull request (only if pushed)
9. Report results to user

Provide clear, structured output at each step with explanations for any blocks or issues.

## COMMIT_ISSUES.md Template

If created, use this structure:

```markdown
# Commit Issues Report

**Generated**: [Date and Time]
**Branch**: [branch-name]
**Status**: ❌ BLOCKED

## Summary

This commit and push has been blocked due to [test failures/security issues/both].

## Test Results ❌

- Framework: [test framework name]
- Total Tests: X
- Passed: Y
- Failed: Z
- Duration: Xs

### Failed Tests

1. **[test file]:[line]** - `[test name]`
   - Error: [error message]
   - Location: [file:line]
   - Stack trace: [relevant stack trace]

[Repeat for each failed test]

## Security Issues ⚠️

### Critical Issues

1. **[Issue Type]**
   - File: [file:line]
   - Issue: [description]
   - Risk: [impact description]
   - Fix Required: [specific fix needed]
   - Code example: [before/after]

### High Severity Issues

[Similar format for high severity issues]

## Files Changed

- [file1] - [description]
- [file2] - [description]

## Action Required

Cannot proceed with commit and push. Please:

1. [Specific action 1]
2. [Specific action 2]
3. [Specific action 3]

## Next Steps

After fixing the issues above:
1. Re-run the tests to verify fixes
2. Re-run security checks
3. Run /safe-commit-pr again

---

*This report was generated automatically to document issues preventing safe code commit and push.*
```

## Success Report Template

If successful, report to user:

```markdown
## ✅ Commit and PR Created Successfully

### Summary
- **Branch**: [branch-name]
- **Commit**: [commit-hash] - "[commit message]"
- **PR**: #[number] - "[PR title]"
- **PR URL**: [GitHub PR URL]

### Test Results ✅
- Unit Tests: X passed, 0 failed
- Integration Tests: Y passed, 0 failed
- Total Duration: Xs

### Security Status ✅
- Dependency vulnerabilities: 0 critical, 0 high
- Code security: Passed
- No secrets detected

### Files Changed
- X files modified
- Y files added
- Z files deleted

### Next Steps
1. Wait for CI/CD pipeline to complete
2. Request code review from team
3. Address any review comments
4. Merge when approved
```

**CRITICAL REMINDER:** The primary goal is safety. If any tests fail or critical/high security issues are found, STOP immediately, document everything in COMMIT_ISSUES.md, and report to the user. Never bypass these safety checks.
