# Agent OS: Complete API-to-Filesystem Convention Mapping

## Executive Summary

This document serves as the canonical reference for all file system conventions and API endpoint mappings in Agent OS. It consolidates all naming patterns, directory structures, glob expansions, template recursions, and integration rules documented throughout the analysis suite.

**Purpose:** Enable both human developers and AI agents (like Claude Code) to understand and work with Agent OS's file-based architecture through precise, unambiguous conventions.

---

## Table of Contents

1. [File System Conventions](#file-system-conventions)
2. [API Endpoint Mappings](#api-endpoint-mappings)
3. [Naming Patterns and Regex](#naming-patterns-and-regex)
4. [Glob Pattern Expansion](#glob-pattern-expansion)
5. [Template Recursion Rules](#template-recursion-rules)
6. [Profile Inheritance](#profile-inheritance)
7. [Claude Code Integration](#claude-code-integration)
8. [Error Handling](#error-handling)
9. [Performance Guidelines](#performance-guidelines)

---

## File System Conventions

### Directory Structure Template

```
profiles/{profile}/
├── agents/                          # Agent definitions
│   ├── {agent-name}.md
│   ├── {category}/
│   │   └── {agent-name}.md
│   └── templates/
│       └── {template-name}.md
├── commands/                        # User-facing commands
│   └── {command-name}/
│       ├── multi-agent/
│       │   └── {command-name}.md
│       └── single-agent/
│           └── {step-number}-{step-name}.md
├── roles/                           # Role definitions
│   └── {role-category}.yml
├── standards/                       # Coding standards
│   └── {category}/
│       └── {standard-name}.md
└── workflows/                       # Workflow documentation
    └── {phase}/
        ├── {workflow-name}.md
        └── {sub-phase}/
            └── {workflow-name}.md
```

### Naming Convention Matrix

| Component | Pattern | Regex | Examples |
|-----------|---------|-------|----------|
| Profile name | `{name}` | `^[a-z0-9-]+$` | `default`, `custom`, `my-profile` |
| Agent file | `{name}.md` | `^[a-z0-9-]+\.md$` | `spec-writer.md`, `database-engineer.md` |
| Command name | `{name}` | `^[a-z0-9-]+$` | `create-spec`, `implement-spec` |
| Single-agent step | `{number}-{name}.md` | `^\d+-[a-z0-9-]+\.md$` | `1-create-spec.md`, `10-verify.md` |
| Role file | `{category}.yml` | `^[a-z0-9-]+\.ya?ml$` | `implementers.yml`, `verifiers.yaml` |
| Standard file | `{name}.md` | `^[a-z0-9-]+\.md$` | `api.md`, `coding-style.md` |
| Workflow file | `{name}.md` | `^[a-z0-9-]+\.md$` | `implement-task.md`, `write-spec.md` |

---

## API Endpoint Mappings

### Complete Endpoint Reference

#### Profiles

| Method | Endpoint | File System Action | Response |
|--------|----------|-------------------|----------|
| GET | `/profiles` | List dirs in `profiles/` | Array of profile names |
| POST | `/profiles` | Create `profiles/{name}/` | Created profile metadata |
| GET | `/profiles/{name}` | Read dir structure | Profile details and file tree |
| DELETE | `/profiles/{name}` | Remove `profiles/{name}/` | 204 No Content |

#### Roles

| Method | Endpoint | File System Action | Response |
|--------|----------|-------------------|----------|
| GET | `/profiles/{profile}/roles` | List `*.yml` in `roles/` | Array of role metadata |
| POST | `/profiles/{profile}/roles` | Write `roles/{file}` | Created role details |
| GET | `/profiles/{profile}/roles/{id}` | Parse YAML, find by ID | Single role object |
| DELETE | `/profiles/{profile}/roles/{id}` | Remove from YAML file | 204 No Content |

#### Standards

| Method | Endpoint | File System Action | Response |
|--------|----------|-------------------|----------|
| GET | `/standards?profile={p}&category={c}` | List `standards/{c}/*.md` | Array of standard metadata |
| GET | `/standards/{category}/{name}?profile={p}` | Read `standards/{c}/{name}.md` | Markdown content |
| POST | `/standards` | Write `standards/{c}/{name}.md` | Created standard metadata |
| DELETE | `/standards/{c}/{name}?profile={p}` | Delete `standards/{c}/{name}.md` | 204 No Content |

#### Commands

| Method | Endpoint | File System Action | Response |
|--------|----------|-------------------|----------|
| GET | `/commands?profile={p}&mode={m}` | List command dirs | Array of command metadata |
| GET | `/commands/{name}?mode=multi-agent&profile={p}` | Read `commands/{name}/multi-agent/{name}.md` | Single Markdown document |
| GET | `/commands/{name}?mode=single-agent&profile={p}` | Read all `commands/{name}/single-agent/\d+-*.md` | Array of step objects |
| POST | `/commands` | Write command file(s) | Created command metadata |
| DELETE | `/commands/{name}?mode={m}&profile={p}` | Delete command file(s) | 204 No Content |

#### Workflows

| Method | Endpoint | File System Action | Response |
|--------|----------|-------------------|----------|
| GET | `/workflows?profile={p}&phase={ph}` | List `workflows/{ph}/**/*.md` | Array of workflow metadata |
| GET | `/workflows/{name}?profile={p}&phase={ph}&sub_phase={sp}` | Read `workflows/{ph}/{sp?}/{name}.md` | Markdown content |
| POST | `/workflows` | Write `workflows/{ph}/{sp?}/{name}.md` | Created workflow metadata |
| DELETE | `/workflows/{name}?profile={p}&phase={ph}` | Delete workflow file | 204 No Content |

#### Agents

| Method | Endpoint | File System Action | Response |
|--------|----------|-------------------|----------|
| GET | `/agents?profile={p}` | List `agents/**/*.md` (exclude templates) | Array of agent metadata |
| GET | `/agents/{name}?profile={p}` | Read `agents/**/{name}.md` | Agent definition with metadata |
| POST | `/agents/compile` | Compile agent from role + template | Compiled agent content |

---

## Naming Patterns and Regex

### Single-Agent Step Discovery

**Critical Convention:** Only files matching this pattern are treated as steps.

**Pattern:** `{step-number}-{step-name}.md`

**Regex:** `/^\d+-.*\.md$/`

**Validation Rules:**
1. **Must start with digits:** `\d+` (one or more digits)
2. **Must have hyphen separator:** `-`
3. **Must end with `.md`:** Extension required
4. **Step name:** Any characters after hyphen (typically lowercase, hyphenated)

**Valid Examples:**
```
1-create-spec.md          ✅ Standard
2-create-tasks-list.md    ✅ Standard
10-advanced-step.md       ✅ Large step number
001-init.md               ✅ Leading zeros (works but not recommended)
```

**Invalid Examples (Ignored):**
```
create-spec.md            ❌ No step number
step-1-create-spec.md     ❌ Doesn't start with digit
1_create_spec.md          ❌ Underscore instead of hyphen
1-create-spec.txt         ❌ Wrong extension
README.md                 ❌ No step number
notes.txt                 ❌ Not matching pattern
```

### Step Numbering Rules

| Pattern | Status | Behavior | Example |
|---------|--------|----------|---------|
| Sequential (1, 2, 3) | ✅ Recommended | Natural ordering | `1-a.md, 2-b.md, 3-c.md` |
| Gaps (1, 3, 5) | ✅ Allowed | Sorted numerically | `1-a.md, 3-c.md` (no 2) |
| Out of order files | ✅ Handled | Sorted numerically | Files `3, 1, 2` → Steps `1, 2, 3` |
| Leading zeros (01, 02) | ⚠️ Works | Sorted numerically | `01-a.md, 02-b.md` |
| Duplicate numbers | ❌ Undefined | Avoid | `1-a.md, 1-b.md` |

**Sorting Algorithm:**
```python
def sort_steps(files):
    # Extract step number from filename
    steps = []
    for file in files:
        match = re.match(r'^(\d+)-', file.name)
        if match:
            step_number = int(match.group(1))
            steps.append((step_number, file))
    
    # Sort by step number (numeric, not alphabetic)
    steps.sort(key=lambda x: x[0])
    
    return [step[1] for step in steps]
```

---

## Glob Pattern Expansion

### Supported Patterns

| Pattern | Matches | Order | Example Result |
|---------|---------|-------|----------------|
| `standards/global/*` | All files in global/ | Alphabetical | `coding-style.md, commenting.md, ...` |
| `standards/backend/*` | All files in backend/ | Alphabetical | `api.md, migrations.md, ...` |
| `standards/*` | All files in all categories | Category, then file | `backend/api.md, ... global/coding-style.md, ...` |
| `workflows/specification/*` | All workflows in phase | Alphabetical | `create-tasks-list.md, initialize-spec.md, ...` |

### Single-Level Glob Behavior

**Pattern:** `{category}/*`

**Rules:**
- Matches all `.md` files in the specified directory
- Does NOT recurse into subdirectories
- Returns files in lexicographical (alphabetical) order
- Case-sensitive on Linux, case-insensitive on macOS/Windows

**Expansion Example:**
```
Pattern: standards/global/*

Directory contents:
- coding-style.md
- conventions.md
- commenting.md
- error-handling.md

Expanded (alphabetical):
1. coding-style.md
2. commenting.md
3. conventions.md
4. error-handling.md
```

### Multi-Category Wildcard

**Pattern:** `standards/*`

**Expansion Order:**
1. Sort categories alphabetically
2. Within each category, sort files alphabetically
3. Concatenate in order

**Example:**
```
Pattern: standards/*

Categories: backend, frontend, global, testing

Result:
1. backend/api.md
2. backend/migrations.md
3. backend/models.md
4. backend/queries.md
5. frontend/accessibility.md
6. frontend/components.md
7. frontend/css.md
8. frontend/responsive.md
9. global/coding-style.md
10. global/commenting.md
... (all files)
```

### Recursive Glob (Future)

**Pattern:** `**/*` (if implemented)

**Status:** ❌ Not currently supported

**Behavior (if added):**
- Would match all files recursively
- Depth-first or breadth-first traversal
- Requires explicit recursion limit

---

## Template Recursion Rules

### Inclusion Syntax

**Standards:**
```markdown
{{standards/{category}/*}}
{{standards/*}}
```

**Workflows:**
```markdown
{{workflows/{phase}/{workflow-name}}}
```

**Variables (in agent/role compilation):**
```markdown
{{id}}
{{description}}
{{your_role}}
{{areas_of_responsibility}}
{{standards}}
```

### Expansion Process

1. **Parse Template:** Identify all `{{...}}` markers
2. **Resolve Paths:** Convert patterns to file paths
3. **Read Content:** Load each file
4. **Inject Headers:** Prepend context headers
5. **Concatenate:** Combine content in order
6. **Replace Markers:** Substitute markers with content
7. **Recurse:** Process included content for nested markers
8. **Check Depth:** Enforce recursion limit

### Recursion Limits

| Limit | Value | Enforcement | Behavior on Exceed |
|-------|-------|-------------|-------------------|
| Max Depth | 10 | ✅ Required | Abort with error |
| Max Includes | 100 | ⚠️ Recommended | Warning or abort |
| Max Content Size | 10 MB | ⚠️ Recommended | Warning or abort |

### Cycle Detection

**Algorithm:**
```python
def expand_template(content, included_files=None):
    if included_files is None:
        included_files = set()
    
    markers = find_all_markers(content)
    
    for marker in markers:
        files = resolve_glob(marker)
        
        for file in files:
            # Check for circular reference
            if file in included_files:
                raise CircularIncludeError(f"Circular include: {file}")
            
            # Track this file
            new_included = included_files.copy()
            new_included.add(file)
            
            # Read and expand recursively
            file_content = read_file(file)
            expanded = expand_template(file_content, new_included)
            
            # Replace marker
            content = content.replace(marker, expanded)
    
    return content
```

### Header Injection

**Rule:** Each included file is preceded by a header indicating the source.

**Header Format:**
- Standards: `## [Standard: {filename}]`
- Workflows: `## [Workflow: {filename}]`

**Example:**
```markdown
Apply these standards:

{{standards/global/*}}
```

**Expands to:**
```markdown
Apply these standards:

## [Standard: coding-style.md]
# Coding Style
[content]

## [Standard: commenting.md]
# Commenting Standards
[content]
```

---

## Profile Inheritance

### Inheritance Rules

**Override Behavior:**

| Component | Override Rule | Fallback Behavior |
|-----------|---------------|-------------------|
| Commands | Complete override | No per-step fallback |
| Standards | File-level override | Parent files used if not overridden |
| Workflows | File-level override | Parent files used if not overridden |
| Roles | Role ID override | Parent roles used if not overridden |
| Agents | File-level override | Parent agents used if not overridden |

### Command Override Example

**Scenario:**
```
profiles/
├── base/
│   └── commands/create-spec/single-agent/
│       ├── 1-create-spec.md
│       ├── 2-create-tasks-list.md
│       └── 3-verify-spec.md
└── custom/  (inherits from base)
    └── commands/create-spec/single-agent/
        └── 1-create-spec.md  # Only step 1 present
```

**Resolution:**
- Custom profile has `create-spec` command
- Result: ONLY custom's step 1 is used
- Parent's steps 2 and 3 are NOT used (no fallback)

**Rationale:** Prevents mixed/inconsistent workflows from different profiles.

### Standards Override Example

**Scenario:**
```
profiles/
├── base/
│   └── standards/global/
│       ├── coding-style.md
│       └── commenting.md
└── custom/  (inherits from base)
    └── standards/global/
        └── coding-style.md  # Overrides parent
```

**Resolution for `{{standards/global/*}}`:**
1. `custom/standards/global/coding-style.md` (from child)
2. `base/standards/global/commenting.md` (from parent)

---

## Claude Code Integration

### Sequential Step Execution

**Rule:** Single-agent commands executed step-by-step, never batch.

**Process:**
1. API returns ordered array of steps
2. Claude Code presents step 1 to agent
3. Agent completes step 1
4. Claude Code waits for confirmation
5. Claude Code presents step 2
6. Repeat until all steps complete

**Rationale:** Ensures quality and allows for course correction.

### Multi-Agent Orchestration

**Process:**
1. API returns single Markdown file (multi-agent command)
2. Claude Code presents entire prompt to main agent
3. Main agent orchestrates subagents autonomously
4. Process runs until completion

### File Discovery

**Rule:** Claude Code NEVER parses file system directly.

**Process:**
1. Claude Code calls API endpoint
2. API performs file discovery and step sorting
3. API returns structured data
4. Claude Code uses API response

---

## Error Handling

### HTTP Status Codes

| Status | Scenario | Example |
|--------|----------|---------|
| 200 OK | Success | Command steps retrieved |
| 201 Created | Resource created | New standard created |
| 204 No Content | Delete success | Standard deleted |
| 400 Bad Request | Invalid input | Invalid step filename |
| 404 Not Found | Resource not found | Profile doesn't exist |
| 409 Conflict | Conflict | Duplicate role ID |
| 500 Internal Server Error | Server error | Template expansion failed |

### Error Response Format

```json
{
  "error": "Description of error",
  "code": 400,
  "details": {
    "field": "filename",
    "value": "invalid-name",
    "expected": "Must match /^\\d+-.*\\.md$/"
  }
}
```

### Common Error Scenarios

| Error | HTTP | Response | Resolution |
|-------|------|----------|------------|
| Command not found | 404 | `{"error": "Command not found"}` | Check command name |
| Invalid step name | 400 | `{"error": "Invalid filename"}` | Use correct pattern |
| Circular include | 500 | `{"error": "Circular dependency"}` | Remove circular ref |
| Missing profile | 404 | `{"error": "Profile not found"}` | Create profile |
| Recursion limit | 500 | `{"error": "Max depth exceeded"}` | Reduce nesting |

---

## Performance Guidelines

### Caching Strategy

**Cache Targets:**
- Profile metadata
- Role definitions
- Standards content
- Workflow content
- Compiled templates
- Step discovery results

**Cache Invalidation:**
- On file system changes (file modified, added, deleted)
- On profile switch
- On explicit cache clear

**Cache Keys:**
```
profile:{profile_name}:metadata
role:{profile}:{role_id}
standard:{profile}:{category}:{name}
workflow:{profile}:{phase}:{name}
command:{profile}:{name}:{mode}:steps
template:{profile}:{template}:compiled
```

### Optimization Rules

1. **Lazy Load:** Load files on-demand, not preemptively
2. **Parallel Reads:** Read multiple files concurrently (async I/O)
3. **In-Memory Cache:** Keep frequently accessed content in memory
4. **Pre-Compile:** Expand templates during installation, not runtime
5. **Batch Operations:** Group related file operations
6. **Index Files:** Use index files for fast metadata lookup

### Performance Targets

| Operation | Target | Acceptable | Critical |
|-----------|--------|------------|----------|
| List commands | <100ms | <500ms | <1s |
| Get single command | <200ms | <500ms | <1s |
| Expand standards | <500ms | <1s | <2s |
| Compile agent | <1s | <3s | <5s |
| Full installation | <10s | <30s | <60s |

---

## Validation Rules

### File Naming Validation

| Component | Validation | Regex | Error on Fail |
|-----------|------------|-------|---------------|
| Profile name | Lowercase, hyphen-separated | `^[a-z0-9-]+$` | ✅ Yes |
| Single-agent step | Number-hyphen-name | `^\d+-[a-z0-9-]+\.md$` | ✅ Yes |
| Standard file | Lowercase, hyphen, .md | `^[a-z0-9-]+\.md$` | ⚠️ Warn |
| Role ID | Lowercase, hyphen | `^[a-z0-9-]+$` | ✅ Yes |

### Content Validation

| Component | Check | Enforcement |
|-----------|-------|-------------|
| YAML files | Valid YAML syntax | ✅ Required |
| Markdown files | Valid Markdown | ⚠️ Recommended |
| Frontmatter | Required fields present | ✅ Required |
| Glob patterns | Valid pattern syntax | ✅ Required |
| File references | Referenced files exist | ⚠️ Warn |

---

## Reference Tables

### Complete Pattern Reference

| Pattern | Example | Matches | Used In |
|---------|---------|---------|---------|
| `^\d+-.*\.md$` | `1-create-spec.md` | Single-agent steps | Commands |
| `^[a-z0-9-]+\.md$` | `api.md` | Standards, workflows, agents | Multiple |
| `^[a-z0-9-]+\.ya?ml$` | `implementers.yml` | Role files | Roles |
| `^[a-z0-9-]+$` | `database-engineer` | IDs (profiles, roles) | Multiple |
| `{{standards/{cat}/*}}` | `{{standards/global/*}}` | Standards inclusion | Templates |
| `{{workflows/{phase}/{name}}}` | `{{workflows/specification/write-spec}}` | Workflow inclusion | Templates |

### File Extension Reference

| Extension | Component | Required | Notes |
|-----------|-----------|----------|-------|
| `.md` | Markdown content | ✅ Yes | Commands, standards, workflows, agents |
| `.yml` | YAML data | ✅ Yes | Roles, configuration |
| `.yaml` | YAML data (alt) | ✅ Yes | Alternative to `.yml` |
| `.json` | JSON data | ❌ No | Not used in current version |
| `.sh` | Shell scripts | ✅ Yes | Installation and management scripts |

---

## Related Documentation

- [Profiles Analysis](./profiles-analysis.md) - Profile structure details
- [Commands Analysis](./commands-analysis.md) - Command patterns
- [Standards Analysis](./standards-analysis.md) - Standards organization
- [Workflows Analysis](./workflows-analysis.md) - Workflow patterns
- [Roles Analysis](./roles-analysis.md) - Role YAML structure
- [Agents Analysis](./agents-analysis.md) - Agent compilation
- [Scripts Analysis](./scripts-analysis.md) - Script inventory
- [API Specification](../api-spec_Version2.md) - Full API spec
- [Claude Code Integration Policy](../CLAUDE_CODE_INTEGRATION_POLICY_Version2.md) - Integration rules

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0 | 2025-10-14 | Complete convention mapping for v2.0.3 |
| 1.0 | 2025-10-13 | Initial API specification |

---

**Last Updated:** 2025-10-14  
**Analysis Version:** 2.0  
**Agent OS Version:** 2.0.3  
**Source Repository:** https://github.com/buildermethods/agent-os

**This document is the canonical reference for all Agent OS file system conventions and API mappings.**
