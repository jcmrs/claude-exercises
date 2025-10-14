# Agent OS: Workflows Structure and API Mapping Analysis

## Executive Summary

This document provides comprehensive analysis of the Agent OS workflows system, including directory organization, file patterns, API mappings, and template inclusion patterns. Workflows define reusable processes and procedural knowledge that can be included in commands and agent definitions.

**Key Findings:**
- 22 workflow files organized across 3 phases (implementation, planning, specification)
- Hierarchical structure with sub-phases (e.g., implementation/verification)
- Workflows are pure Markdown files with no special format requirements
- Workflows are included in templates via `{{workflows/{phase}/{workflow-name}}}` syntax

---

## Workflow Inventory

### Workflows by Phase

| Phase | Files | Sub-Phases | Purpose |
|-------|-------|------------|---------|
| `implementation/` | 11 | 1 (verification) | Implementation execution and verification |
| `planning/` | 4 | 0 | Product planning and roadmap creation |
| `specification/` | 5 | 0 | Specification creation and validation |
| **Total** | **22** | **1** | Complete workflow library |

### Complete File Tree

```
profiles/default/workflows/
├── implementation/
│   ├── analyze-patterns.md
│   ├── document-implementation.md
│   ├── implement-task.md
│   ├── implementer-responsibilities.md
│   ├── update-tasks-list.md
│   ├── verification/
│   │   ├── create-verification-report.md
│   │   ├── run-all-tests.md
│   │   ├── update-roadmap.md
│   │   ├── verify-documentation.md
│   │   └── verify-tasks.md
│   └── verifier-responsibilities.md
├── planning/
│   ├── create-product-mission.md
│   ├── create-product-roadmap.md
│   ├── create-product-tech-stack.md
│   └── gather-product-info.md
└── specification/
    ├── create-tasks-list.md
    ├── initialize-spec.md
    ├── research-spec.md
    ├── verify-spec.md
    └── write-spec.md
```

---

## Directory Structure

### Phase Organization

**Pattern:** `profiles/{profile}/workflows/{phase}/{workflow-name}.md`

**Phases:**
1. **implementation/** - Task implementation workflows and verification processes
2. **planning/** - Product planning, roadmap creation, and tech stack definition
3. **specification/** - Specification creation, research, and validation

### Sub-Phase Organization

**Pattern:** `profiles/{profile}/workflows/{phase}/{sub-phase}/{workflow-name}.md`

**Current Sub-Phases:**
- `implementation/verification/` - Verification-specific workflows

**Extensibility:** Additional sub-phases can be added as needed (e.g., `planning/architecture/`, `specification/analysis/`)

### File Count Breakdown

```
implementation/        ██████ 6 files (top-level)
implementation/verification/ █████ 5 files (sub-phase)
planning/             ████ 4 files
specification/        █████ 5 files
```

---

## File Naming Conventions

### Workflow Files

**Pattern:** `{descriptive-name}.md`

**Rules:**
- Lowercase only
- Hyphen-separated words
- Descriptive of the workflow's purpose
- Verb-based naming preferred (e.g., "create", "verify", "implement")
- No version numbers or dates in filename
- `.md` extension required

**Valid Examples:**
```
implement-task.md
create-product-mission.md
verify-spec.md
analyze-patterns.md
update-tasks-list.md
```

**Invalid Examples (Avoid):**
```
Implement-Task.md      # Not lowercase
implement_task.md      # Underscore instead of hyphen
task-implementation.md # Noun-based (prefer verb-based)
implement-v2.md        # Version in filename
implement              # No extension
```

### Phase/Sub-Phase Names

**Rules:**
- Lowercase, singular or plural
- Descriptive of the workflow category
- No special characters except hyphen

**Current Phases:**
- `implementation` ✅
- `planning` ✅
- `specification` ✅

**Current Sub-Phases:**
- `verification` ✅

---

## API Endpoint Mappings

### List Workflows

**Endpoint:** `GET /workflows?profile={profile}&phase={phase}`

**Query Parameters:**
- `profile` (optional): Profile name, defaults to "default"
- `phase` (optional): Filter by phase ("implementation", "planning", "specification")

**File System Action:**
- Without phase: List all `.md` files in `profiles/{profile}/workflows/*/` and `profiles/{profile}/workflows/*/*/`
- With phase: List all `.md` files in `profiles/{profile}/workflows/{phase}/` and subdirectories

**Response Example:**
```json
[
  {
    "name": "implement-task",
    "phase": "implementation",
    "sub_phase": null,
    "profile": "default",
    "path": "workflows/implementation/implement-task.md"
  },
  {
    "name": "verify-tasks",
    "phase": "implementation",
    "sub_phase": "verification",
    "profile": "default",
    "path": "workflows/implementation/verification/verify-tasks.md"
  },
  {
    "name": "write-spec",
    "phase": "specification",
    "sub_phase": null,
    "profile": "default",
    "path": "workflows/specification/write-spec.md"
  }
]
```

### Get Workflow Content

**Endpoint:** `GET /workflows/{workflowName}?profile={profile}&phase={phase}&sub_phase={sub_phase}`

**File System Mapping:**
```
URL: /workflows/implement-task?profile=default&phase=implementation
→ File: profiles/default/workflows/implementation/implement-task.md

URL: /workflows/verify-tasks?profile=default&phase=implementation&sub_phase=verification
→ File: profiles/default/workflows/implementation/verification/verify-tasks.md
```

**Response:**
```json
{
  "name": "implement-task",
  "phase": "implementation",
  "sub_phase": null,
  "profile": "default",
  "content": "# Implement Task Workflow\n\n..."
}
```

### Create/Update Workflow

**Endpoint:** `POST /workflows`

**Request Body:**
```json
{
  "name": "implement-task",
  "phase": "implementation",
  "sub_phase": null,
  "profile": "default",
  "content": "# Implement Task Workflow\n\n..."
}
```

**File System Action:**
- Write to: `profiles/{profile}/workflows/{phase}/{sub_phase}/{name}.md` (if sub_phase)
- Or: `profiles/{profile}/workflows/{phase}/{name}.md` (if no sub_phase)
- Create directories if missing
- Overwrite existing file

### Delete Workflow

**Endpoint:** `DELETE /workflows/{workflowName}?profile={profile}&phase={phase}&sub_phase={sub_phase}`

**File System Action:**
- Delete file at appropriate path
- Optionally remove empty directories

---

## Workflow Details by Phase

### Implementation Phase (11 workflows)

#### Top-Level Workflows (6 files)

| Workflow | Purpose | Used In |
|----------|---------|---------|
| `analyze-patterns.md` | Analyze codebase patterns before implementing | Implementation preparation |
| `document-implementation.md` | Document completed implementation | Post-implementation |
| `implement-task.md` | Core implementation workflow for a single task | Main implementation loop |
| `implementer-responsibilities.md` | Defines implementer role expectations | Agent/role definitions |
| `update-tasks-list.md` | Update tasks list after completing work | Post-implementation |
| `verifier-responsibilities.md` | Defines verifier role expectations | Agent/role definitions |

#### Verification Sub-Phase (5 files)

| Workflow | Purpose | Used In |
|----------|---------|---------|
| `create-verification-report.md` | Generate verification report | Verification process |
| `run-all-tests.md` | Execute complete test suite | Verification process |
| `update-roadmap.md` | Update roadmap after verification | Post-verification |
| `verify-documentation.md` | Verify documentation completeness | Verification process |
| `verify-tasks.md` | Verify task completion against requirements | Verification process |

### Planning Phase (4 workflows)

| Workflow | Purpose | Used In |
|----------|---------|---------|
| `create-product-mission.md` | Create product mission statement | `plan-product` command step 2 |
| `create-product-roadmap.md` | Create product roadmap | `plan-product` command step 3 |
| `create-product-tech-stack.md` | Define technology stack | `plan-product` command step 4 |
| `gather-product-info.md` | Gather product requirements | `plan-product` command step 1 |

### Specification Phase (5 workflows)

| Workflow | Purpose | Used In |
|----------|---------|---------|
| `create-tasks-list.md` | Create implementation tasks list | `create-spec` command step 2 |
| `initialize-spec.md` | Initialize new specification structure | `new-spec` command step 1 |
| `research-spec.md` | Research requirements for specification | `new-spec` command step 2 |
| `verify-spec.md` | Verify specification completeness | `create-spec` command step 3 |
| `write-spec.md` | Write specification document | `create-spec` command step 1 |

---

## Template Inclusion Patterns

### Inclusion Syntax

**In Command/Agent Files:**
```markdown
Follow this workflow:

{{workflows/{phase}/{workflow-name}}}
```

**Examples:**
```markdown
{{workflows/specification/write-spec}}
{{workflows/implementation/implement-task}}
{{workflows/planning/gather-product-info}}
{{workflows/implementation/verification/verify-tasks}}
```

### Expansion Behavior

**Process:**
1. **Parse Template:** Identify `{{workflows/{phase}/{workflow-name}}}` markers
2. **Resolve Path:** Construct file path `profiles/{profile}/workflows/{phase}/{workflow-name}.md`
3. **Read Content:** Load workflow file content
4. **Optional Header:** May inject header like `## [Workflow: {workflow-name}.md]`
5. **Replace Marker:** Substitute marker with workflow content

**Example Expansion:**

**Source (Command File):**
```markdown
# Create Specification

Follow this process:

{{workflows/specification/write-spec}}
```

**Expanded:**
```markdown
# Create Specification

Follow this process:

## [Workflow: write-spec.md]
# Write Specification Workflow

[Content of write-spec.md]
```

### Recursive Inclusion

**Workflows can include other workflows:**

**workflow-a.md:**
```markdown
Step 1: Do something

{{workflows/common/shared-step}}

Step 2: Continue
```

**Rules:**
- Maximum recursion depth: 10 levels (recommended)
- Cycle detection: Track included files, abort on circular reference
- Error on missing workflow: Return clear error message

---

## Workflow Content Structure

### Recommended Structure

While workflows are free-form Markdown, a recommended structure is:

```markdown
# {Workflow Name}

## Purpose
What this workflow accomplishes.

## Prerequisites
What must be in place before executing this workflow.

## Steps
1. Step one
2. Step two
3. Step three

## Expected Outputs
What artifacts or results this workflow produces.

## Success Criteria
How to know the workflow completed successfully.

## Common Issues
Known pitfalls and how to avoid them.
```

### Content Guidelines

**Do:**
- ✅ Use clear, step-by-step instructions
- ✅ Provide concrete examples
- ✅ Reference standards and best practices
- ✅ Define clear inputs and outputs
- ✅ Keep workflows focused on a single goal

**Don't:**
- ❌ Mix multiple unrelated workflows in one file
- ❌ Use vague or ambiguous language
- ❌ Assume context without explanation
- ❌ Include tool-specific commands (keep generic)
- ❌ Create circular workflow dependencies

---

## Usage in Commands

### Implementation Phase Workflows

**Used in `implement-spec` command:**
```markdown
# Implement Spec

Use the implementer workflows:

{{workflows/implementation/implementer-responsibilities}}

For each task:

{{workflows/implementation/implement-task}}

Then verify:

{{workflows/implementation/verification/verify-tasks}}
```

### Planning Phase Workflows

**Used in `plan-product` command:**
```markdown
# Plan Product

Step 1: {{workflows/planning/gather-product-info}}
Step 2: {{workflows/planning/create-product-mission}}
Step 3: {{workflows/planning/create-product-roadmap}}
Step 4: {{workflows/planning/create-product-tech-stack}}
```

### Specification Phase Workflows

**Used in `new-spec` command:**
```markdown
# New Spec

Initialize: {{workflows/specification/initialize-spec}}
Research: {{workflows/specification/research-spec}}
```

**Used in `create-spec` command:**
```markdown
# Create Spec

Write: {{workflows/specification/write-spec}}
Tasks: {{workflows/specification/create-tasks-list}}
Verify: {{workflows/specification/verify-spec}}
```

---

## Profile Inheritance

### Workflow Override Behavior

**Rule:** Child workflows override parent workflows by filename and path.

**Example:**
```
profiles/
├── base/
│   └── workflows/
│       └── specification/
│           ├── write-spec.md    # Parent version
│           └── verify-spec.md   # Parent version
└── custom/
    └── workflows/
        └── specification/
            └── write-spec.md    # Child version (overrides)
```

**Resolution for `{{workflows/specification/write-spec}}`:**
- Uses `custom/workflows/specification/write-spec.md` (child overrides parent)

**Resolution for `{{workflows/specification/verify-spec}}`:**
- Uses `base/workflows/specification/verify-spec.md` (parent, not overridden)

### Cross-Profile References

**Not Currently Supported:** Workflows cannot reference parent profile explicitly.

**Workaround:** Copy desired parent workflows to child profile.

**Future Enhancement:** Support syntax like `{{workflows/../../base/specification/write-spec}}`

---

## Validation and Quality Control

### Validation Checks

| Check | Description | Enforcement |
|-------|-------------|-------------|
| File extension | Must be `.md` | ✅ Required |
| Filename format | Lowercase, hyphen-separated | ⚠️ Recommended |
| Valid Markdown | Parseable Markdown syntax | ⚠️ Recommended |
| Phase exists | Workflow in valid phase directory | ✅ Required |
| No circular refs | No circular workflow inclusions | ✅ Required |
| Clear structure | Workflow has clear steps/purpose | ⚠️ Manual review |

### Completeness Checks

**For Workflows:**
- [ ] Clear purpose statement
- [ ] Defined prerequisites
- [ ] Step-by-step instructions
- [ ] Expected outputs documented
- [ ] Success criteria defined

---

## Performance Considerations

### Caching Strategy

**In-Memory Cache:**
- Cache workflow content by profile, phase, and filename
- Invalidate on file system changes
- Cache compiled template expansions with workflows

**Cache Key Example:**
```
Key: workflow:{profile}:{phase}:{sub_phase}:{filename}
Value: File content (Markdown)
TTL: Until file modified
```

### Optimization

**For Template Inclusion:**
- Pre-compile workflow inclusions during installation
- Store expanded results in index file
- Lazy-load individual workflows on demand

**For API Requests:**
- Return metadata (list) quickly
- Stream large workflow content
- Use ETags for client-side caching

---

## Error Handling

### Missing Workflows

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| Phase doesn't exist | 404 | `{"error": "Phase not found"}` | Return error |
| Workflow doesn't exist | 404 | `{"error": "Workflow not found"}` | Return error |
| Profile doesn't exist | 404 | `{"error": "Profile not found"}` | Return error |
| Empty phase | 200 | `[]` | Return empty array |

### Template Expansion Errors

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| Workflow not found | 500 | `{"error": "Workflow template not found"}` | Abort expansion |
| Circular reference | 500 | `{"error": "Circular workflow dependency"}` | Abort expansion |
| Recursion limit | 500 | `{"error": "Max recursion depth exceeded"}` | Abort expansion |
| Parse error | 500 | `{"error": "Workflow parse error"}` | Return error with details |

---

## Claude Code Integration

### Workflow Usage Pattern

**Workflows are never used directly by users.** Instead:

1. **In Multi-Agent Mode:**
   - Workflows expanded into agent definition at installation
   - Agent receives workflows as part of its prompt
   - No runtime workflow lookup

2. **In Single-Agent Mode:**
   - Workflows expanded into command steps at installation
   - Each step includes relevant workflows inline
   - User/agent has workflow context for each step

### Dynamic Workflow Updates

**Current Limitation:** Workflows are static after installation.

**Future Enhancement:**
- API-driven workflow retrieval
- Hot-reload workflows during command execution
- Version-controlled workflows with updates

---

## Testing Recommendations

### Unit Tests
- [ ] Workflow file discovery (top-level)
- [ ] Workflow file discovery (sub-phases)
- [ ] Template inclusion basic
- [ ] Template inclusion recursive
- [ ] Circular dependency detection
- [ ] Recursion limit enforcement
- [ ] Profile inheritance override

### Integration Tests
- [ ] API `/workflows` listing
- [ ] API `/workflows/{name}` retrieval
- [ ] Workflow creation via POST
- [ ] Workflow deletion via DELETE
- [ ] Template expansion in commands
- [ ] Template expansion in agents
- [ ] Multi-level sub-phase navigation

### Edge Case Tests
- [ ] Empty workflow file
- [ ] Very large workflow file (>1MB)
- [ ] Workflow with special characters
- [ ] Workflow with code blocks
- [ ] Deeply nested sub-phases (3+ levels)
- [ ] Circular workflow includes
- [ ] Missing workflow in template

---

## Related Documentation

- [API Specification](../api-spec_Version2.md) - API endpoints for workflows
- [Claude Code Integration Policy](../CLAUDE_CODE_INTEGRATION_POLICY_Version2.md) - Integration patterns
- [Profiles Analysis](./profiles-analysis.md) - Profile structure and inheritance
- [Commands Analysis](./commands-analysis.md) - How workflows are used in commands
- [Standards Analysis](./standards-analysis.md) - Standards vs workflows distinction

---

## Workflows vs Standards

### Key Differences

| Aspect | Workflows | Standards |
|--------|-----------|-----------|
| **Purpose** | Procedural: "How to do X" | Declarative: "What good X looks like" |
| **Content** | Step-by-step instructions | Rules, patterns, examples |
| **Organization** | By phase (planning, implementation, etc.) | By category (backend, frontend, etc.) |
| **Usage** | Included in commands | Included in agents and commands |
| **Audience** | Agent/user executing a process | Agent writing/reviewing code |

### Example Comparison

**Workflow (Implementation):**
```markdown
# Implement Task

1. Read the task description
2. Identify affected files
3. Make necessary changes
4. Run tests
5. Update documentation
6. Mark task complete
```

**Standard (Coding Style):**
```markdown
# Coding Style

- Use 2 spaces for indentation
- Maximum line length: 100 characters
- Use descriptive variable names
- Add comments for complex logic
```

---

## Recommendations

### Immediate Actions
1. Add validation for workflow filenames
2. Create workflow authoring guide
3. Implement circular dependency detector
4. Document workflow structure template

### Short-Term Improvements
1. Add workflow metadata (version, author, tags)
2. Implement workflow versioning
3. Create workflow testing framework
4. Add workflow quality metrics (clarity, completeness)
5. Support deeper sub-phase nesting (3+ levels)

### Long-Term Vision
1. Workflow marketplace/registry
2. Dynamic workflow updates via API
3. AI-assisted workflow generation
4. Workflow composition (chain multiple workflows)
5. Workflow analytics (usage tracking)
6. Interactive workflow builder UI
7. Workflow branching (conditional steps)

---

**Last Updated:** 2025-10-14  
**Analysis Version:** 2.0  
**Agent OS Version Analyzed:** 2.0.3  
**Source Repository:** https://github.com/buildermethods/agent-os
