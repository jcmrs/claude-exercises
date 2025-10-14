# Agent OS: Agents Structure and Compilation Analysis

## Executive Summary

This document provides comprehensive analysis of the Agent OS agents system, including agent definitions, templates, compilation processes, and multi-agent orchestration patterns. Agents are specialized AI instances with specific roles, responsibilities, and capabilities.

**Key Findings:**
- 9 agent definition files (7 specialized + 2 templates)
- Template-based agent generation from role definitions
- Agent metadata in frontmatter (name, description, tools, color, model)
- Agents compiled during multi-agent mode installation

---

## Agent Inventory

### Agent Files

| Type | Count | Location | Purpose |
|------|-------|----------|---------|
| Specialized Agents | 7 | `agents/*.md` and `agents/specification/*.md` | Pre-defined agent definitions |
| Agent Templates | 2 | `agents/templates/*.md` | Templates for dynamic agent generation |
| **Total** | **9** | | Complete agent library |

### Specialized Agents

| Agent | Phase | Purpose |
|-------|-------|---------|
| `implementation-verifier.md` | Implementation | Verify implementation completeness and quality |
| `product-planner.md` | Planning | Create product plans, missions, roadmaps |
| `spec-initializer.md` | Specification | Initialize new specification structure |
| `spec-researcher.md` | Specification | Research requirements for specifications |
| `spec-verifier.md` | Specification | Verify specification completeness |
| `spec-writer.md` | Specification | Write specification documents |
| `tasks-list-creator.md` | Specification | Create implementation tasks lists |

### Agent Templates

| Template | Purpose | Used For |
|----------|---------|----------|
| `implementer.md` | Template for implementer agents | Dynamically generated from `implementers.yml` roles |
| `verifier.md` | Template for verifier agents | Dynamically generated from `verifiers.yml` roles |

---

## Directory Structure

### Complete File Tree

```
profiles/default/agents/
├── implementation-verifier.md      # Verifies implementation
├── product-planner.md              # Plans products
├── specification/                  # Specification-related agents
│   ├── spec-initializer.md
│   ├── spec-researcher.md
│   ├── spec-verifier.md
│   ├── spec-writer.md
│   └── tasks-list-creator.md
└── templates/                      # Agent generation templates
    ├── implementer.md
    └── verifier.md
```

**Pattern:** `profiles/{profile}/agents/{agent-name}.md` or `profiles/{profile}/agents/{category}/{agent-name}.md`

### File Naming Conventions

**Rules:**
- Lowercase only
- Hyphen-separated words
- Descriptive of agent role
- `.md` extension required
- Optional subdirectory categorization

**Valid Examples:**
```
product-planner.md
implementation-verifier.md
spec-writer.md
api-engineer.md (generated)
database-engineer.md (generated)
```

---

## Agent File Structure

### Frontmatter Metadata

All agent files contain YAML frontmatter with metadata:

```yaml
---
name: {agent-name}
description: {brief description}
tools: {comma-separated or array}
color: {color-name}
model: {model-name}
---
```

### Frontmatter Fields

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `name` | string | Agent display name | `database-engineer` or `Spec Writer` |
| `description` | string | Brief description of agent's purpose | `Handles migrations, models, schemas` |
| `tools` | string/array | Available tools | `Write, Read, Bash, WebFetch` |
| `color` | string | Visual identifier | `orange`, `blue`, `purple` |
| `model` | string | LLM model | `inherit`, `sonnet`, `opus`, `haiku` |

### Content Structure

After frontmatter, agent content typically includes:

```markdown
---
{frontmatter}
---

{Your role statement}

## Core Responsibilities
{Overview of responsibilities}

## Workflow
{Step-by-step process}

## Standards
{Standards to follow}
```

---

## Static Agent Definitions

### Specification Phase Agents

#### spec-initializer
- **Purpose:** Initialize new specification structure
- **Creates:** `agent-os/specs/{spec-name}/` directory structure
- **Outputs:** `planning/requirements.md`, `planning/visuals/` folder
- **Used In:** `new-spec` command (multi-agent mode)

#### spec-researcher
- **Purpose:** Research and gather requirements
- **Reads:** User input, existing documentation
- **Outputs:** Enhanced requirements, research notes
- **Used In:** `new-spec` command (multi-agent mode)

#### spec-writer
- **Purpose:** Write specification document
- **Reads:** `planning/requirements.md`, visuals
- **Outputs:** `spec.md` (specification document)
- **Used In:** `create-spec` command (multi-agent mode)

#### tasks-list-creator
- **Purpose:** Create implementation tasks list
- **Reads:** `spec.md`
- **Outputs:** `tasks.md` (tasks breakdown)
- **Used In:** `create-spec` command (multi-agent mode)

#### spec-verifier
- **Purpose:** Verify specification completeness
- **Reads:** `spec.md`, `tasks.md`
- **Validates:** Completeness, clarity, feasibility
- **Used In:** `create-spec` command (multi-agent mode)

### Implementation Phase Agents

#### implementation-verifier
- **Purpose:** Verify implementation completeness
- **Reads:** Implemented code, `tasks.md`, specifications
- **Validates:** Code quality, test coverage, documentation
- **Outputs:** Verification report
- **Used In:** `implement-spec` command (multi-agent mode)

### Planning Phase Agents

#### product-planner
- **Purpose:** Create comprehensive product plans
- **Creates:** Mission statement, roadmap, tech stack
- **Outputs:** Multiple planning documents
- **Used In:** `plan-product` command (multi-agent mode)

---

## Agent Templates

### Template Purpose

Templates enable dynamic agent generation from role definitions in YAML files.

**Template + Role YAML → Compiled Agent**

### Template Variables

**Supported Variables in Templates:**

| Variable | Source | Description | Example |
|----------|--------|-------------|---------|
| `{{id}}` | Role `id` | Role identifier | `database-engineer` |
| `{{description}}` | Role `description` | Brief description | `Handles migrations, models` |
| `{{your_role}}` | Role `your_role` | First-person role statement | `You are a database engineer...` |
| `{{tools}}` | Role `tools` | Available tools | `Write, Read, Bash, WebFetch` |
| `{{color}}` | Role `color` | Visual identifier | `orange` |
| `{{model}}` | Role `model` | LLM model | `inherit` |
| `{{areas_of_responsibility}}` | Role array | Markdown list of responsibilities | `- Create database migrations\n- ...` |
| `{{example_areas_outside_of_responsibility}}` | Role array | Markdown list of anti-responsibilities | `- Create API endpoints\n- ...` |
| `{{standards}}` | Role `standards` | Expanded standards content | Full standards text |
| `{{workflows/*}}` | Workflow files | Expanded workflow content | Full workflow text |
| `{{verified_by}}` | Role `verified_by` | Verifier agent ID(s) | `backend-verifier` |

### Implementer Template

**Source:** `agents/templates/implementer.md`

**Template Structure:**
```markdown
---
name: {{id}}
description: {{description}}
tools: {{tools}}
color: {{color}}
model: {{model}}
---

{{your_role}}

## Core Responsibilities
{{workflows/implementation/implementer-responsibilities}}

## Your Areas of specialization
{{areas_of_responsibility}}

Examples of areas NOT your responsibility:
{{example_areas_outside_of_responsibility}}

## Workflow
### Step 1: Analyze YOUR assigned task
### Step 2: Search for Existing Patterns
{{workflows/implementation/analyze-patterns}}
### Step 3: Implement Your Tasks
{{workflows/implementation/implement-task}}
### Step 4: Update tasks.md
{{workflows/implementation/update-tasks-list}}
### Step 5: Document your implementation
{{workflows/implementation/document-implementation}}

## Standards to Follow
{{standards}}

## Verified By
{{verified_by}}
```

### Verifier Template

**Source:** `agents/templates/verifier.md`

**Template Structure:**
```markdown
---
name: {{id}}
description: {{description}}
tools: {{tools}}
color: {{color}}
model: {{model}}
---

{{your_role}}

## Core Responsibilities
{{workflows/implementation/verifier-responsibilities}}

## Your Areas of Verification
{{areas_of_responsibility}}

## Verification Workflow
{{workflows/implementation/verification/*}}

## Standards to Check
{{standards}}
```

---

## Agent Compilation Process

### Compilation Trigger

Agent compilation occurs during project installation when `multi_agent_mode: true`.

### Compilation Steps

1. **Read Role Definitions:** Parse `roles/implementers.yml` and `roles/verifiers.yml`
2. **Load Templates:** Read appropriate template file
3. **Variable Substitution:** Replace `{{variable}}` markers with role data
4. **Standards Expansion:** Expand `{{standards/*}}` patterns with actual standard content
5. **Workflow Expansion:** Expand `{{workflows/*}}` patterns with workflow content
6. **Array Formatting:** Convert YAML arrays to Markdown lists
7. **Write Agent File:** Output to `<project>/.claude/agents/agent-os/{id}.md`

### Example Compilation

**Input Role (from `implementers.yml`):**
```yaml
implementers:
  - id: database-engineer
    description: Handles migrations, models, schemas
    your_role: You are a database engineer...
    tools: Write, Read, Bash, WebFetch
    model: inherit
    color: orange
    areas_of_responsibility:
      - Create database migrations
      - Create database models
    standards:
      - global/*
      - backend/*
    verified_by:
      - backend-verifier
```

**Output Agent (compiled to `.claude/agents/agent-os/database-engineer.md`):**
```markdown
---
name: database-engineer
description: Handles migrations, models, schemas
tools: Write, Read, Bash, WebFetch
color: orange
model: inherit
---

You are a database engineer...

## Core Responsibilities
[Expanded content from workflows/implementation/implementer-responsibilities.md]

## Your Areas of specialization
- Create database migrations
- Create database models

## Workflow
### Step 1: Analyze YOUR assigned task
### Step 2: Search for Existing Patterns
[Expanded content from workflows/implementation/analyze-patterns.md]
...

## Standards to Follow
[Expanded content from all global/* and backend/* standards]

## Verified By
backend-verifier
```

---

## Multi-Agent Orchestration

### Agent Discovery

During multi-agent command execution, the orchestrator discovers available agents:

**Location:** `<project>/.claude/agents/agent-os/*.md`

**Discovery Pattern:**
- List all `.md` files in directory
- Parse frontmatter for metadata
- Index by agent name/ID

### Agent Selection

Commands specify which agents to use:

**In Multi-Agent Command File:**
```markdown
# Create Spec Process

### PHASE 1: Delegate to Spec Writer
Use the **spec-writer** subagent to create the specification.

### PHASE 2: Delegate to Tasks List Creator
Use the **tasks-list-creator** subagent to create the tasks list.
```

**Orchestrator:**
1. Parses command to find agent references (e.g., `**spec-writer**`)
2. Looks up agent definition by name
3. Delegates work to specified agent
4. Waits for agent completion
5. Proceeds to next step

### Agent Communication

**Delegation Pattern:**
```
Main Agent → Spec Writer Agent
              ↓
              Creates: spec.md
              ↓
Main Agent ← Completion Signal
```

**Data Flow:**
- Main agent provides context (requirements, visuals, etc.)
- Subagent performs specialized task
- Subagent produces output (file, artifact)
- Main agent validates and proceeds

---

## Installation and Deployment

### Multi-Agent Mode

**Installation Command:**
```bash
~/agent-os/scripts/project-install.sh --multi-agent-mode true --multi-agent-tool claude-code
```

**Output:**
- Static agents copied: `<project>/.claude/agents/agent-os/{agent-name}.md`
- Dynamic agents compiled: `<project>/.claude/agents/agent-os/{role-id}.md`
- Commands compiled: `<project>/.claude/commands/agent-os/{command-name}.md`

### Single-Agent Mode

**Installation Command:**
```bash
~/agent-os/scripts/project-install.sh --single-agent-mode true
```

**Output:**
- Agents NOT compiled (single-agent doesn't use subagents)
- Commands copied: `<project>/agent-os/commands/{command-name}/single-agent/*.md`
- Roles copied: `<project>/agent-os/roles/*.yml` (for reference)

---

## API Endpoint Mappings

### List Agents

**Endpoint:** `GET /agents?profile={profile}`

**File System Action:**
- List all `.md` files in `profiles/{profile}/agents/` (recursively)
- Exclude `templates/` subdirectory
- Parse frontmatter for metadata

**Response Example:**
```json
[
  {
    "name": "spec-writer",
    "description": "Write specification documents",
    "tools": ["Write", "Read", "WebFetch"],
    "color": "blue",
    "model": "inherit",
    "path": "agents/specification/spec-writer.md"
  },
  {
    "name": "product-planner",
    "description": "Create product plans",
    "tools": ["Write", "Read", "WebFetch"],
    "color": "green",
    "model": "inherit",
    "path": "agents/product-planner.md"
  }
]
```

### Get Agent Definition

**Endpoint:** `GET /agents/{agentName}?profile={profile}`

**File System Mapping:**
- Search `profiles/{profile}/agents/**/{agentName}.md`
- Return full content including frontmatter

**Response:**
```json
{
  "name": "spec-writer",
  "path": "agents/specification/spec-writer.md",
  "metadata": {
    "name": "spec-writer",
    "description": "Write specification documents",
    "tools": ["Write", "Read", "WebFetch"],
    "color": "blue",
    "model": "inherit"
  },
  "content": "# Spec Writer\n\n..."
}
```

### Compile Agent from Role

**Endpoint:** `POST /agents/compile`

**Request Body:**
```json
{
  "profile": "default",
  "role_id": "database-engineer",
  "template": "implementer",
  "output_path": "<project>/.claude/agents/agent-os/"
}
```

**File System Action:**
- Read role definition from `roles/implementers.yml`
- Load template from `agents/templates/implementer.md`
- Perform variable substitution and expansion
- Write compiled agent to output path

---

## Profile Inheritance

### Agent Override Behavior

**Rule:** Child agents override parent agents by filename.

**Example:**
```
profiles/
├── base/
│   └── agents/
│       └── spec-writer.md    # Parent version
└── custom/
    └── agents/
        └── spec-writer.md    # Child version (overrides)
```

**Resolution:**
- Uses `custom/agents/spec-writer.md` (child overrides parent)

---

## Validation and Quality Control

### Agent File Validation

| Check | Description | Enforcement |
|-------|-------------|-------------|
| Valid frontmatter | YAML frontmatter is parseable | ✅ Required |
| Required metadata | `name`, `description`, `tools` present | ✅ Required |
| Valid Markdown | Content is valid Markdown | ⚠️ Recommended |
| Clear workflow | Agent has clear steps/process | ⚠️ Manual review |

### Template Validation

| Check | Description | Enforcement |
|-------|-------------|-------------|
| All variables used | All role fields have placeholders | ⚠️ Recommended |
| Valid expansions | `{{workflows/*}}` and `{{standards/*}}` are valid | ✅ Required |
| No circular refs | No circular workflow/standards includes | ✅ Required |

---

## Error Handling

### Agent Lookup Errors

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| Agent not found | 404 | `{"error": "Agent not found"}` | Return error |
| Profile not found | 404 | `{"error": "Profile not found"}` | Return error |
| Invalid frontmatter | 400 | `{"error": "Invalid metadata"}` | Return parse error |

### Compilation Errors

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| Role not found | 404 | `{"error": "Role not found"}` | Abort compilation |
| Template not found | 404 | `{"error": "Template not found"}` | Abort compilation |
| Expansion error | 500 | `{"error": "Template expansion failed"}` | Abort compilation |
| Write error | 500 | `{"error": "File write failed"}` | Abort compilation |

---

## Performance Considerations

### Caching Strategy

**Agent Definitions:**
- Cache parsed agent metadata in memory
- Invalidate on file system changes
- Cache compiled agents per project

**Compilation:**
- Cache template expansions
- Cache standards and workflow content
- Pre-compile during installation

---

## Testing Recommendations

### Unit Tests
- [ ] Agent metadata parsing
- [ ] Template variable substitution
- [ ] Standards expansion
- [ ] Workflow expansion
- [ ] Array-to-markdown conversion
- [ ] Circular dependency detection

### Integration Tests
- [ ] API `/agents` listing
- [ ] API `/agents/{name}` retrieval
- [ ] Agent compilation from role
- [ ] Multi-agent orchestration
- [ ] Agent file validation
- [ ] Profile inheritance

### Edge Case Tests
- [ ] Agent with minimal metadata
- [ ] Agent with all fields
- [ ] Very large agent content
- [ ] Missing template variable
- [ ] Circular workflow includes
- [ ] Invalid standards pattern

---

## Related Documentation

- [API Specification](../api-spec_Version2.md) - API endpoints for agents
- [Claude Code Integration Policy](../CLAUDE_CODE_INTEGRATION_POLICY_Version2.md) - Multi-agent orchestration
- [Profiles Analysis](./profiles-analysis.md) - Profile structure
- [Roles Analysis](./roles-analysis.md) - Role definitions and compilation
- [Commands Analysis](./commands-analysis.md) - How agents are used in commands

---

## Recommendations

### Immediate Actions
1. Add agent metadata validation
2. Implement template variable checking
3. Create agent authoring guide
4. Document compilation process

### Short-Term Improvements
1. Add agent versioning
2. Implement agent testing framework
3. Create agent debugging tools
4. Support agent composition (agent uses other agents)

### Long-Term Vision
1. Agent marketplace/registry
2. Dynamic agent loading via API
3. AI-assisted agent generation
4. Agent performance metrics
5. Interactive agent builder UI
6. Agent analytics and tracking
7. Agent A/B testing framework

---

**Last Updated:** 2025-10-14  
**Analysis Version:** 2.0  
**Agent OS Version Analyzed:** 2.0.3  
**Source Repository:** https://github.com/buildermethods/agent-os
