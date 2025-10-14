# Agent OS: Profiles Structure and Conventions Analysis

## Executive Summary

This document provides a comprehensive analysis of the Agent OS profile system, documenting all file structures, naming conventions, and organizational patterns. The profile system is the core mechanism for organizing standards, commands, workflows, agents, and roles in Agent OS.

**Key Findings:**
- Profile system uses directory-based organization with predictable structure
- Default profile contains 60 files across 5 major subdirectories
- File naming follows strict conventions for API discoverability
- Profile inheritance supported through configuration (not yet fully implemented)

---

## Profile Directory Structure

### Top-Level Organization

```
profiles/
└── default/
    ├── agents/           # Agent definitions for multi-agent mode
    ├── commands/         # User-facing commands (dual-mode)
    ├── roles/            # Role definitions (YAML files)
    ├── standards/        # Coding standards and best practices
    └── workflows/        # Workflow documentation and processes
```

### Complete File Tree

```
profiles/default/
├── agents/
│   ├── implementation-verifier.md
│   ├── product-planner.md
│   ├── specification/
│   │   ├── spec-initializer.md
│   │   ├── spec-researcher.md
│   │   ├── spec-verifier.md
│   │   ├── spec-writer.md
│   │   └── tasks-list-creator.md
│   └── templates/
│       ├── implementer.md
│       └── verifier.md
├── commands/
│   ├── create-spec/
│   │   ├── multi-agent/
│   │   │   └── create-spec.md
│   │   └── single-agent/
│   │       ├── 1-create-spec.md
│   │       ├── 2-create-tasks-list.md
│   │       └── 3-verify-spec.md
│   ├── implement-spec/
│   │   ├── multi-agent/
│   │   │   └── implement-spec.md
│   │   └── single-agent/
│   │       └── implement-spec.md
│   ├── new-spec/
│   │   ├── multi-agent/
│   │   │   └── new-spec.md
│   │   └── single-agent/
│   │       ├── 1-new-spec.md
│   │       └── 2-research-spec.md
│   └── plan-product/
│       ├── multi-agent/
│       │   └── plan-product.md
│       └── single-agent/
│           ├── 1-plan-product.md
│           ├── 2-create-mission.md
│           ├── 3-create-roadmap.md
│           └── 4-create-tech-stack.md
├── roles/
│   ├── implementers.yml
│   └── verifiers.yml
├── standards/
│   ├── backend/
│   │   ├── api.md
│   │   ├── migrations.md
│   │   ├── models.md
│   │   └── queries.md
│   ├── frontend/
│   │   ├── accessibility.md
│   │   ├── components.md
│   │   ├── css.md
│   │   └── responsive.md
│   ├── global/
│   │   ├── coding-style.md
│   │   ├── commenting.md
│   │   ├── conventions.md
│   │   ├── error-handling.md
│   │   ├── tech-stack.md
│   │   └── validation.md
│   └── testing/
│       └── test-writing.md
└── workflows/
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

## File Count Summary

| Directory | Files | Subdirectories | Purpose |
|-----------|-------|----------------|---------|
| `agents/` | 9 | 2 | Agent definitions for multi-agent orchestration |
| `commands/` | 12 | 8 | User-facing commands in dual modes |
| `roles/` | 2 | 0 | Role definitions in YAML format |
| `standards/` | 15 | 4 | Coding standards organized by category |
| `workflows/` | 22 | 4 | Workflow documentation by phase |
| **Total** | **60** | **18** | Complete default profile |

---

## Naming Conventions

### Profile Names
- **Pattern:** `{profile-name}/`
- **Current:** `default`
- **Convention:** Lowercase, hyphen-separated
- **Location:** `profiles/{profile-name}/`

### Agent Files
- **Location:** `profiles/{profile}/agents/`
- **Pattern:** `{agent-name}.md` or `{category}/{agent-name}.md`
- **Examples:**
  - `implementation-verifier.md`
  - `product-planner.md`
  - `specification/spec-writer.md`
  - `templates/implementer.md`

### Command Files

#### Multi-Agent Mode
- **Location:** `profiles/{profile}/commands/{command-name}/multi-agent/`
- **Pattern:** `{command-name}.md`
- **Convention:** Single file per command
- **Examples:**
  - `create-spec/multi-agent/create-spec.md`
  - `implement-spec/multi-agent/implement-spec.md`

#### Single-Agent Mode
- **Location:** `profiles/{profile}/commands/{command-name}/single-agent/`
- **Pattern:** `{step-number}-{step-name}.md`
- **Regex:** `/^\d+-.*\.md$/`
- **Numbering:** Sequential integers (1, 2, 3, ...) or gaps allowed (1, 3, 5, ...)
- **Examples:**
  - `1-create-spec.md`
  - `2-create-tasks-list.md`
  - `3-verify-spec.md`
  - `4-create-tech-stack.md`

**Important Convention Notes:**
- Only files matching the regex pattern are treated as steps
- Steps are sorted numerically by step number
- Non-numbered files are ignored during step discovery
- Step number gaps are handled gracefully

### Role Files
- **Location:** `profiles/{profile}/roles/`
- **Pattern:** `{role-category}.yml`
- **Format:** YAML with structured sections
- **Examples:**
  - `implementers.yml`
  - `verifiers.yml`

### Standards Files
- **Location:** `profiles/{profile}/standards/{category}/`
- **Pattern:** `{standard-name}.md`
- **Categories:** `backend/`, `frontend/`, `global/`, `testing/`
- **Examples:**
  - `backend/api.md`
  - `frontend/components.md`
  - `global/coding-style.md`
  - `testing/test-writing.md`

### Workflow Files
- **Location:** `profiles/{profile}/workflows/{phase}/`
- **Pattern:** `{workflow-name}.md`
- **Phases:** `implementation/`, `planning/`, `specification/`
- **Sub-phases:** `implementation/verification/`
- **Examples:**
  - `specification/write-spec.md`
  - `implementation/implement-task.md`
  - `planning/create-product-roadmap.md`

---

## API Endpoint Mappings

### Profile Operations

| HTTP Method | Endpoint | File System Action | Example |
|-------------|----------|-------------------|---------|
| GET | `/profiles` | List directory names in `profiles/` | `["default"]` |
| POST | `/profiles` | Create directory `profiles/{name}/` | Create profile structure |
| GET | `/profiles/{name}` | Read directory structure | Return file tree |
| DELETE | `/profiles/{name}` | Remove directory `profiles/{name}/` | Delete all files |

### Role Operations

| HTTP Method | Endpoint | File System Action | Example |
|-------------|----------|-------------------|---------|
| GET | `/profiles/{profile}/roles` | List `*.yml` files in `roles/` | `["implementers.yml", "verifiers.yml"]` |
| POST | `/profiles/{profile}/roles` | Write YAML file | Create/update role |
| GET | `/profiles/{profile}/roles/{roleId}` | Read specific YAML file | Return role data |
| DELETE | `/profiles/{profile}/roles/{roleId}` | Delete YAML file | Remove role |

### Standards Operations

| HTTP Method | Endpoint | File System Action | Example |
|-------------|----------|-------------------|---------|
| GET | `/standards?profile={p}&category={c}` | List `standards/{category}/*.md` | Return metadata list |
| GET | `/standards/{category}/{name}?profile={p}` | Read specific `.md` file | Return Markdown content |
| POST | `/standards` | Write `.md` file | Create/update standard |
| DELETE | `/standards/{category}/{name}?profile={p}` | Delete `.md` file | Remove standard |

### Command Operations

| HTTP Method | Endpoint | File System Action | Example |
|-------------|----------|-------------------|---------|
| GET | `/commands?profile={p}&mode={m}` | List command directories | Return command metadata |
| GET | `/commands/{name}?mode=multi-agent&profile={p}` | Read single `.md` file | Return multi-agent prompt |
| GET | `/commands/{name}?mode=single-agent&profile={p}` | Read all `\d+-*.md` files | Return array of steps |
| POST | `/commands` | Write `.md` file(s) | Create/update command |
| DELETE | `/commands/{name}?mode={m}&profile={p}` | Delete file(s) | Remove command |

---

## Profile Inheritance

### Configuration
Profiles can inherit from parent profiles (specified in profile config or global config).

### Inheritance Rules
1. **Complete Override:** Child profile command fully replaces parent command
2. **No Per-Step Fallback:** All steps must come from either child OR parent
3. **Standards Inheritance:** Child can reference parent standards via glob patterns
4. **Role Inheritance:** Roles can inherit from parent with `model: inherit`

### Example Scenarios

**Scenario 1: Command Override**
```
profiles/
├── base/
│   └── commands/create-spec/single-agent/
│       ├── 1-create-spec.md
│       └── 2-verify-spec.md
└── custom/
    └── commands/create-spec/single-agent/
        └── 1-create-spec.md  # Only step 1 present
```
**Result:** Custom profile uses only its `1-create-spec.md`, parent's step 2 is NOT used.

**Scenario 2: Standards Reference**
```yaml
# custom profile role
standards:
  - global/*        # References custom/standards/global/* if exists
  - ../base/global/* # Can reference parent explicitly
```

---

## Template Expansion

### Standards Inclusion Syntax
- **Pattern:** `{{standards/{category}/*}}`
- **Examples:**
  - `{{standards/global/*}}` - All global standards
  - `{{standards/backend/*}}` - All backend standards
  - `{{standards/*}}` - All standards (all categories)

### Workflow Inclusion Syntax
- **Pattern:** `{{workflows/{phase}/{workflow-name}}}`
- **Example:** `{{workflows/specification/write-spec}}`

### Expansion Behavior
1. **File Discovery:** Glob patterns resolve to matching file paths
2. **Ordering:** Files expanded in lexicographical (alphabetical) order
3. **Headers:** Each included file preceded by header (e.g., `## [Standard: coding-style.md]`)
4. **Recursion:** Templates can include other templates
5. **Recursion Limit:** Max depth of 10 to prevent infinite loops
6. **Cycle Detection:** Tracks included files; aborts on circular reference

### Expansion Example

**Source Template:**
```markdown
Follow these standards:

{{standards/global/*}}
```

**Expanded Result:**
```markdown
Follow these standards:

## [Standard: coding-style.md]
[Content of coding-style.md]

## [Standard: commenting.md]
[Content of commenting.md]

## [Standard: conventions.md]
[Content of conventions.md]
```

---

## Installation and Compilation

### Installation Process
During project installation, profile files are:
1. **Copied** to project directory (`<project>/agent-os/`)
2. **Compiled** for multi-agent mode (if enabled):
   - Commands → `<project>/.claude/commands/agent-os/{command}/`
   - Agents → `<project>/.claude/agents/agent-os/{agent}/`
   - Templates expanded with standards and workflow references
3. **Linked** for single-agent mode (if enabled):
   - Roles → `<project>/agent-os/roles/`
   - Commands → `<project>/agent-os/commands/{command}/single-agent/`

### Path Resolution
- Base installation: `~/agent-os/profiles/{profile}/`
- Project installation: `<project>/agent-os/profiles/{profile}/`
- Multi-agent compiled: `<project>/.claude/{agents|commands}/agent-os/`

---

## Notable Patterns and Observations

### Consistency
✅ All standards follow consistent `{name}.md` pattern  
✅ All commands have both multi-agent and single-agent modes  
✅ Workflows organized by logical phases  
✅ Agent definitions follow predictable structure

### Potential Issues
⚠️ No profile versioning mechanism  
⚠️ Profile inheritance not fully implemented  
⚠️ No validation of file naming conventions  
⚠️ Standards can have duplicate or conflicting content  

### Extensibility Points
- Custom profiles can be added under `profiles/{custom-name}/`
- Standards categories are extensible (add new category directories)
- Workflow phases can be added as new subdirectories
- Agent templates support dynamic role-based compilation

---

## Validation and Error Handling

### Expected Validations
- [ ] Profile directory exists
- [ ] Required subdirectories present (`agents/`, `commands/`, `roles/`, `standards/`, `workflows/`)
- [ ] Command files follow naming conventions
- [ ] Single-agent steps are numbered sequentially (or with allowed gaps)
- [ ] Role YAML files are valid
- [ ] Standards files are valid Markdown
- [ ] No circular template includes

### Error Scenarios
- **Missing Steps:** Skipped with warning; non-matching files ignored
- **Parse Errors:** Returned with file and context information
- **Recursion Errors:** Abort with cycle detection message
- **Missing Templates:** Error with clear file path reference

---

## Claude Code Integration

### Sequential Execution
- Single-agent commands presented step-by-step
- User/agent completes one step before proceeding to next
- No batch execution (prevents premature completion)

### File Discovery
- Only files matching `/^\d+-.*\.md$/` are steps
- Non-step files ignored (e.g., `README.md`, `notes.txt`)
- Steps sorted numerically, not alphabetically

### Multi-Agent Orchestration
- Single file returned as complete prompt
- Agent orchestrates subagents based on prompt content
- No step-by-step presentation needed

---

## Performance Considerations

### Caching Strategy
- Standards and templates should be cached in memory
- Profile directory structure cached during installation
- Template expansion results cached per project

### Concurrency
- Multiple commands can run concurrently
- File operations must be atomic and async
- Lock files to prevent concurrent writes

---

## Related Documentation

- [API Specification](../api-spec_Version2.md) - API endpoint details
- [Claude Code Integration Policy](../CLAUDE_CODE_INTEGRATION_POLICY_Version2.md) - Tool integration rules
- [Commands Analysis](./commands-analysis.md) - Command-specific details
- [Standards Analysis](./standards-analysis.md) - Standards organization
- [Roles Analysis](./roles-analysis.md) - Role YAML structure

---

## Recommendations

### Immediate Actions
1. Implement profile version tracking in config
2. Add validation script for profile structure
3. Document profile inheritance explicitly
4. Create profile creation template/wizard

### Short-Term Improvements
1. Add profile metadata file (version, author, description)
2. Implement profile inheritance fully
3. Add standards conflict detection
4. Create profile testing framework

### Long-Term Vision
1. Profile marketplace/registry
2. Dynamic profile loading via API
3. Profile composition (mix multiple profiles)
4. Profile analytics and usage tracking

---

**Last Updated:** 2025-10-14  
**Analysis Version:** 2.0  
**Agent OS Version Analyzed:** 2.0.3  
**Source Repository:** https://github.com/buildermethods/agent-os
