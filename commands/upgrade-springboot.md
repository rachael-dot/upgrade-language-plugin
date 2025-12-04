---
description: Upgrade Spring Boot framework version with comprehensive validation, testing, and documentation
---

You are helping to upgrade Spring Boot framework in this project. Follow the comprehensive upgrade process defined in `.claude/skills/upgrade-springboot.md`.

**KEY FEATURE:** This upgrade uses **OpenRewrite** to automatically apply Spring Boot migration recipes for safe, comprehensive, and automated code transformations.

**IMPORTANT:** You MUST create a file called `UPGRADE_SPRING_EXPLANATION.md` at the start of this process to document:
- Target Spring Boot version and rationale
- Compatibility analysis with current Java version
- **OpenRewrite recipe selection and execution results**
- **Files modified by OpenRewrite**
- Every action taken during the upgrade (automated and manual)
- Build and test results
- Configuration changes made
- Any issues encountered and how they were resolved

This file should be updated throughout the process and included in the final commit.

## Task Parameters

Extract from the user's request:
- **Current Version**: Automatically detect from pom.xml (Spring Boot parent version)
- **Target Version**: The desired Spring Boot version (e.g., "3.6.0", "3.5.8")
- **Issue Number**: GitHub issue number if applicable

## Execution Steps

### 1. Input and Validation
Read `.claude/skills/upgrade-springboot.md` and execute **Step 1: Input Validation and Version Detection**:

- Prompt user for target Spring Boot version
- Detect current version from pom.xml
- Validate version compatibility (Java version requirements)
- Check for major version upgrade warnings (2.x → 3.x)
- Determine upgrade type (major/minor/patch)
- **Create `UPGRADE_SPRING_EXPLANATION.md`**: Start the upgrade documentation immediately

### 2. Initialize Documentation
Execute **Step 2: Initialize Upgrade Documentation**:

- Create `UPGRADE_SPRING_EXPLANATION.md` with upgrade summary
- Document current state and target version
- Run pre-upgrade build and tests
- Document pre-upgrade test results
- List all current Spring dependencies

### 3. Analyze Release Notes
Execute **Step 3: Analyze Release Notes and Breaking Changes**:

- Fetch Spring Boot release notes for target version
- **Determine appropriate OpenRewrite recipe** based on version upgrade
- Identify breaking changes
- Analyze impact on current application
- **Document findings and selected OpenRewrite recipe in `UPGRADE_SPRING_EXPLANATION.md`**
- List potentially affected files

### 4. Create Feature Branch
Execute **Step 1** from the skill for git setup:

- Create feature branch: `upgrade/spring-boot-{CURRENT}-to-{TARGET}`
- Verify branch creation
- Confirm issue number if provided

### 5. Run OpenRewrite Migration Recipe
Execute **Step 4: Run OpenRewrite Migration Recipe**:

- **Execute appropriate OpenRewrite recipe for Spring Boot upgrade**
- Review all files modified by OpenRewrite using `git diff`
- **Document OpenRewrite execution results in `UPGRADE_SPRING_EXPLANATION.md`**:
  - Recipe name and execution status
  - All files modified
  - Key changes (POM updates, javax→jakarta migrations, deprecated API replacements)
  - Any manual changes still required
- Commit OpenRewrite changes with detailed message

### 6. Verify/Update pom.xml Version
Execute **Step 5: Update pom.xml Version (if needed)**:

- Verify OpenRewrite updated Spring Boot parent version
- If not updated, manually update the version
- **Document in `UPGRADE_SPRING_EXPLANATION.md`** if manual update was needed
- Commit if manual changes were made

### 7. Build and Fix Compilation Issues
Execute **Step 6: Initial Build and Dependency Resolution**:

- Run `mvn clean compile`
- If build fails:
  - Analyze error messages
  - Identify deprecated APIs or incompatible dependencies
  - Fix issues systematically
  - **Document each issue and resolution**
  - Commit after each major fix
- Iterate until build succeeds

### 8. Update Dependencies
Execute **Step 7: Update Dependencies**:

- Check for Spring-related dependency updates (OpenRewrite may have updated many already)
- Update remaining dependencies: springdoc-openapi, spring-data, etc.
- Document all dependency changes (both OpenRewrite and manual)
- Commit dependency updates

### 9. Update Configuration
Execute **Step 8: Configuration Updates**:

- Check application.properties/yml files (OpenRewrite may have updated many already)
- Update any remaining deprecated configuration properties
- Add new required properties
- Remove obsolete properties
- **Document all configuration changes (both OpenRewrite and manual)**
- Commit configuration updates if needed

### 10. Run Comprehensive Tests
Execute **Step 9: Run Comprehensive Tests**:

- Run full test suite: `mvn clean test`
- Run integration tests: `mvn clean verify`
- If tests fail:
  - Analyze each failure
  - Determine root cause (API changes, test framework, etc.)
  - Fix tests systematically
  - **Document each test fix in detail**
  - Commit test fixes
- **Update `UPGRADE_SPRING_EXPLANATION.md`** with complete test results

### 11. Verify Application
Execute **Step 10: Verify Application Functionality**:

- Build complete application: `mvn clean install`
- Start application: `mvn spring-boot:run`
- Test key endpoints (health, API, OpenAPI docs)
- Document application verification results
- Fix any runtime issues found
- Re-verify after fixes

### 12. Run Quality Checks
Execute **Step 11: Code Quality and Security Checks**:

- Run static analysis (SpotBugs, Checkstyle if configured)
- Check for vulnerable dependencies
- **Document quality and security analysis**
- Fix any critical issues found

### 13. Performance Analysis
Execute **Step 12: Performance Comparison** (optional but recommended):

- Document build time comparison
- Document application startup time comparison
- Note any performance changes

### 14. Finalize Documentation
Execute **Step 13: Finalize Documentation**:

- Add executive summary to `UPGRADE_SPRING_EXPLANATION.md`
- Include **OpenRewrite results summary**
- Include key metrics, test results, and upgrade status
- List all breaking changes handled (by OpenRewrite and manually)
- Add known issues and recommendations
- Include references to Spring Boot documentation

### 15. Create Pull Request
Execute **Step 14: Create Pull Request**:

- Ensure all changes committed
- **Finalize `UPGRADE_SPRING_EXPLANATION.md` with complete summary**
- Push branch to remote
- Create PR with comprehensive description including:
  - Summary of changes
  - **Reference to `UPGRADE_SPRING_EXPLANATION.md`**
  - Test results and verification steps
  - Breaking changes handled
  - Build verification checklist
  - Migration impact assessment

### 16. Post-PR Documentation
Execute **Step 15: Post-PR Validation**:

- Update `UPGRADE_SPRING_EXPLANATION.md` with PR number and URL
- Commit documentation update
- Push final changes

### 17. Report Results
Provide final summary:

- PR URL
- **OpenRewrite execution status**
- Upgrade status (successful/issues found)
- Build and test results
- Application verification status
- Any warnings or recommendations
- Next steps for reviewers

## Important Guidelines

1. **Always use OpenRewrite first** - Run the appropriate recipe before manual changes
2. **Review OpenRewrite changes carefully** - Use `git diff` to understand what was changed
3. **Always consult the skill file** (`.claude/skills/upgrade-springboot.md`) for detailed instructions
4. **Create and maintain `UPGRADE_SPRING_EXPLANATION.md`** - This is MANDATORY. Update it throughout the entire process
5. **Document OpenRewrite results** - Record what OpenRewrite changed and what needs manual work
6. **Commit frequently** - After OpenRewrite, then after each logical manual step
7. **Test continuously** - Run builds and tests after OpenRewrite and after significant changes
8. **Document everything** - Every issue, fix, and decision goes in UPGRADE_SPRING_EXPLANATION.md
9. **Verify functionality** - Always verify the application actually runs
10. **Handle breaking changes carefully** - OpenRewrite handles many, but verify completeness
11. **Check Java compatibility** - Spring Boot 3.x requires Java 17+
12. **Update remaining dependencies** - OpenRewrite updates many, but check for others
13. **Include `UPGRADE_SPRING_EXPLANATION.md` in commits** - Don't forget to stage this file

## Example Usage

```bash
/upgrade-springboot 3.6.0
/upgrade-springboot 3.5.8 for issue #42
```

When prompted by Claude:
```
What Spring Boot version would you like to upgrade to? (e.g., 3.6.0)
> 3.6.0
```

## Version Upgrade Types

### Patch Upgrade (3.5.7 → 3.5.8)
- Usually safe, bug fixes only
- Minimal breaking changes expected
- Quick process

### Minor Upgrade (3.5.x → 3.6.0)
- New features added
- Some deprecations possible
- Check release notes carefully

### Major Upgrade (2.7.x → 3.0.x)
- Significant breaking changes
- Requires Java 17+
- javax.* → jakarta.* namespace change
- Extensive testing required
- Review migration guide thoroughly

## Error Handling

If issues arise:
- Consult the **Common Issues and Solutions** section in the skill file
- Document problems clearly in UPGRADE_SPRING_EXPLANATION.md
- Don't proceed if tests fail - investigate and fix first
- Consider breaking the upgrade into smaller steps for major versions

## Output Format

Use the TodoWrite tool to track progress through the upgrade steps:
1. Prompt for version and create UPGRADE_SPRING_EXPLANATION.md
2. Analyze release notes and determine OpenRewrite recipe
3. Create feature branch
4. **Run OpenRewrite migration recipe and document results**
5. Verify/update pom.xml parent version
6. Fix compilation issues (updating documentation)
7. Update remaining dependencies (documenting changes)
8. Update remaining configuration (documenting changes)
9. Fix tests (documenting each fix)
10. Verify application functionality
11. Run quality and security checks
12. Finalize UPGRADE_SPRING_EXPLANATION.md with executive summary
13. Create comprehensive PR
14. Report results

Provide clear, structured output at each step with detailed explanations of changes made (both by OpenRewrite and manually).

**CRITICAL:** The `UPGRADE_SPRING_EXPLANATION.md` file is a living document that should be created at the start and updated throughout the entire process. It provides complete transparency into the upgrade process, including OpenRewrite automation and manual interventions, and serves as a permanent record for future reference and troubleshooting.

## Success Criteria

The upgrade is considered successful when:
- ✅ **OpenRewrite recipe executed successfully**
- ✅ **OpenRewrite changes reviewed and committed**
- ✅ `mvn clean install` succeeds
- ✅ All tests pass (unit + integration)
- ✅ Application starts without errors
- ✅ Key endpoints verified functional
- ✅ No critical security vulnerabilities
- ✅ `UPGRADE_SPRING_EXPLANATION.md` is complete and comprehensive (including OpenRewrite details)
- ✅ All changes committed with clear messages
- ✅ PR created with full documentation
- ✅ Ready for reviewer approval
