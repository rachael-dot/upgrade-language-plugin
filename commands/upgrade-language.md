---
description: Upgrade programming language version with proper analysis and testing
---

You are helping to upgrade a programming language version in this project. Follow the comprehensive upgrade process defined in `.claude/skills/upgrade-language.md`.

**IMPORTANT:** You MUST create a file called `UPGRADE_EXPLANATION.md` at the start of this process to document:
- Why you chose to upgrade (or not)
- Which version you selected and the rationale
- Every action you take during the upgrade
- Build and test results
- Any issues encountered and how they were resolved

This file should be updated throughout the process and included in the final commit.

## Task Parameters

Extract from the user's request:
- **Language**: Which language to upgrade (Java, Node.js, Python, Go, etc.)
- **Current Version**: The version currently in use
- **Target Version**: The desired version (or help determine the best target)
- **Issue Number**: GitHub issue number if applicable

## Execution Steps

### 1. Initial Analysis
Read `.claude/skills/upgrade-language.md` and execute **Step 1: Evaluate Whether to Upgrade**:

- Check if the current version is still supported
- Determine if this is a production or development project
- Identify if the target version is LTS or feature release
- Analyze project type and requirements
- **Provide recommendation**: Should we upgrade? If yes, to which version?
- **Explain reasoning**: Support timelines, stability, compatibility
- **Create `UPGRADE_EXPLANATION.md`**: Start the decision document immediately

### 2. Autonomous Version Selection
If the user hasn't specified a target version:

- Research the language's release schedule
- Compare LTS vs feature releases
- **Automatically determine the best version** using the decision logic from the skill file:
  - Analyze project context (production vs sample/learning)
  - Check branch name for version hints
  - Check local environment version
  - Consider support timelines and stability
- **Make the decision autonomously** and explain your reasoning
- **Update `UPGRADE_EXPLANATION.md`** with your version selection rationale
- Proceed directly to implementation

### 3. Create Feature Branch
Execute **Step 3** from the skill:

- Verify issue number if provided
- Create appropriately named branch
- Confirm branch creation

### 4. Locate All References
Execute **Step 4** from the skill:

- Search for version references in:
  - Build configuration files
  - CI/CD workflows
  - Docker files
  - Documentation
  - Version manager files
- List all files that need updates
- Show current values found
- **Document findings in `UPGRADE_EXPLANATION.md`**

### 5. Update Configuration
Execute **Step 5** from the skill:

- Update build configuration
- Update all CI/CD workflows
- Update Docker images
- Update documentation
- Show diff of changes
- **Update `UPGRADE_EXPLANATION.md`** with each file modification and the changes made

### 6. Build and Test
Execute **Step 6** from the skill:

- Run clean build
- Run full test suite
- Test application startup
- Document any warnings or issues
- Verify all tests pass
- **Update `UPGRADE_EXPLANATION.md`** with detailed build and test results, including:
  - Build success/failure
  - Test pass/fail counts
  - Any warnings or deprecation notices
  - Performance observations
  - Issues encountered and resolutions

### 7. Create Commit
Execute **Step 7** from the skill:

- **Finalize `UPGRADE_EXPLANATION.md`** with final summary and recommendations
- Review changes
- Stage appropriate files (including `UPGRADE_EXPLANATION.md`)
- Create descriptive commit with:
  - Conventional commit format
  - Detailed description
  - Issue reference
  - Co-authorship attribution

### 8. Push and Create PR
Execute **Step 8** from the skill:

- Push branch to remote
- Create PR with comprehensive description including:
  - Summary of changes
  - **Reference to `UPGRADE_EXPLANATION.md` for detailed rationale**
  - Rationale for version choice
  - Test results
  - Compatibility notes
  - Issue closure reference

### 9. Report Results
Provide final summary:

- PR URL
- Build status
- Test results
- Any warnings or concerns
- Next steps

## Important Guidelines

1. **Always consult the skill file** (`.claude/skills/upgrade-language.md`) for detailed instructions
2. **Create and maintain `UPGRADE_EXPLANATION.md`** - This is MANDATORY. Update it throughout the process with decision rationale and all actions taken
3. **Verify LTS status** before selecting versions
4. **Make autonomous decisions** - use the decision logic to select the best version without user input
5. **Update ALL references** - missing one breaks CI/CD
6. **Run tests completely** - don't skip verification
7. **Document warnings** - even if tests pass
8. **Be objective** about trade-offs and explain your version choice clearly
9. **Include `UPGRADE_EXPLANATION.md` in the commit** - Don't forget to stage this file

## Example Usage

```bash
/upgrade-language Java 17 to 21
/upgrade-language Node.js 16
/upgrade-language Python 3.9 to 3.11 for issue #42
```

## Error Handling

If issues arise:
- Consult **Step 9: Common Issues and Solutions** in the skill
- Document problems clearly
- Suggest solutions from the skill file
- Don't proceed if tests fail - investigate first

## Output Format

Use the TodoWrite tool to track progress through the upgrade steps:
1. Analyze upgrade feasibility and create UPGRADE_EXPLANATION.md
2. Autonomously select target version and document rationale
3. Create feature branch
4. Locate all version references
5. Update all configurations (updating explanation file as you go)
6. Run build and tests (documenting results)
7. Finalize UPGRADE_EXPLANATION.md with summary
8. Create commit (including UPGRADE_EXPLANATION.md)
9. Push and create PR

Provide clear, structured output at each step with explanations for your autonomous decisions.

**CRITICAL:** The `UPGRADE_EXPLANATION.md` file is a living document that should be created at the start and updated throughout the entire process. It provides transparency into your decision-making and serves as a record of the upgrade process.
