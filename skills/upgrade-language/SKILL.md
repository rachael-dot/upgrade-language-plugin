# Upgrade Programming Language Version

A comprehensive guide for upgrading the programming language version in a project, ensuring stability, compatibility, and proper testing.

## Step 1: Evaluate Whether to Upgrade

Before upgrading, analyze these factors:

### Check Current Version Support Status
- Is the current version still supported?
- When does support end?
- Are there critical security vulnerabilities?

### Understand Release Types
For languages with LTS (Long-Term Support) models (Java, Node.js, etc.):
- **LTS Releases**: 2-8 years of support, production-ready, stable
- **Feature/Current Releases**: 6 months support, experimental features, frequent breaking changes

### Decision Matrix

| Factor | Upgrade | Stay |
|--------|---------|------|
| Current version EOL soon | ✅ | ❌ |
| Security vulnerabilities | ✅ | ❌ |
| Need new language features | ✅ | ❌ |
| Current version still supported 2+ years | ❌ | ✅ |
| Stable production application | Use LTS only | ✅ |
| Dependencies not compatible | ❌ | ✅ |

### Questions to Ask
1. **Is this a production application?** → Choose LTS versions
2. **What do dependencies support?** → Check framework compatibility
3. **What version do other services use?** → Consider ecosystem consistency
4. **How often can we upgrade?** → Avoid non-LTS if upgrades are difficult

## Step 2: Choose Target Version and Create Explanation Document

### Create Upgrade Decision Document

**IMPORTANT:** Before making any changes, create a file called `UPGRADE_EXPLANATION.md` in the project root. This file will document:
- The decision to upgrade (or not)
- The rationale behind version selection
- All actions taken during the upgrade process
- Any issues encountered and how they were resolved

**Document Structure:**
```markdown
# Language Upgrade Decision and Process Log

## Decision Summary
- **Language**: [Language name]
- **Current Version**: [Version]
- **Target Version**: [Version] or "No upgrade recommended"
- **Decision**: [Upgrade / Don't upgrade]
- **Decision Date**: [Date]

## Rationale

### Support Timeline Analysis
[Analysis of current and target version support status]

### Version Selection Logic
[Detailed explanation of why this specific version was chosen]

### Risk Assessment
[Compatibility concerns, breaking changes, etc.]

## Actions Taken

### 1. Version Reference Updates
[List each file updated and what changed]

### 2. Build and Test Results
[Document build output, test results, warnings]

### 3. Issues Encountered
[Any problems found and how they were resolved]

## Final Recommendation
[Summary and any follow-up actions needed]
```

### Autonomous Version Selection Logic

Claude should automatically determine the best target version using these criteria, and **document the decision in UPGRADE_EXPLANATION.md**:

**For LTS-Based Languages (Java, Node.js):**

1. **Check current version status:**
   - If EOL soon (< 1 year): Upgrade to latest LTS
   - If still supported long-term: Consider staying or upgrading to next LTS

2. **Analyze project context:**
   - Production apps: Choose most stable LTS
   - Sample/learning projects: Latest LTS acceptable
   - Check branch name for hints (e.g., "java25-update" suggests Java 25)
   - Check local environment version

3. **Decision matrix:**
   ```
   Current: Java 17 (LTS, still supported)
   Available:
     - Java 21 (LTS) - Battle-tested (Sept 2023), widely adopted
     - Java 25 (LTS) - Latest (Sept 2025), cutting-edge

   Decision Logic:
     IF (branch hints at version OR local env matches newer version) AND (sample/non-critical project):
       → Choose hinted/matching LTS version
     ELSE IF (production project):
       → Choose most mature LTS (e.g., Java 21)
     ELSE:
       → Choose latest stable LTS
   ```

**Development/Experimental:**
```
Latest feature release acceptable for learning, not production
```

### For Always-Stable Languages (Python, Go, Rust)

**Python:**
```
Current: 3.9
Target: 3.11 or 3.12
Why: Active support, performance improvements, new features
Avoid: 3.13 if too new (let it stabilize)
```

**Go:**
```
Current: 1.19
Target: Latest stable (e.g., 1.21)
Why: Go has strong backward compatibility
```

### Version Selection Checklist
- [ ] Target version is stable (not beta/RC)
- [ ] Major frameworks support it
- [ ] CI/CD platforms support it
- [ ] Docker base images available
- [ ] Cloud providers support it (AWS, GCP, Azure)
- [ ] Support period is adequate (2+ years for production)

## Step 3: Create Feature Branch

```bash
# Check the issue number if upgrading for a specific issue
gh issue view <issue-number>

# Create descriptive branch name
git checkout -b <issue-number>-upgrade-<language>-<from>-to-<to>

# Example:
git checkout -b 4-upgrade-java-17-to-21
```

## Step 4: Locate All Version References

### Common Files to Check

**Java/Maven:**
- `pom.xml` - `<maven.compiler.release>`, `<java.version>`
- `.github/workflows/*.yml` - `setup-java` actions
- `Dockerfile` - `FROM` base image
- `.tool-versions` or `.sdkmanrc` - Version managers
- `README.md` - Documentation

**Node.js:**
- `package.json` - `"engines": { "node": "..." }`
- `.github/workflows/*.yml` - `setup-node` actions
- `.nvmrc` - nvm version
- `Dockerfile`
- `README.md`

**Python:**
- `pyproject.toml` or `setup.py` - `python_requires`
- `.python-version` - pyenv
- `Dockerfile`
- `.github/workflows/*.yml` - `setup-python` actions
- `tox.ini`, `Pipfile`

**Go:**
- `go.mod` - `go 1.xx`
- `.github/workflows/*.yml`
- `Dockerfile`
- `README.md`

### Search Commands
```bash
# Search for version references (adjust pattern for your language)
grep -r "java-version\|java.version\|jdk" .github/
grep -r "<java.version>\|<maven.compiler" pom.xml
grep -r "node.*version\|engines" package.json
grep -r "python.*version\|python_requires" setup.py pyproject.toml
```

## Step 5: Update All References

**Remember to update `UPGRADE_EXPLANATION.md`** with each file you modify, documenting what changed and why.

### Update Build Configuration

**Maven (Java):**
```xml
<properties>
    <!-- Java version -->
    <maven.compiler.release>21</maven.compiler.release>
</properties>
```

**package.json (Node.js):**
```json
{
  "engines": {
    "node": ">=20.0.0"
  }
}
```

**pyproject.toml (Python):**
```toml
[project]
requires-python = ">=3.11"
```

**go.mod (Go):**
```go
go 1.21
```

### Update CI/CD Workflows

**GitHub Actions:**
```yaml
- name: Set up JDK 21
  uses: actions/setup-java@v4
  with:
    java-version: '21'
    distribution: 'temurin'
```

**Update ALL workflow files:**
- Build workflows
- Test workflows
- Docker build workflows
- Deployment workflows

### Update Docker Images
```dockerfile
# Before
FROM eclipse-temurin:17-jdk-alpine

# After
FROM eclipse-temurin:21-jdk-alpine
```

### Update Documentation
```markdown
# README.md
## Requirements
- Java 21 (LTS)
- Maven 3.9+
```

## Step 6: Build and Test

**Document all results in `UPGRADE_EXPLANATION.md`** including build output, test results, and any warnings or issues encountered.

### Verify Build Works
```bash
# Java
mvn clean compile

# Node.js
npm install && npm run build

# Python
pip install -e . && python -m build

# Go
go build ./...
```

### Run Full Test Suite
```bash
# Java
mvn test

# Node.js
npm test

# Python
pytest

# Go
go test ./...
```

### Check for Warnings
- Look for deprecation warnings
- Note any restricted API warnings
- Document compatibility issues
- Verify no test failures

### Test Application Startup
```bash
# Start the application and verify it runs
# Java Spring Boot example:
mvn spring-boot:run

# Verify health endpoint or basic functionality
curl http://localhost:8080/actuator/health
```

## Step 7: Commit Changes

### Review Changes
```bash
git status
git diff
```

### Stage Only Relevant Files
```bash
# Don't commit IDE files, build artifacts, etc.
# IMPORTANT: Include UPGRADE_EXPLANATION.md in the commit
git add UPGRADE_EXPLANATION.md pom.xml .github/workflows/ Dockerfile README.md
```

### Create Descriptive Commit
```bash
git commit -m "$(cat <<'EOF'
chore: upgrade <language> from <old> to <new>

Update project to use <language> <new> across all build configurations:
- Set compiler/runtime version in build files
- Update all CI/CD workflows
- Update Docker base images
- Verified build and tests pass successfully

Closes #<issue-number>

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

## Step 8: Push and Create PR

### Push Branch
```bash
git push -u origin <branch-name>
```

### Create Pull Request
```bash
gh pr create --base master --title "chore: upgrade <language> from <old> to <new>" --body "$(cat <<'EOF'
## Summary
This PR upgrades the project from <language> <old> to <language> <new>.

See `UPGRADE_EXPLANATION.md` for detailed decision rationale and process log.

## Rationale
- [Why this version was chosen - LTS status, support timeline, etc.]
- [What benefits it provides]
- [Support timeline comparison]

## Changes Made
- Updated build configuration (<file>)
- Updated CI/CD workflows:
  - `workflow1.yml`
  - `workflow2.yml`
- Updated Docker base image
- Updated documentation

## Test Plan
- [x] Build completes successfully
- [x] All unit tests pass (X tests, 0 failures)
- [x] Integration tests pass
- [x] Application starts correctly
- [x] [Any specific feature testing]

## Compatibility Notes
[Document any warnings, breaking changes, or compatibility considerations]

## Migration Notes
[If applicable, notes for other developers on what they need to do]

Closes #<issue-number>

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

## Step 9: Post-PR Checklist

- [ ] CI/CD pipeline passes
- [ ] Code review completed
- [ ] Documentation updated
- [ ] Team notified of version change
- [ ] Deployment runbook updated if needed

## Common Issues and Solutions

### Issue: Tests fail with new version
**Solution:**
1. Check test framework compatibility
2. Update test dependencies
3. Look for deprecated API usage in tests

### Issue: Dependencies incompatible
**Solution:**
1. Update dependencies to compatible versions
2. Check dependency release notes
3. Consider staging the upgrade (dependencies first, then language)

### Issue: CI/CD fails but local works
**Solution:**
1. Check CI/CD platform supports the version
2. Verify Docker base images exist
3. Update GitHub Actions versions (e.g., `setup-java@v4`)

### Issue: Warnings about deprecated APIs
**Solution:**
1. Document warnings in PR
2. Create follow-up issues to fix deprecations
3. Don't block upgrade if warnings are minor

## Language-Specific Considerations

### Java
- Prefer Temurin or Oracle JDK distributions
- Check Spring Boot version compatibility
- Consider module system (Java 9+) implications
- Note restricted API changes in newer versions

### Node.js
- Check npm version compatibility
- Verify native module compilation
- Test across different OS (Windows, Linux, macOS)
- Consider ESM vs CommonJS implications

### Python
- Check for f-string, type hint syntax changes
- Verify C extension compatibility
- Test with actual Python version (not just syntax check)
- Consider virtual environment implications

### Go
- Generally safe to upgrade (strong backward compatibility)
- Check go.mod and go.sum updates
- Verify vendored dependencies

## References

### Support Timelines
- [Java Roadmap](https://www.oracle.com/java/technologies/java-se-support-roadmap.html)
- [Node.js Releases](https://nodejs.org/en/about/releases/)
- [Python Status](https://devguide.python.org/versions/)
- [Go Releases](https://go.dev/doc/devel/release)

### Compatibility Matrices
- [Spring Boot System Requirements](https://docs.spring.io/spring-boot/system-requirements.html)
- [AWS Lambda Runtimes](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)
- [GitHub Actions Runner Images](https://github.com/actions/runner-images)
