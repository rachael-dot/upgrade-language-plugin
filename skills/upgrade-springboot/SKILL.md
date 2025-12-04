---
name: upgrade-springboot
description: Upgrade Spring Boot framework to a specified version with comprehensive validation, testing, documentation, and PR creation
---

# Upgrade Spring Boot Framework - Plugin Skill

A comprehensive guide for upgrading Spring Boot framework projects to a specified target version, ensuring compatibility, stability, proper testing, and complete documentation throughout the process.

## Overview

This skill automates the Spring Boot upgrade process by:
1. Accepting a target Spring Boot version as input
2. Analyzing current configuration and dependencies
3. **Using OpenRewrite to automatically apply Spring Boot migration recipes**
4. Performing the upgrade with proper validation
5. Running comprehensive tests
6. Verifying application functionality
7. Creating and maintaining `UPGRADE_SPRING_EXPLANATION.md` documentation
8. Creating a pull request with all changes

## Prerequisites

### Required Information
- **Target Spring Boot Version**: The version to upgrade to (e.g., "3.6.0", "3.5.8")
- **Current Version**: Automatically detected from pom.xml

### Required Tools
- **OpenRewrite**: For automated code migration and refactoring
- **JDK version** compatible with target Spring Boot version
- **Maven build tool** (3.9.9 or higher)
- **Git** for version control
- **GitHub CLI (gh)** for PR creation

### Version Compatibility Matrix
- Spring Boot 3.0+: Requires Java 17 or higher
- Spring Boot 2.7+: Requires Java 11 or higher
- Spring Boot 2.5-2.6: Requires Java 8 or higher

### OpenRewrite Recipes Available
OpenRewrite provides official Spring Boot migration recipes:
- **org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_0**: Migrate from Spring Boot 2.x to 3.0
- **org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_1**: Migrate from 3.0 to 3.1
- **org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_2**: Migrate from 3.1 to 3.2
- **org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_3**: Migrate from 3.2 to 3.3
- Custom recipes for specific minor/patch versions can be applied

## Step 1: Input Validation and Version Detection

### Prompt for Target Version
Ask the user for the target Spring Boot version:

```
What Spring Boot version would you like to upgrade to? (e.g., 3.6.0)
```

### Detect Current Version
Extract the current Spring Boot version from `pom.xml`:

```bash
# Check current Spring Boot version
grep -A 2 "<parent>" pom.xml | grep "<version>"
```

### Validate Version Compatibility
1. Check if target version is newer than current version
2. Verify Java version compatibility
3. Check for major version changes (e.g., 2.x to 3.x)
4. Warn about breaking changes if major version upgrade

### Create Branch
Create a feature branch for the upgrade:

```bash
git checkout -b upgrade/spring-boot-{CURRENT_VERSION}-to-{TARGET_VERSION}
```

## Step 2: Initialize Upgrade Documentation

**CRITICAL**: Create `UPGRADE_SPRING_EXPLANATION.md` at the project root before any changes.

### Create Initial Documentation
```markdown
# Spring Boot Upgrade: {CURRENT_VERSION} → {TARGET_VERSION}

## Upgrade Summary
- **Current Version**: {CURRENT_VERSION}
- **Target Version**: {TARGET_VERSION}
- **Upgrade Date**: {CURRENT_DATE}
- **Upgrade Type**: {Major/Minor/Patch}

## Compatibility Analysis
- **Java Version**: {DETECTED_JAVA_VERSION}
- **Spring Boot Version Compatibility**: {Compatible/Requires Java Upgrade}
- **Breaking Changes Expected**: {Yes/No based on major version change}

## Pre-Upgrade State
### Application Configuration
- Current pom.xml parent version: {CURRENT_VERSION}
- Key dependencies detected:
  {LIST_KEY_SPRING_DEPENDENCIES}

### Test Status Before Upgrade
- Build Status: {PASS/FAIL}
- Total Tests: {COUNT}
- Passing Tests: {COUNT}
- Application Startup: {SUCCESS/FAIL}

## Upgrade Process Log
{This section will be updated throughout the upgrade}

---
*Document maintained by upgrade-springboot skill*
```

### Run Pre-Upgrade Verification
```bash
# Build and test current state
mvn clean install

# Record results in UPGRADE_SPRING_EXPLANATION.md
```

**Update Documentation**: Add build and test results to the "Pre-Upgrade State" section.

## Step 3: Analyze Release Notes and Breaking Changes

### Fetch Release Notes
For major/minor upgrades, fetch and analyze release notes:

```bash
# Fetch Spring Boot release notes from official sources
# For 3.x versions
https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-{VERSION}-Release-Notes

# For migration guides
https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-{VERSION}-Configuration-Changelog
```

### Determine OpenRewrite Recipe
Based on the current and target versions, determine the appropriate OpenRewrite recipe:

**Examples:**
- 2.7.x → 3.0.x: Use `org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_0`
- 3.0.x → 3.1.x: Use `org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_1`
- 3.1.x → 3.2.x: Use `org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_2`
- 3.2.x → 3.3.x: Use `org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_3`

For patch upgrades (e.g., 3.5.7 → 3.5.8), OpenRewrite may not be necessary as these are typically bug fixes.

### Document Breaking Changes
**Update UPGRADE_SPRING_EXPLANATION.md** with a new section:

```markdown
## Breaking Changes Analysis
### Changes from Release Notes
- {LIST_BREAKING_CHANGES}

### OpenRewrite Recipe Selected
- **Recipe**: {RECIPE_NAME}
- **Purpose**: {WHAT_IT_DOES}

### Potential Impact on Application
- Configuration changes needed: {YES/NO}
- Dependency updates required: {YES/NO}
- Code changes required: {YES/NO}
- API changes: {YES/NO}

### Affected Files (Preliminary)
- {LIST_FILES_THAT_MAY_NEED_CHANGES}
```

## Step 4: Run OpenRewrite Migration Recipe

**IMPORTANT**: This step uses OpenRewrite to automatically apply the Spring Boot migration recipe.

### Add OpenRewrite Maven Plugin (if not present)
Check if the project has the OpenRewrite Maven plugin configured:

```bash
# Check for rewrite-maven-plugin in pom.xml
grep -A 5 "rewrite-maven-plugin" pom.xml
```

If not present, you can run OpenRewrite using the command line without modifying pom.xml.

### Execute OpenRewrite Recipe
Run the appropriate OpenRewrite recipe based on the version upgrade:

```bash
# For Spring Boot 2.x to 3.0 upgrade
mvn -U org.openrewrite.maven:rewrite-maven-plugin:run \
  -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:LATEST \
  -Drewrite.activeRecipes=org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_0

# For Spring Boot 3.0 to 3.1 upgrade
mvn -U org.openrewrite.maven:rewrite-maven-plugin:run \
  -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:LATEST \
  -Drewrite.activeRecipes=org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_1

# For Spring Boot 3.1 to 3.2 upgrade
mvn -U org.openrewrite.maven:rewrite-maven-plugin:run \
  -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:LATEST \
  -Drewrite.activeRecipes=org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_2

# For Spring Boot 3.2 to 3.3 upgrade
mvn -U org.openrewrite.maven:rewrite-maven-plugin:run \
  -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:LATEST \
  -Drewrite.activeRecipes=org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_3
```

### Review OpenRewrite Changes
After running OpenRewrite, review what was changed:

```bash
# Check what files were modified
git status

# Review the changes
git diff
```

### Document OpenRewrite Results
**Update UPGRADE_SPRING_EXPLANATION.md**:

```markdown
## OpenRewrite Migration Results
### Recipe Executed
- **Recipe**: {RECIPE_NAME}
- **Execution Time**: {TIME}
- **Status**: {SUCCESS/PARTIAL/FAILED}

### Files Modified by OpenRewrite
{LIST_ALL_FILES_MODIFIED}

### Key Changes Applied
1. **POM Updates**:
   - Spring Boot parent version: {OLD} → {NEW}
   - Dependencies updated: {LIST}

2. **Code Changes**:
   - Package name migrations (javax.* → jakarta.*): {COUNT} files
   - Deprecated API replacements: {COUNT} occurrences
   - Configuration property updates: {COUNT} properties

3. **Configuration Changes**:
   - application.properties updates: {LIST}
   - application.yml updates: {LIST}

### Manual Changes Required
{LIST_ANY_CHANGES_OPENREWRITE_COULDNT_HANDLE}
```

### Commit OpenRewrite Changes
```bash
git add .
git commit -m "refactor: apply OpenRewrite Spring Boot {TARGET_VERSION} migration recipe

Applied OpenRewrite recipe: {RECIPE_NAME}

Changes include:
- Updated Spring Boot parent version to {TARGET_VERSION}
- Migrated deprecated APIs
- Updated configuration properties
- Applied code transformations for Spring Boot {TARGET_VERSION}

See UPGRADE_SPRING_EXPLANATION.md for detailed change log.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

## Step 5: Update pom.xml Version (if needed)

**NOTE**: OpenRewrite typically updates the Spring Boot parent version automatically. This step is only needed if OpenRewrite didn't update it or if you're doing a patch upgrade without OpenRewrite.

### Verify Parent Version
Check if OpenRewrite updated the Spring Boot parent version:

```bash
# Check the current parent version in pom.xml
grep -A 2 "<parent>" pom.xml | grep "<version>"
```

### Manual Update (if needed)
Only if OpenRewrite didn't update the version or for patch upgrades:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>{TARGET_VERSION}</version>
    <relativePath/>
</parent>
```

If manual update was needed, commit the change:
```bash
git add pom.xml UPGRADE_SPRING_EXPLANATION.md
git commit -m "chore: update Spring Boot parent version to {TARGET_VERSION}

Manual version update (OpenRewrite didn't handle this version).

See UPGRADE_SPRING_EXPLANATION.md for details.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

## Step 6: Initial Build and Dependency Resolution

**NOTE**: Since OpenRewrite has already applied many changes, this build may surface issues that OpenRewrite couldn't automatically resolve.

### Run Clean Build
```bash
mvn clean compile
```

### Handle Build Failures
If build fails:

1. **Analyze Error Messages**
   - Identify deprecated APIs
   - Find incompatible dependencies
   - Detect configuration issues

2. **Update UPGRADE_SPRING_EXPLANATION.md**:
   ```markdown
   ## Build Issues Encountered
   ### Issue 1: {ERROR_TYPE}
   - **Error Message**: {FULL_ERROR}
   - **Affected Files**: {FILE_LIST}
   - **Root Cause**: {ANALYSIS}
   - **Resolution**: {STEPS_TAKEN}
   - **Status**: {RESOLVED/IN_PROGRESS/BLOCKED}
   ```

3. **Fix Issues Systematically**
   - Update deprecated API calls
   - Update incompatible dependencies
   - Fix configuration issues
   - Document each fix in UPGRADE_SPRING_EXPLANATION.md

4. **Commit After Each Major Fix**
   ```bash
   git add {AFFECTED_FILES} UPGRADE_SPRING_EXPLANATION.md
   git commit -m "fix: resolve {ISSUE_TYPE} after Spring Boot upgrade

   {DETAILED_DESCRIPTION}

   See UPGRADE_SPRING_EXPLANATION.md for details.

   🤖 Generated with [Claude Code](https://claude.com/claude-code)

   Co-Authored-By: Claude <noreply@anthropic.com>"
   ```

**Iterate**: Continue fixing and building until `mvn clean compile` succeeds.

## Step 7: Update Dependencies

**NOTE**: OpenRewrite typically updates many Spring-related dependencies automatically. This step focuses on dependencies that OpenRewrite may have missed or that need specific version updates.

### Check for Dependency Updates
```bash
# Check for available dependency updates
mvn versions:display-dependency-updates

# Check for plugin updates
mvn versions:display-plugin-updates
```

### Update Spring-Related Dependencies
Focus on Spring ecosystem dependencies:

```xml
<!-- Example dependencies that may need updates -->
- springdoc-openapi-ui
- spring-data-jdbc
- spring-security
- Any other Spring-related libraries
```

### Document Dependency Changes
**Update UPGRADE_SPRING_EXPLANATION.md**:

```markdown
## Dependency Updates
### Updated Dependencies
| Dependency | Old Version | New Version | Reason |
|------------|-------------|-------------|---------|
| springdoc-openapi-ui | {OLD} | {NEW} | Compatibility with Spring Boot {TARGET_VERSION} |

### Dependencies Checked but Not Updated
- {DEPENDENCY_NAME}: {REASON_NOT_UPDATED}
```

### Commit Dependency Changes
```bash
git add pom.xml UPGRADE_SPRING_EXPLANATION.md
git commit -m "chore: update Spring-related dependencies for compatibility

Updated dependencies to ensure compatibility with Spring Boot {TARGET_VERSION}.

See UPGRADE_SPRING_EXPLANATION.md for full dependency change list.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

## Step 8: Configuration Updates

**NOTE**: OpenRewrite typically updates many configuration properties automatically. This step focuses on properties that OpenRewrite may have missed or custom configuration specific to your application.

### Check Application Properties
Review and update configuration files:

```bash
# Check for application configuration files
ls -la src/main/resources/application*.properties
ls -la src/main/resources/application*.yml
```

### Update Deprecated Configuration Properties
Based on release notes, update any deprecated configuration properties.

### Document Configuration Changes
**Update UPGRADE_SPRING_EXPLANATION.md**:

```markdown
## Configuration Changes
### Updated Properties
- `{OLD_PROPERTY}` → `{NEW_PROPERTY}` (deprecated in {VERSION})
- Reason: {EXPLANATION}

### New Configuration Added
- `{NEW_PROPERTY}`: {DESCRIPTION}

### Configuration Removed
- `{OLD_PROPERTY}`: {REASON_FOR_REMOVAL}
```

### Commit Configuration Changes
```bash
git add src/main/resources/ UPGRADE_SPRING_EXPLANATION.md
git commit -m "chore: update application configuration for Spring Boot {TARGET_VERSION}

Updated deprecated configuration properties and added new required settings.

See UPGRADE_SPRING_EXPLANATION.md for details.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

## Step 9: Run Comprehensive Tests

### Execute Full Test Suite
```bash
# Run all tests with coverage
mvn clean test

# Run integration tests
mvn clean verify
```

### Analyze Test Results
1. **Document Test Execution**:
   ```markdown
   ## Test Results After Upgrade
   ### Unit Tests
   - Total Tests: {COUNT}
   - Passing: {COUNT}
   - Failing: {COUNT}
   - Skipped: {COUNT}

   ### Failing Tests Analysis
   {IF_ANY_FAILURES_LIST_THEM}
   ```

2. **Fix Failing Tests**:
   - Analyze each failure
   - Determine if caused by:
     - API changes in Spring Boot
     - Test framework updates needed
     - Application code issues
     - Configuration changes

3. **Document Each Fix**:
   ```markdown
   ### Test Fix: {TEST_NAME}
   - **Original Failure**: {ERROR_MESSAGE}
   - **Root Cause**: {ANALYSIS}
   - **Fix Applied**: {DESCRIPTION}
   - **Files Modified**: {FILE_LIST}
   ```

4. **Commit Test Fixes**:
   ```bash
   git add {TEST_FILES} UPGRADE_SPRING_EXPLANATION.md
   git commit -m "test: fix tests after Spring Boot {TARGET_VERSION} upgrade

   {DESCRIPTION_OF_FIXES}

   See UPGRADE_SPRING_EXPLANATION.md for detailed test fix log.

   🤖 Generated with [Claude Code](https://claude.com/claude-code)

   Co-Authored-By: Claude <noreply@anthropic.com>"
   ```

**Iterate**: Continue until all tests pass.

## Step 10: Verify Application Functionality

### Build Complete Application
```bash
# Full build with all tests
mvn clean install
```

### Start Application
```bash
# Run the application
mvn spring-boot:run
```

### Verify Key Endpoints
Test critical application endpoints:

```bash
# Wait for application to start
sleep 10

# Test health endpoint
curl http://localhost:8080/actuator/health

# Test main API endpoints (adjust based on your application)
curl http://localhost:8080/api/...

# Test OpenAPI documentation
curl http://localhost:8080/v3/api-docs
```

### Document Application Verification
**Update UPGRADE_SPRING_EXPLANATION.md**:

```markdown
## Application Verification
### Startup Status
- Application started: {SUCCESS/FAIL}
- Startup time: {TIME}
- Errors during startup: {YES/NO}

### Endpoint Testing Results
| Endpoint | Status | Response Time | Notes |
|----------|--------|---------------|-------|
| /actuator/health | {200/ERROR} | {TIME} | {NOTES} |
| /api/endpoint1 | {200/ERROR} | {TIME} | {NOTES} |
| /v3/api-docs | {200/ERROR} | {TIME} | {NOTES} |

### Issues Found
{LIST_ANY_RUNTIME_ISSUES}

### OpenAPI/Swagger UI
- Swagger UI accessible: {YES/NO}
- API documentation generated: {YES/NO}
```

### Fix Any Runtime Issues
If issues are found:
1. Analyze and fix the issues
2. Document in UPGRADE_SPRING_EXPLANATION.md
3. Commit the fixes
4. Re-verify application

## Step 11: Code Quality and Security Checks

### Run Static Analysis
```bash
# Run SpotBugs if configured
mvn spotbugs:check

# Run Checkstyle if configured
mvn checkstyle:check
```

### Run Security Checks
```bash
# Check for vulnerable dependencies
mvn dependency:analyze
mvn versions:display-dependency-updates
```

### Document Quality Checks
**Update UPGRADE_SPRING_EXPLANATION.md**:

```markdown
## Code Quality and Security
### Static Analysis
- SpotBugs: {PASS/FAIL} - {ISSUE_COUNT} issues
- Checkstyle: {PASS/FAIL} - {ISSUE_COUNT} issues

### Security Analysis
- Vulnerable dependencies: {COUNT}
- Critical vulnerabilities: {COUNT}
- Recommended updates: {LIST}

### Actions Taken
{LIST_ANY_FIXES_APPLIED}
```

## Step 12: Performance Comparison (Optional but Recommended)

### Document Performance Metrics
**Update UPGRADE_SPRING_EXPLANATION.md**:

```markdown
## Performance Analysis
### Build Performance
- Build time (before): {TIME}
- Build time (after): {TIME}
- Change: {FASTER/SLOWER by X%}

### Application Performance
- Startup time (before): {TIME}
- Startup time (after): {TIME}
- Change: {FASTER/SLOWER by X%}

### Memory Usage
- Memory usage at startup: {BEFORE} → {AFTER}
```

## Step 13: Finalize Documentation

### Add Executive Summary
**Update UPGRADE_SPRING_EXPLANATION.md** at the top:

```markdown
## Executive Summary
✅ **Upgrade Status**: SUCCESSFUL

### Key Metrics
- **Duration**: {TIME_TAKEN}
- **Files Modified**: {COUNT}
- **Tests Status**: {PASSING_COUNT}/{TOTAL_COUNT} passing
- **Build Status**: ✅ SUCCESS
- **Application Status**: ✅ RUNNING

### Major Changes
1. {SUMMARY_OF_MAJOR_CHANGE_1}
2. {SUMMARY_OF_MAJOR_CHANGE_2}
3. {SUMMARY_OF_MAJOR_CHANGE_3}

### Breaking Changes Handled
- {LIST_BREAKING_CHANGES_ADDRESSED}

### Known Issues
- {LIST_ANY_KNOWN_ISSUES_OR_NONE}

### Recommendations
- {ANY_FOLLOW_UP_RECOMMENDATIONS}

### Testing Performed
- ✅ Unit tests
- ✅ Integration tests
- ✅ Application startup
- ✅ Endpoint verification
- ✅ OpenAPI documentation

### Upgrade Safety
**Risk Level**: {LOW/MEDIUM/HIGH}
- This upgrade {has/has not} breaking changes
- All tests passing
- Application verified functional
- Ready for deployment
```

### Add References Section
```markdown
## References
### Spring Boot Documentation
- [Spring Boot {TARGET_VERSION} Release Notes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-{TARGET_VERSION}-Release-Notes)
- [Spring Boot {TARGET_VERSION} Documentation](https://docs.spring.io/spring-boot/docs/{TARGET_VERSION}/reference/html/)
- [Configuration Changelog](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-{TARGET_VERSION}-Configuration-Changelog)

### Migration Guides
- [Migrating from {PREVIOUS_MAJOR_VERSION} to {CURRENT_MAJOR_VERSION}](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-{VERSION}-Migration-Guide)

---
*Document generated and maintained by upgrade-springboot skill*
*Last updated: {TIMESTAMP}*
```

## Step 14: Create Pull Request

### Final Commit
Ensure all changes are committed:

```bash
# Check status
git status

# Add any remaining files
git add .

# Final commit if needed
git commit -m "docs: finalize Spring Boot {TARGET_VERSION} upgrade documentation

Complete UPGRADE_SPRING_EXPLANATION.md with all upgrade details, test results, and verification.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

### Push Branch
```bash
git push -u origin upgrade/spring-boot-{CURRENT_VERSION}-to-{TARGET_VERSION}
```

### Create Pull Request
```bash
gh pr create --title "Upgrade Spring Boot from {CURRENT_VERSION} to {TARGET_VERSION}" --body "$(cat <<'EOF'
## Summary
Upgrade Spring Boot framework from version {CURRENT_VERSION} to {TARGET_VERSION}.

## Changes Made
- ✅ Updated Spring Boot parent version in pom.xml
- ✅ Updated compatible Spring ecosystem dependencies
- ✅ Fixed deprecated API usage
- ✅ Updated application configuration
- ✅ Fixed and verified all tests
- ✅ Verified application functionality
- ✅ Created comprehensive upgrade documentation

## Documentation
See `UPGRADE_SPRING_EXPLANATION.md` for complete upgrade details including:
- Pre and post-upgrade state comparison
- Breaking changes analysis and handling
- All issues encountered and resolutions
- Test results and verification steps
- Performance analysis
- Security and dependency updates

## Test Plan
- ✅ All unit tests passing ({TEST_COUNT} tests)
- ✅ All integration tests passing
- ✅ Application starts successfully
- ✅ All API endpoints verified functional
- ✅ OpenAPI documentation accessible
- ✅ Health checks passing
- ✅ No critical security vulnerabilities

## Build Verification
- ✅ `mvn clean install` - SUCCESS
- ✅ `mvn spring-boot:run` - Application starts
- ✅ Static analysis checks - PASSED
- ✅ Code coverage maintained

## Breaking Changes
{LIST_BREAKING_CHANGES_OR_NONE}

## Migration Impact
- **Risk Level**: {LOW/MEDIUM/HIGH}
- **Rollback Plan**: Revert this PR and redeploy previous version

## Verification Steps for Reviewers
1. Review `UPGRADE_SPRING_EXPLANATION.md` for detailed upgrade log
2. Run `mvn clean install` to verify build
3. Run `mvn spring-boot:run` to verify application startup
4. Test key endpoints
5. Review dependency changes in pom.xml

## References
- [Spring Boot {TARGET_VERSION} Release Notes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-{TARGET_VERSION}-Release-Notes)
- See UPGRADE_SPRING_EXPLANATION.md for complete reference list

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

## Step 15: Post-PR Validation

### Document PR Details
**Update UPGRADE_SPRING_EXPLANATION.md**:

```markdown
## Pull Request
- **PR Number**: #{PR_NUMBER}
- **PR URL**: {PR_URL}
- **Status**: Open
- **Created**: {TIMESTAMP}

### PR Checklist
- ✅ All changes committed
- ✅ Documentation complete
- ✅ Tests passing
- ✅ Build successful
- ✅ Ready for review
```

### Commit Documentation Update
```bash
git add UPGRADE_SPRING_EXPLANATION.md
git commit -m "docs: add PR reference to upgrade documentation"
git push
```

## Common Issues and Solutions

### Issue: OpenRewrite Recipe Execution Failed
**Impact**: Automated migration didn't complete

**Solution**:
1. Check Maven logs for specific errors
2. Verify internet connectivity (OpenRewrite downloads recipe artifacts)
3. Try running with `-X` flag for debug output: `mvn -X org.openrewrite.maven:rewrite-maven-plugin:run ...`
4. If recipe not available for your version, proceed with manual upgrade
5. Document the manual approach in UPGRADE_SPRING_EXPLANATION.md

### Issue: OpenRewrite Made Unexpected Changes
**Impact**: Some code changes don't look correct

**Solution**:
1. Review the OpenRewrite changes carefully using `git diff`
2. Revert specific problematic changes if needed
3. Document why certain OpenRewrite changes were reverted
4. Consider filing an issue with OpenRewrite if the recipe has bugs
5. Proceed with manual fixes for those specific issues

### Issue: Major Version Upgrade (2.x → 3.x)
**Impact**: Requires Java 17+, Jakarta EE namespace migration

**Solution**:
1. Verify Java 17+ is installed
2. Run OpenRewrite recipe for Spring Boot 3.0 migration (handles `javax.*` → `jakarta.*`)
3. Review OpenRewrite changes for completeness
4. Manually update any missed `javax.*` imports to `jakarta.*`
5. Update dependency versions for Jakarta compatibility
6. Review and test security configurations
7. Document all namespace changes in UPGRADE_SPRING_EXPLANATION.md

### Issue: Deprecated API Usage
**Solution**:
1. Identify all deprecation warnings during build
2. Check release notes for replacement APIs
3. Update code to use new APIs
4. Document each change in UPGRADE_SPRING_EXPLANATION.md
5. Test thoroughly after each change

### Issue: Test Failures After Upgrade
**Solution**:
1. Analyze each failing test individually
2. Check if Spring testing framework behavior changed
3. Update test configuration if needed
4. Review mock and assertion frameworks compatibility
5. Document test fixes in UPGRADE_SPRING_EXPLANATION.md

### Issue: Configuration Properties Changed
**Solution**:
1. Review configuration changelog from Spring Boot release notes
2. Update application.properties/yml with new property names
3. Remove deprecated properties
4. Test application with new configuration
5. Document all configuration changes

### Issue: Dependency Conflicts
**Solution**:
1. Run `mvn dependency:tree` to identify conflicts
2. Use `<dependencyManagement>` to force compatible versions
3. Exclude conflicting transitive dependencies
4. Verify all critical dependencies are compatible
5. Document dependency resolutions

## Important Reminders

1. **Use OpenRewrite First**: Always run the appropriate OpenRewrite recipe before manual changes
2. **Review OpenRewrite Changes**: Carefully review all automated changes with `git diff`
3. **Documentation First**: Create and update UPGRADE_SPRING_EXPLANATION.md throughout the entire process
4. **Document OpenRewrite Results**: Record what OpenRewrite changed and what still needs manual work
5. **Commit Frequently**: Commit after OpenRewrite, then after each logical manual step
6. **Test Continuously**: Run tests after OpenRewrite and after each significant change
7. **Document Everything**: Every issue, fix, and decision should be in UPGRADE_SPRING_EXPLANATION.md
8. **Verify Functionality**: Always verify the application runs and endpoints work
9. **Security First**: Check for vulnerable dependencies after upgrade
10. **Follow Semantic Versioning**: Understand the implications of major/minor/patch upgrades
11. **Read Release Notes**: Always review official Spring Boot release notes
12. **Maintain History**: Keep UPGRADE_SPRING_EXPLANATION.md in the repository for future reference
13. **Create Comprehensive PR**: Include all relevant information for reviewers

## Success Criteria

The upgrade is complete when:
- ✅ Appropriate OpenRewrite recipe executed successfully
- ✅ OpenRewrite changes reviewed and committed
- ✅ Target Spring Boot version updated in pom.xml
- ✅ All dependencies compatible and updated
- ✅ Build succeeds: `mvn clean install`
- ✅ All tests pass (unit + integration)
- ✅ Application starts without errors
- ✅ All key endpoints verified functional
- ✅ OpenAPI documentation accessible
- ✅ No critical security vulnerabilities
- ✅ Static analysis passes
- ✅ UPGRADE_SPRING_EXPLANATION.md complete and comprehensive (including OpenRewrite results)
- ✅ All changes committed with descriptive messages
- ✅ Branch pushed to remote repository
- ✅ Pull request created with complete description
- ✅ PR includes reference to UPGRADE_SPRING_EXPLANATION.md

---

*This skill automates Spring Boot upgrades while maintaining comprehensive documentation and ensuring application stability.*
