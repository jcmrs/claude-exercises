# Agent OS: Commands Structure and API Mapping Analysis

## Executive Summary

This document provides comprehensive analysis of the Agent OS command system, including file structures, naming conventions, API mappings, and execution patterns. Commands are the primary user-facing interface for Agent OS workflows.

**Key Findings:**
- 4 core commands with dual-mode support (multi-agent + single-agent)
- 14 total command files across both modes
- Strict naming conventions enable automatic step discovery
- Commands map directly to API endpoints with mode selection

---

## Command Inventory

### Available Commands

| Command | Purpose | Multi-Agent | Single-Agent Steps | Total Files |
|---------|---------|-------------|-------------------|-------------|
| `create-spec` | Create specification with tasks list | ✅ | 3 steps | 4 |
| `implement-spec` | Implement a specification | ✅ | 1 step | 2 |
| `new-spec` | Initialize and research new specification | ✅ | 2 steps | 3 |
| `plan-product` | Create product plan with mission/roadmap/tech stack | ✅ | 4 steps | 5 |
| **Total** | | **4** | **10 steps** | **14** |

---

## Directory Structure

### Command Organization

```
profiles/default/commands/
├── create-spec/
│   ├── multi-agent/
│   │   └── create-spec.md
│   └── single-agent/
│       ├── 1-create-spec.md
│       ├── 2-create-tasks-list.md
│       └── 3-verify-spec.md
├── implement-spec/
│   ├── multi-agent/
│   │   └── implement-spec.md
│   └── single-agent/
│       └── implement-spec.md
├── new-spec/
│   ├── multi-agent/
│   │   └── new-spec.md
│   └── single-agent/
│       ├── 1-new-spec.md
│       └── 2-research-spec.md
└── plan-product/
    ├── multi-agent/
    │   └── plan-product.md
    └── single-agent/
        ├── 1-plan-product.md
        ├── 2-create-mission.md
        ├── 3-create-roadmap.md
        └── 4-create-tech-stack.md
```

---

## File Naming Conventions

### Multi-Agent Mode

**Pattern:** `profiles/{profile}/commands/{command-name}/multi-agent/{command-name}.md`

**Rules:**
- Exactly one file per command
- File name matches command directory name
- Contains complete orchestration prompt for multi-agent execution
- No step numbers (single atomic prompt)

**Examples:**
```
commands/create-spec/multi-agent/create-spec.md
commands/new-spec/multi-agent/new-spec.md
commands/plan-product/multi-agent/plan-product.md
```

### Single-Agent Mode

**Pattern:** `profiles/{profile}/commands/{command-name}/single-agent/{step-number}-{step-name}.md`

**Rules:**
- **Step Number:** Must start with digits (`\d+`)
- **Separator:** Hyphen (`-`) after step number
- **Step Name:** Descriptive identifier (lowercase, hyphenated)
- **Extension:** `.md` only
- **Regex:** `/^\d+-.*\.md$/`

**Valid Examples:**
```
1-create-spec.md
2-create-tasks-list.md
3-verify-spec.md
10-advanced-step.md
```

**Invalid Examples (Ignored by Step Discovery):**
```
create-spec.md          # No step number
01-create-spec.md       # Leading zero (valid but not recommended)
step-1-create-spec.md   # Doesn't start with digit
README.md               # Doesn't match pattern
notes.txt               # Wrong extension
```

### Step Numbering Conventions

| Convention | Status | Behavior | Example |
|------------|--------|----------|---------|
| Sequential (1, 2, 3, ...) | ✅ Recommended | Steps in order | `1-init.md, 2-process.md, 3-verify.md` |
| Gaps allowed (1, 3, 5, ...) | ✅ Supported | Sorted numerically | `1-init.md, 3-verify.md` (no step 2) |
| Leading zeros (01, 02, ...) | ⚠️ Works but avoid | Sorted numerically | `01-init.md, 02-process.md` |
| Decimal numbers (1.5, 2.3) | ❌ Not supported | Would sort incorrectly | Avoid |

---

## API Endpoint Mappings

### List Commands

**Endpoint:** `GET /commands?profile={profile}&mode={mode}`

**Query Parameters:**
- `profile` (optional): Profile name, defaults to "default"
- `mode` (optional): Filter by mode ("multi-agent", "single-agent", or both)

**File System Action:**
- List directory names in `profiles/{profile}/commands/`
- For each directory, check presence of `multi-agent/` and/or `single-agent/` subdirectories

**Response Example:**
```json
[
  {
    "name": "create-spec",
    "profile": "default",
    "modes": ["multi-agent", "single-agent"],
    "single_agent_steps": 3
  },
  {
    "name": "implement-spec",
    "profile": "default",
    "modes": ["multi-agent", "single-agent"],
    "single_agent_steps": 1
  }
]
```

### Get Multi-Agent Command

**Endpoint:** `GET /commands/{commandName}?mode=multi-agent&profile={profile}`

**File System Mapping:**
```
URL: /commands/create-spec?mode=multi-agent&profile=default
→ File: profiles/default/commands/create-spec/multi-agent/create-spec.md
```

**Response:**
```json
{
  "command": "create-spec",
  "mode": "multi-agent",
  "profile": "default",
  "content": "# Create Spec Process\n\n..."
}
```

### Get Single-Agent Command

**Endpoint:** `GET /commands/{commandName}?mode=single-agent&profile={profile}`

**File System Mapping:**
```
URL: /commands/create-spec?mode=single-agent&profile=default
→ Directory: profiles/default/commands/create-spec/single-agent/
→ Files: Find all matching /^\d+-.*\.md$/
→ Sort: Numerically by step number
```

**Response:**
```json
{
  "command": "create-spec",
  "mode": "single-agent",
  "profile": "default",
  "steps": [
    {
      "step": 1,
      "filename": "1-create-spec.md",
      "content": "Now that we've initiated..."
    },
    {
      "step": 2,
      "filename": "2-create-tasks-list.md",
      "content": "Create a comprehensive..."
    },
    {
      "step": 3,
      "filename": "3-verify-spec.md",
      "content": "Verify the specification..."
    }
  ]
}
```

### Create/Update Command

**Endpoint:** `POST /commands`

**For Multi-Agent Mode:**
```json
{
  "name": "create-spec",
  "mode": "multi-agent",
  "profile": "default",
  "content": "# Multi-agent workflow\n..."
}
```

**File System Action:**
- Write to: `profiles/{profile}/commands/{name}/multi-agent/{name}.md`
- Create directory if missing
- Overwrite existing file

**For Single-Agent Mode:**
```json
{
  "name": "create-spec",
  "mode": "single-agent",
  "profile": "default",
  "steps": [
    {
      "step": 1,
      "filename": "1-create-spec.md",
      "content": "..."
    },
    {
      "step": 2,
      "filename": "2-create-tasks-list.md",
      "content": "..."
    }
  ]
}
```

**File System Action:**
- Write each step to: `profiles/{profile}/commands/{name}/single-agent/{filename}`
- Validate filename matches pattern `/^\d+-.*\.md$/`
- Create directory if missing
- Overwrite existing files with matching names

### Delete Command

**Endpoint:** `DELETE /commands/{commandName}?mode={mode}&profile={profile}`

**File System Action:**
- For multi-agent: Remove `profiles/{profile}/commands/{name}/multi-agent/{name}.md`
- For single-agent: Remove all files in `profiles/{profile}/commands/{name}/single-agent/`
- Optionally remove empty directories

---

## Command Details

### create-spec

**Purpose:** Create a comprehensive specification document with tasks breakdown

**Workflow:**
1. Write the specification document
2. Create the tasks list
3. Verify spec & tasks list

**File Breakdown:**

| Mode | File | Purpose |
|------|------|---------|
| Multi-Agent | `multi-agent/create-spec.md` | Orchestrates spec-writer and tasks-list-creator subagents |
| Single-Agent | `1-create-spec.md` | Guides user to write spec using `workflows/specification/write-spec` |
| Single-Agent | `2-create-tasks-list.md` | Guides user to create tasks using `workflows/specification/create-tasks-list` |
| Single-Agent | `3-verify-spec.md` | Verifies spec and tasks completeness |

**Template Expansions Used:**
- `{{workflows/specification/write-spec}}`
- `{{workflows/specification/create-tasks-list}}`
- `{{standards/*}}`

### implement-spec

**Purpose:** Implement features from a specification's tasks list

**Workflow:**
- Execute implementation with role-based agents (database-engineer, api-engineer, etc.)

**File Breakdown:**

| Mode | File | Purpose |
|------|------|---------|
| Multi-Agent | `multi-agent/implement-spec.md` | Orchestrates implementer subagents for each task |
| Single-Agent | `implement-spec.md` | Single comprehensive prompt (no steps) |

**Special Note:** Single-agent mode has only one file (not numbered) because implementation is a single continuous process.

### new-spec

**Purpose:** Initialize a new specification with research

**Workflow:**
1. Initialize spec structure
2. Research and gather requirements

**File Breakdown:**

| Mode | File | Purpose |
|------|------|---------|
| Multi-Agent | `multi-agent/new-spec.md` | Orchestrates spec-initializer and spec-researcher subagents |
| Single-Agent | `1-new-spec.md` | Guides user to initialize spec using `workflows/specification/initialize-spec` |
| Single-Agent | `2-research-spec.md` | Guides user to research using `workflows/specification/research-spec` |

**Template Expansions Used:**
- `{{workflows/specification/initialize-spec}}`
- `{{workflows/specification/research-spec}}`

### plan-product

**Purpose:** Create comprehensive product planning documentation

**Workflow:**
1. Gather product information
2. Create mission statement
3. Create product roadmap
4. Define tech stack

**File Breakdown:**

| Mode | File | Purpose |
|------|------|---------|
| Multi-Agent | `multi-agent/plan-product.md` | Orchestrates product-planner subagent |
| Single-Agent | `1-plan-product.md` | Gather information using `workflows/planning/gather-product-info` |
| Single-Agent | `2-create-mission.md` | Create mission using `workflows/planning/create-product-mission` |
| Single-Agent | `3-create-roadmap.md` | Create roadmap using `workflows/planning/create-product-roadmap` |
| Single-Agent | `4-create-tech-stack.md` | Define tech stack using `workflows/planning/create-product-tech-stack` |

**Template Expansions Used:**
- `{{workflows/planning/gather-product-info}}`
- `{{workflows/planning/create-product-mission}}`
- `{{workflows/planning/create-product-roadmap}}`
- `{{workflows/planning/create-product-tech-stack}}`

---

## Step Discovery Algorithm

### Pseudo-code

```python
def discover_steps(command_name, profile):
    directory = f"profiles/{profile}/commands/{command_name}/single-agent/"
    
    if not directory.exists():
        return []
    
    steps = []
    for file in directory.list_files():
        if file.name.matches(r'^\d+-.*\.md$'):
            step_number = extract_leading_number(file.name)
            steps.append({
                'step': step_number,
                'filename': file.name,
                'content': file.read()
            })
    
    # Sort numerically by step number (not alphabetically)
    steps.sort(key=lambda s: s['step'])
    
    return steps
```

### Edge Cases

| Scenario | Behavior | Example |
|----------|----------|---------|
| Missing step numbers (gaps) | Continue, skip missing | Files: 1, 3, 5 → Steps: 1, 3, 5 |
| Out of order file names | Sort numerically | Files: 3, 1, 2 → Steps: 1, 2, 3 |
| Non-matching files | Ignore silently | `README.md`, `notes.txt` ignored |
| Empty directory | Return empty array | No error |
| Duplicate step numbers | Undefined (avoid) | `1-a.md`, `1-b.md` → Last wins or error |

---

## Claude Code Integration

### Multi-Agent Execution
1. API returns single Markdown file
2. Claude Code presents entire prompt to main agent
3. Main agent orchestrates subagents based on prompt instructions
4. Process runs autonomously until completion

### Single-Agent Execution
1. API returns array of ordered steps
2. Claude Code presents step 1 to agent
3. Agent completes step 1
4. Claude Code waits for confirmation/completion
5. Claude Code presents step 2
6. Repeat until all steps completed

**Important:** Batch execution discouraged; sequential step-by-step ensures quality.

### File Discovery Integration
- Claude Code calls API endpoint for single-agent mode
- Receives ordered array of steps
- Does NOT parse file system directly
- Relies on API for step discovery and ordering

---

## Profile Inheritance

### Command Override Behavior

**Rule:** Child profile command completely overrides parent command (all steps).

**Example:**
```
profiles/
├── base/
│   └── commands/create-spec/single-agent/
│       ├── 1-create-spec.md
│       ├── 2-create-tasks-list.md
│       └── 3-verify-spec.md
└── custom/
    └── commands/create-spec/single-agent/
        └── 1-create-spec.md  # Only this step
```

**Resolution:**
- Custom profile has `create-spec` → Use ONLY custom profile's step(s)
- Result: Only step 1 from custom profile used
- Parent's steps 2 and 3 are NOT used (no fallback)

**Rationale:** Prevents mixed/inconsistent workflows from different profiles.

---

## Template Compilation

### Command File Template Processing

During installation, command files may contain template expansions:

**Raw Command File:**
```markdown
Follow this workflow:

{{workflows/specification/write-spec}}

Apply these standards:

{{standards/global/*}}
```

**Compiled Result:**
```markdown
Follow this workflow:

## [Workflow: write-spec.md]
[Content of write-spec.md workflow]

Apply these standards:

## [Standard: coding-style.md]
[Content of coding-style.md]

## [Standard: commenting.md]
[Content of commenting.md]
```

### Compilation Timing
- **Multi-Agent Mode:** Templates expanded during installation
- **Single-Agent Mode:** Templates may be expanded on-demand or during installation
- **API Mode:** Templates expanded at request time

---

## Error Handling

### Missing Files

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| Command doesn't exist | 404 | `{"error": "Command not found"}` | Return error |
| Mode not supported | 404 | `{"error": "Mode not available"}` | Return error |
| No steps in single-agent | 200 | `{"steps": []}` | Return empty array |
| Profile doesn't exist | 404 | `{"error": "Profile not found"}` | Return error |

### Validation Errors

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| Invalid step filename | 400 | `{"error": "Invalid filename"}` | Reject creation |
| Invalid mode parameter | 400 | `{"error": "Invalid mode"}` | Return error |
| Duplicate step number | 409 | `{"error": "Duplicate step"}` | Reject or overwrite |

### Parse Errors

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| Malformed Markdown | 200 | Return content as-is | No validation |
| Template expansion error | 500 | `{"error": "Expansion failed"}` | Return error with details |
| Circular include | 500 | `{"error": "Circular dependency"}` | Abort expansion |

---

## Performance Considerations

### Caching
- Cache command metadata (available commands, step counts)
- Cache compiled templates per profile
- Invalidate cache on file system changes

### Optimization
- Read all step files in parallel (async I/O)
- Pre-compile templates during installation
- Use in-memory storage for frequently accessed commands

### Concurrency
- Multiple users can access same command concurrently (read-only)
- Write operations should use file locks
- Step discovery should be atomic per request

---

## Testing Recommendations

### Unit Tests
- [ ] Step discovery with sequential numbering
- [ ] Step discovery with gaps in numbering
- [ ] Step discovery with out-of-order files
- [ ] Non-matching file filtering
- [ ] Profile inheritance command override
- [ ] Template expansion basic
- [ ] Template expansion recursive
- [ ] Template expansion with circular dependency

### Integration Tests
- [ ] API endpoint `/commands` listing
- [ ] API endpoint `/commands/{name}` multi-agent mode
- [ ] API endpoint `/commands/{name}` single-agent mode
- [ ] Command creation via POST
- [ ] Command deletion via DELETE
- [ ] Profile switching
- [ ] Mode switching

### Edge Case Tests
- [ ] Empty command directory
- [ ] Command with only multi-agent mode
- [ ] Command with only single-agent mode
- [ ] Duplicate step numbers
- [ ] Very large step numbers (1000+)
- [ ] Special characters in filenames
- [ ] Missing profile

---

## Related Documentation

- [API Specification](../api-spec_Version2.md) - Full API contract
- [Claude Code Integration Policy](../CLAUDE_CODE_INTEGRATION_POLICY_Version2.md) - Integration rules
- [Profiles Analysis](./profiles-analysis.md) - Profile structure details
- [Workflows Analysis](./workflows-analysis.md) - Workflow file details
- [Standards Analysis](./standards-analysis.md) - Standards organization

---

## Recommendations

### Immediate Actions
1. Add validation for step filename patterns
2. Implement duplicate step number detection
3. Create command template/wizard
4. Document expected command structure in README

### Short-Term Improvements
1. Add command metadata files (description, tags, version)
2. Implement command dependencies (prerequisites)
3. Add command validation tests
4. Create command development guide

### Long-Term Vision
1. Command marketplace/registry
2. Dynamic command loading via API
3. Command composition (chain commands)
4. Command analytics and usage tracking
5. Interactive command builder UI

---

**Last Updated:** 2025-10-14  
**Analysis Version:** 2.0  
**Agent OS Version Analyzed:** 2.0.3  
**Source Repository:** https://github.com/buildermethods/agent-os
