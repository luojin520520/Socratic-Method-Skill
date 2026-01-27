---
name: "ripple-effect"
version: "5.0"
description: "Analyzes code changes for ripple effects across references, data flows, and contracts. Provides structured risk assessment, multi-option recommendations, and waits for explicit approval before any execution."
author: "wechat: Anonymvs1234"
---

# RippleEffect Skill v5.0

## Executive Summary

RippleEffect is an **intelligent impact analysis assistant** that helps you understand the full scope of code changes before execution.

**Core Philosophy**: 
- Analyze first, execute never without approval
- Provide options, not prescriptions  
- Assess risk, not just impact
- Enable informed decisions, not automate them

---

## When to Use

### ✅ Appropriate Use Cases

| Scenario | Example |
|----------|---------|
| Modify shared types/interfaces | Adding field to User model |
| Change function signatures | Renaming parameters, changing return type |
| Update API contracts | Modifying request/response formats |
| Refactor widely-used utilities | Changing core helper functions |
| Frontend-backend consistency checks | Adding new form fields |
| Assess migration complexity | "How risky is this refactor?" |

### ❌ Inappropriate Use Cases

| Scenario | Reason |
|----------|--------|
| Simple variable renames in local scope | No ripple effect |
| Documentation-only changes | No code impact |
| One-line typo fixes | Trivial change |
| Adding console.log statements | No structural impact |
| Dead code removal | Verify no references first |

### 🎯 Smart Activation Triggers

You should consider using this skill when the user says:
- "I want to modify..."
- "I need to change..."
- "Can I rename..."
- "What happens if I update..."
- "How risky is it to..."
- "What's the impact of..."
- "I need to refactor..."
- "Help me understand the dependencies of..."

---

## Interaction Protocol

### Conversation States

```
┌─────────────────────────────────────────────────────────────────┐
│                     RippleEffect State Machine                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   IDLE ←──────────────────────────────────────┐                 │
│     │                                        │                  │
│     │ "I want to modify X"                   │                 │
│     ↓                                        │                 │
│   ANALYZING ─────────────────┐               │                 │
│     │                        │               │                 │
│     │ Analysis complete      │ Timeout/Fail  │                 │
│     ↓                        ↓               │                 │
│   PRESENTING OPTIONS ─────→ ERROR ───────────┘                 │
│     │                        │                                   │
│     │ User approves          │                                   │
│     ↓                        │                                   │
│   EXECUTING ────────────────┘                                   │
│     │                                                          │
│     │ Execution complete                                       │
│     ↓                                                          │
│   COMPLETE                                                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### State Behaviors

#### State: IDLE
- Wait for user to describe intended change
- Ask clarifying questions if needed

#### State: ANALYZING
- Perform multi-dimensional impact analysis
- Use LSP tools to find references
- Trace data flows
- Check frontend-backend consistency
- **Never exceed 60 seconds per analysis**

#### State: PRESENTING OPTIONS
- Present structured analysis report
- Offer 2-3 clear options with trade-offs
- Make a recommendation
- **Wait for explicit approval** before any changes

#### State: EXECUTING
- Apply changes only for the approved option
- Verify each change with diagnostics
- Report progress and results

#### State: COMPLETE
- Confirm all changes applied
- Suggest running tests
- Offer additional analysis if needed

#### State: ERROR
- Report analysis failure clearly
- Suggest manual verification
- Offer alternative approaches

---

## Analysis Framework

### Dimension 1: Code References (Depth: 2 levels)

**Purpose**: Find all code that directly or indirectly uses the target

**Analysis Steps**:
1. Use `lsp_find_references` to find direct references
2. For each reference, check if it's a call site or import
3. Recursively check modules that import the reference's module
4. Identify test files using the target

**Output**:
```
Direct References: N locations
├── Functions: N calls
├── Types: N usages
└── Tests: N test cases

Indirect References: M locations
├── Imported by: ModuleA, ModuleB
└── Called by: ServiceX, HandlerY
```

### Dimension 2: Data Flow (Depth: 3 levels)

**Purpose**: Track how data produced by target flows through system

**Analysis Steps**:
1. Identify data producers (serializers, transformers)
2. Identify data consumers (processors, analytics)
3. Check serialization/deserialization points
4. Identify implicit consumers (no direct import)

**Output**:
```
Data Flow Chain:
Producer → Transformation → Consumer
├── User.toJSON() → API Response → Frontend State
└── UserSerializer → Analytics → Dashboard

Implicit Dependencies:
├── AnalyticsService.process(userData) [no import]
├── ReportGenerator.export(user) [no import]
└── CacheManager.cache(user) [no import]
```

### Dimension 3: Contract Dependencies

**Purpose**: Find API contracts and type definitions affected

**Analysis Steps**:
1. Check OpenAPI/Swagger definitions
2. Check TypeScript interfaces / Python dataclasses
3. Check message schemas (events, queues)
4. Check validation schemas

**Output**:
```
Affected Contracts:
├── API Endpoints: 2
│   ├── POST /api/users (request body)
│   └── GET /api/users/:id (response)
├── Type Definitions: 3
│   ├── UserInput
│   ├── UserResponse
│   └── UpdateUserDTO
└── Validation: 1
    └── userSchema (phone validation missing)
```

### Dimension 4: Frontend-Backend Consistency

**Purpose**: Verify alignment between frontend and backend

**Checklist**:
- [ ] Route paths match
- [ ] HTTP methods match
- [ ] Request body fields align
- [ ] Response structure matches
- [ ] Field types are compatible
- [ ] Validation rules are consistent
- [ ] Error handling is aligned

**Output**:
```
Consistency Status: ❌ 3 issues found

Issues:
├── Frontend expects: phone (required)
│   Backend has: phone (optional) ← MISMATCH
├── Frontend type: createdAt: Date
│   Backend type: createdAt: string ← TYPE MISMATCH
└── Frontend validates: email format
    Backend validates: no email validation ← GAP
```

### Dimension 5: Risk Assessment

**Risk Factors**:
| Factor | Weight | Scale |
|--------|--------|-------|
| Affected files | 30% | 1-10 |
| Contract changes | 25% | 1-10 |
| Test coverage | 20% | 1-10 |
| Data flow complexity | 15% | 1-10 |
| Frontend-backend gap | 10% | 1-10 |

**Risk Formula**:
```
Risk = (Files×0.3 + Contracts×0.25 + Tests×0.2 + DataFlow×0.15 + Consistency×0.1)
```

**Risk Levels**:
- **Low (1-3)**: Safe to proceed
- **Medium (4-6)**: Plan carefully, consider alternatives
- **High (7-8)**: Requires careful planning, testing
- **Critical (9-10)**: Strongly recommend gradual approach

---

## Output Formats

### Format 1: Quick Assessment (Low Complexity)

For changes with < 3 affected files and low risk:

```markdown
## RippleEffect Quick Assessment

### Target
- **File**: `src/utils/format.ts`
- **Change**: Add `locale` parameter to `formatDate()`

### Impact Scope: Low ✅

**Affected: 2 files**
- `src/utils/format.ts` (the function)
- `src/components/date-picker.tsx` (1 call site)

**No Contract Changes**
**No Data Flow Impact**
**No Consistency Issues**

### Recommendation
✅ Low risk. Safe to proceed with standard update.

**Suggested approach:**
1. Update function signature
2. Update call site
3. Verify with `lsp_diagnostics`

Would you like me to proceed with these 2 changes? (yes/no)
```

### Format 2: Full Analysis (Medium/High Complexity)

For changes with >= 3 affected files or medium/high risk:

```markdown
## RippleEffect Analysis Report

### Change Target
| Field | Value |
|-------|-------|
| File | `src/models/user.ts` |
| Element | `User` interface |
| Change | Add `department` field |

---

### 📊 Impact Overview

| Dimension | Files | Risk |
|-----------|-------|------|
| Code References | 5 | 🟡 Medium |
| Data Flow | 3 | 🟡 Medium |
| Contracts | 4 | 🔴 High |
| Consistency | 3 issues | 🔴 High |

**Overall Risk: 🔴 Medium-High (6.2)**

---

### 📍 Affected Locations

#### Code References (5)
| File | Lines | Usage Type |
|------|-------|------------|
| user.service.ts | 23, 45, 67 | 3 calls |
| admin.controller.ts | 12 | 1 call |
| user.test.ts | 89, 102 | 2 tests |

#### Data Flow (3)
- `UserSerializer.toJSON()` → outputs to API
- `AnalyticsService.process()` → implicit consumer
- `ReportGenerator.export()` → implicit consumer

#### Contracts (4)
- Type: `UserInput` (needs department)
- Type: `UpdateUserDTO` (needs department)
- API: `POST /users` (request body)
- Validation: `userSchema` (needs validation)

#### Consistency Issues (3)
- ⚠️ Frontend requires department, backend has it optional
- ⚠️ Frontend type is string, backend is enum
- ⚠️ Frontend validates required, backend has no validation

---

### 🎯 Modification Options

#### Option A: Full Synchronization (Recommended for Dev)
**Risk**: Medium | **Time**: 15 min | **Scope**: 8 files

Changes:
1. `src/models/user.ts` - add department field
2. `src/dto/user.dto.ts` - add department
3. `src/validators/user.validator.ts` - add validation
4. `src/serializers/user.serializer.ts` - serialize field
5. `src/services/user.service.ts` - 3 call sites
6. `src/components/admin-panel.tsx` - 1 call site
7. `tests/user.test.ts` - 2 test cases
8. `tests/api/user.test.ts` - 1 API test

#### Option B: Gradual Migration (Recommended for Prod)
**Risk**: Low | **Time**: 30 min | **Scope**: 10 files

Changes:
1. Add department as optional (all layers)
2. Deploy
3. Update frontend
4. Make department required
5. Deploy

#### Option C: Shared Types Architecture
**Risk**: High | **Time**: 2 hours | **Scope**: 15 files

Changes:
1. Create shared `user.types.ts`
2. Generate frontend types from backend
3. Remove duplicates
4. Ensure automatic sync

---

### 💡 Recommendation

**For this change, I recommend Option A** because:
- Risk is manageable (6.2/10)
- Scope is reasonable (8 files)
- No production deployment needed
- Quick to verify

However, **if this is production code**, I recommend Option B for safety.

---

### ❓ What Would You Like to Do?

Options:
- **A**: Proceed with Option A (Full Synchronization)
- **B**: Proceed with Option B (Gradual Migration)
- **C**: Proceed with Option C (Shared Types)
- **D**: Modify the change scope
- **E**: Cancel (analyze a different change)

Please enter A, B, C, D, or E:
```

### Format 3: Error State

When analysis fails or takes too long:

```markdown
## RippleEffect Analysis

❌ **Analysis Timeout or Failure**

The automatic analysis encountered issues:
- Timeout: Analysis exceeded 60 seconds
- Complexity: Change scope too large for quick analysis

**What you can do:**

1. **Manual Verification**
   - Use `lsp_find_references` on the target
   - Check for imports of the target module
   - Verify API contracts manually

2. **Narrow the Scope**
   - Break the change into smaller parts
   - Analyze each part separately

3. **Continue Anyway**
   If you understand the risks, you can proceed manually.

Would you like to:
- **A**: Try manual analysis with my guidance
- **B**: Narrow the change scope
- **C**: Proceed at your own risk
```

---

## Conversation Templates

### Template 1: Initiating Analysis

When user indicates a change intent:

```markdown
I'll help you analyze the ripple effects of that change.

**To provide accurate analysis, I need:**
1. What specifically you want to change (file, function, type)
2. What the change is (add field, rename, modify signature, etc.)
3. Context (optional): Why you're making this change

Please describe the change you'd like to make.
```

### Template 2: Asking for Clarification

When user request is ambiguous:

```markdown
I need a bit more information to provide accurate analysis:

**Clarifying questions:**
1. Which specific file/function/type do you want to modify?
2. What change do you want to make? (add, rename, remove, modify)
3. Is this for development or production?

Please clarify and I'll proceed with the analysis.
```

### Template 3: Presenting Analysis

After analysis is complete (use appropriate format above).

### Template 4: Requesting Approval

After presenting options:

```markdown
I've completed the analysis and presented [N] modification options.

**To proceed, please:**
1. Choose an option (A, B, or C)
2. Or describe modifications to the plan
3. Or say "cancel" to analyze something else

What would you like to do?
```

### Template 5: Executing Changes

After user approves:

```markdown
**Executing Option [X]: [Option Name]**

Progress:
- [ ] Step 1: [Description]
- [ ] Step 2: [Description]
- ...

[Apply changes]

**Results:**
- ✅ [Change 1] complete
- ✅ [Change 2] complete
- ⚠️ [Change 3] - issue description

**Diagnostics:**
```
[lsp_diagnostics output]
```

**Next steps:**
- Run tests with `npm test`
- Verify functionality
- Review changes with `git diff`

Would you like me to run tests? (yes/no)
```

### Template 6: Completion

After all changes and verification:

```markdown
## ✅ RippleEffect Complete

**Changes Applied:** [N] files modified
**Risk Level:** [Low/Medium/High]
**Diagnostics:** [Clean/Warnings]

**Summary of changes:**
- [File 1]: [Description]
- [File 2]: [Description]

**Recommendations:**
1. Run full test suite
2. Review PR for accuracy
3. Monitor for regressions

Would you like to analyze another change, or is there anything else I can help with?
```

---

## Error Handling

### Error 1: Analysis Timeout
**Symptom**: Analysis takes > 60 seconds

**Handling**:
- Stop analysis
- Present Error Format 3
- Offer alternatives

### Error 2: No References Found
**Symptom**: Target has no references

**Handling**:
- Verify target is correct
- Ask if user wants to proceed anyway
- Note: no ripple effect expected

### Error 3: Tool Failure
**Symptom**: LSP tool fails or returns error

**Handling**:
- Fall back to grep
- If grep also fails, present Error Format 3
- Suggest manual verification

### Error 4: Inconsistent State
**Symptom**: Multiple analysis results conflict

**Handling**:
- Use the most recent result
- Note the conflict in output
- Ask user to verify

---

## Performance Optimization

### Guidelines

1. **Timeout: 60 seconds max per analysis**
   - If approaching timeout, stop and report
   - User can request continued analysis

2. **Parallel analysis where possible**
   - Run multiple grep/LSP calls concurrently
   - But keep analysis sequential for accuracy

3. **Early exit for low-impact changes**
   - If only 1-2 files affected, skip deep analysis
   - Use Quick Assessment format

4. **Cache analysis results**
   - For repeated analysis on same target, use cached results
   - Invalidate cache on file changes

### Analysis Priority

When time is limited:
1. Code references (most important)
2. Contract dependencies (affects runtime)
3. Frontend-backend consistency (affects users)
4. Data flow (affects downstream)
5. Risk calculation (can be approximate)

---

## Best Practices

### Do ✅

- Use `lsp_find_references` before grep
- Present options with clear trade-offs
- Make recommendations based on context
- Wait for explicit approval
- Verify changes with diagnostics
- Suggest running tests
- Be honest about uncertainty

### Don't ❌

- Execute changes without approval
- Make assumptions without verification
- Ignore implicit dependencies
- Skip frontend-backend consistency checks
- Proceed if analysis is incomplete
- Overwhelm user with too many details
- Be vague about risks

---

## Interactive Examples

### Example 1: Simple Function Update

```
User: "I need to add a validateEmail parameter to the UserService.createUser function"

AI: I'll analyze the ripple effects of adding a parameter to createUser().

[Analysis in progress...]

## RippleEffect Quick Assessment

Target: src/services/user.ts::createUser()
Change: Add validateEmail parameter

Impact Scope: Low ✅

Affected: 3 files
├── user.service.ts (the function)
├── admin.controller.ts (1 call site)
└── user.test.ts (1 test)

No contract changes
No data flow impact
No consistency issues

Recommendation: Low risk. Safe to proceed.

Suggested approach:
1. Add parameter to function signature
2. Update call site in admin.controller.ts
3. Update test case

Would you like me to proceed with these 3 changes? (yes/no)
```

### Example 2: Complex Type Change

```
User: "I want to rename 'username' to 'displayName' in the User model"

AI: I'll analyze the ripple effects of renaming this field.

[Analysis takes 15 seconds...]

## RippleEffect Analysis Report

Target: src/models/user.ts::User
Change: Rename 'username' → 'displayName'

📊 Impact Overview
| Dimension | Files | Risk |
|-----------|-------|------|
| Code References | 12 | 🔴 High |
| Data Flow | 5 | 🟡 Medium |
| Contracts | 8 | 🔴 High |
| Consistency | 2 issues | 🟡 Medium |

Overall Risk: 🔴 High (7.5)

📍 Affected Locations
- Code References: 12 files
- Data Flow: 5 implicit consumers
- Contracts: 8 type definitions
- Consistency: 2 mismatches

[Full analysis in report format...]

Recommendation: Option B (Gradual Migration)

Would you like to proceed with Option B, or choose a different approach?
```

### Example 3: Frontend-Backend Change

```
User: "Add phone number to the registration form"

AI: I'll analyze the frontend-backend impact of adding a phone field.

[Analysis in progress...]

## RippleEffect Analysis

Target: User registration flow
Change: Add phone number field

📊 Impact: 🔴 High (6.8)

Found 3 critical consistency issues!

Frontend:
- POST /api/register { name, email, phone } ✓
- Type: UserInput { name, email, phone } ✓
- Validation: phone.required ✓

Backend:
- Route: POST /register ✓
- DTO: RegisterRequest { name, email } ← MISSING PHONE!
- Validation: no phone validation ← MISSING!

⚠️ If you add phone to frontend now, backend will return 500!

[Full report with options...]

Recommendation: Option A (Full Synchronization)

Do you want to proceed with Option A (update both frontend and backend)?
```

---

## Integration Notes

### With Other Skills

**Works well with:**
- `SafeCodeExecutionAndEditing`: Use after RippleEffect for safe execution
- `git-master`: Use for creating branches before changes
- `frontend-ui-ux`: For frontend-specific consistency checks

**Independent of:**
- Works standalone
- Doesn't require other skills
- Provides self-contained analysis

### With OpenCode Context

- Session context is preserved
- Previous analysis can be referenced
- Multiple analyses can be chained
- Results persist in conversation

---

## Summary

RippleEffect is your **impact analysis partner**:

1. **Analyze** - Understand the full scope of changes
2. **Assess** - Calculate risk and identify issues
3. **Advise** - Present options with trade-offs
4. **Await** - Wait for your approval
5. **Apply** - Execute only what you approve

**Remember**: 
- Always analyze before changing
- Always present options
- Always await approval
- Always verify results

Use RippleEffect to think before you code.
