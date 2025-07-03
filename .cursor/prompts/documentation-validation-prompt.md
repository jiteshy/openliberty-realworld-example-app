# Documentation Validation & Correction Prompt

You are a senior software architect tasked with validating the accuracy of generated project documentation against the actual source code. Your goal is to identify false claims, gaps, and inaccuracies, then provide corrections for both the documentation and the generation prompt.

**Input**: 
- Generated documentation (markdown file)
- Access to the complete source codebase
- The documentation generation prompt used

**Output**: 
- Updated documentation with corrections
- Recommended improvements to the documentation generation prompt
- Validation report with findings

## Validation Methodology

### Phase 1: Critical Accuracy Verification

#### 1.1 Package Manager Verification
**Check**: Does the documentation consistently use the correct package manager?
- Search for `yarn.lock`, `package-lock.json`, or `pnpm-lock.yaml`
- Verify all installation commands use the correct package manager
- **Red Flags**: Mixed npm/yarn commands, recommending alternatives

#### 1.2 Testing Claims Verification
**Check**: Are testing capabilities accurately documented?
- Search for `*.spec.ts`, `*.test.js`, or similar test files
- Verify testing tools mentioned exist in package.json
- Check if test commands actually work
- **Red Flags**: Detailed testing documentation without test files, coverage targets without tests

#### 1.3 Routing Strategy Verification
**Check**: Is the routing configuration accurately described?
- Examine `app.config.ts`, router configuration files
- Look for `useHash: true` or hash routing configuration
- Verify routing strategy claims (hash-based vs standard)
- **Red Flags**: Claiming hash routing without configuration, incorrect routing descriptions

#### 1.4 Environment Configuration Verification
**Check**: Are environment claims accurate?
- Search for `environment.ts`, `.env`, or environment files
- Check for hardcoded vs configurable API endpoints
- Verify environment variable usage claims
- **Red Flags**: Claiming configurability without environment files, contradictory statements

#### 1.5 Technology Stack Verification
**Check**: Are all mentioned technologies actually used?
- Verify all dependencies exist in package.json
- Check if claimed frameworks/libraries are actually imported and used
- Validate version numbers against package.json
- **Red Flags**: Mentioning unused dependencies, incorrect versions

### Phase 2: Feature Accuracy Verification

#### 2.1 API Integration Verification
**Check**: Are API claims accurate?
- Examine interceptors, HTTP client configuration
- Verify API endpoint documentation against actual service calls
- Check authentication implementation (JWT, localStorage, etc.)
- **Red Flags**: Incorrect API patterns, wrong endpoint documentation

#### 2.2 State Management Verification
**Check**: Is state management accurately described?
- Examine services for BehaviorSubjects, state patterns
- Verify component state management claims
- Check for external state libraries (NgRx, Akita) vs service-based state
- **Red Flags**: Claiming state libraries that don't exist, incorrect state patterns

#### 2.3 Component Architecture Verification
**Check**: Is component architecture accurately documented?
- Verify standalone vs module-based components
- Check component hierarchy claims
- Examine actual import/export patterns
- **Red Flags**: Incorrect component patterns, wrong architectural claims

### Phase 3: Command and Setup Verification

#### 3.1 Installation Commands Verification
**Check**: Do documented commands actually work?
- Verify each installation step against actual project setup
- Test that prerequisites are accurate and sufficient
- Check command availability and functionality
- **Red Flags**: Commands that would fail, missing prerequisites

#### 3.2 Development Commands Verification
**Check**: Are all documented commands functional?
- Verify scripts exist in package.json
- Test that commands produce expected outputs
- Check for commands that reference non-existent functionality
- **Red Flags**: Test commands without tests, build commands with wrong parameters

### Phase 4: File Structure and Naming Verification

#### 4.1 Directory Structure Verification
**Check**: Does documented structure match reality?
- Compare documented folder structure with actual project layout
- Verify claimed file organization patterns
- Check for missing or incorrectly described directories
- **Red Flags**: Non-existent directories, incorrect structure descriptions

#### 4.2 File Naming Convention Verification
**Check**: Are naming conventions accurately documented?
- Verify actual file naming patterns used in the project
- Check for claimed file types that don't exist
- Validate naming convention examples against real files
- **Red Flags**: Documenting conventions not followed, non-existent file types

## Validation Execution Instructions

### Step 1: Systematic Code Analysis
1. **Read the generated documentation completely**
2. **Create a checklist of all factual claims made**
3. **For each claim, verify against the actual codebase**
4. **Document discrepancies with evidence**

### Step 2: Evidence Collection
For each inaccuracy found:
```markdown
**Claim**: [Quote from documentation]
**Reality**: [What actually exists in code]
**Evidence**: [File path and relevant code snippet]
**Impact**: [How this affects developer experience]
```

### Step 3: Pattern Identification
Identify recurring types of inaccuracies:
- Generic assumptions vs. actual implementation
- Best practice documentation vs. what's implemented
- Technology claims vs. actual dependencies
- Command documentation vs. actual functionality

### Step 4: Correction Generation

#### 4.1 Documentation Corrections
For each inaccuracy, provide:
- **Corrected text** to replace the inaccurate content
- **Evidence** supporting the correction
- **Context** on why the original was wrong

#### 4.2 Generation Prompt Improvements
For each pattern of inaccuracy, suggest:
- **Specific validation steps** to add to the generation prompt
- **Warning phrases** to include about common false assumptions
- **Verification requirements** before documenting features

## Output Format

### Validation Report
```markdown
# Documentation Validation Report

## Summary
- Total claims verified: [number]
- Inaccuracies found: [number]
- Severity: [Critical/Major/Minor]

## Critical Issues
[List issues that would prevent successful project setup]

## Major Issues  
[List issues that would cause confusion or incorrect understanding]

## Minor Issues
[List cosmetic or clarity issues]

## Pattern Analysis
[Common types of inaccuracies and their root causes]
```

### Documentation Corrections
```markdown
# Corrected Documentation

[Provide the corrected sections with explanations]
```

### Generation Prompt Improvements
```markdown
# Recommended Prompt Improvements

## Additional Validation Steps
[Specific checks to add to prevent found issues]

## Enhanced Warning Language
[Stronger language about verification requirements]

## New Validation Rules
[Specific rules to prevent identified patterns]
```

## Quality Assurance Checklist

Before completing validation:
- [ ] Every factual claim in documentation has been verified against code
- [ ] Package manager usage is consistent throughout
- [ ] No testing documentation exists without actual test files
- [ ] Routing strategy matches actual implementation
- [ ] Environment configuration claims match reality
- [ ] All commands have been verified as functional
- [ ] Technology stack claims match package.json
- [ ] File structure documentation matches actual project
- [ ] No contradictory statements exist
- [ ] All corrections include supporting evidence

## Validation Execution

Execute this validation process thoroughly and provide detailed findings with specific evidence for each inaccuracy discovered. Focus on accuracy that impacts developer productivity and onboarding success. 