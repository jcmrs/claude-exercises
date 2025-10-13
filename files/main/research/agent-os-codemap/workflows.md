# Agent OS: Workflows and CI/CD Analysis

## Overview

This document provides detailed analysis of all workflows in Agent OS, including GitHub Actions CI/CD pipelines, runtime script workflows, and user-facing command workflows.

**Related Documents:**
- [Project Roadmap](./roadmap.md)
- [Code Map](./codemap.md)
- [Architecture Diagrams](./architecture.md)

---

## GitHub Actions Workflows

### 1. PR Decline Workflow (`.github/workflows/pr-decline.yml`)

#### Purpose
Automates the process of declining pull requests with standardized messaging based on predefined categories.

#### Triggers
- **Event:** `pull_request_target` with `labeled` action
- **Workflow Dispatch:** Manual trigger with inputs

#### Workflow Dispatch Inputs
- `pr_number` (required) - PR number to act on
- `reason` (required, choice) - Decline reason
  - Out of scope
  - Low info
  - Duplicate
  - Spam

#### Label Mappings
```yaml
"Close PR: Out of scope" → "Out of scope"
"Close PR: Low info" → "Low info"
"Close PR: Duplicate" → "Duplicate"
"Close PR: Spam" → "Spam"
```

#### Jobs

##### `decline` Job
**Runs on:** `ubuntu-latest`

**Permissions:**
- `pull-requests: write`
- `contents: read`

**Steps:**

1. **Determine PR number and reason**
   - Uses: `actions/github-script@v7`
   - Logic:
     - If triggered by label: Extract PR number and map label to reason
     - If workflow dispatch: Use input values
     - Skip if not a decline label

2. **Stop if not a decline event**
   - Condition: `skip == 'true' || pr == '' || reason == ''`
   - Action: Echo message and exit

3. **Build canned message**
   - Uses: `actions/github-script@v7`
   - Generates appropriate message based on reason
   - Environment variables:
     - `IDEAS_URL`: https://github.com/buildermethods/agent-os/discussions/categories/ideas
     - `CONTRIBUTING_URL`: https://github.com/buildermethods/agent-os/blob/main/.github/CONTRIBUTING.md

4. **Comment and close PR**
   - Uses: GitHub CLI (`gh`)
   - Posts comment with reason
   - Closes the PR

#### Canned Messages

**Out of scope:**
```
Thanks for the PR! After review, this change isn't on the current roadmap for Agent OS.
We keep core focused to manage long-term maintenance and compatibility.

If you'd like to continue the conversation, please start a proposal in **Ideas**: <URL>
If you publish a fork/plugin/example, feel free to share it in **Show & Tell** so others can try it.

Closing to keep the backlog focused.
```

**Low info:**
```
Thanks for the PR! We're missing required details for review.

Please update the PR with:
• Summary
• Checklist
• Documented steps to test

Guidelines: <URL>

Once updated, you can open a new PR or ask a maintainer to reopen.
```

**Duplicate:**
```
Thanks for the PR! This appears to duplicate existing work or discussion.
We'll consolidate on the canonical thread/PR to reduce churn.

Closing this one to keep things tidy.
```

**Spam:**
```
Closing this PR. It doesn't meet our contribution policy.
```

#### Workflow Diagram

```mermaid
graph TD
    A[PR Labeled or Manual Trigger] --> B{Event Type?}
    B -->|Label| C[Extract PR & Reason from Label]
    B -->|Dispatch| D[Use Input PR & Reason]
    C --> E{Valid Decline Label?}
    D --> F[Have PR & Reason?]
    E -->|No| G[Skip - No Action]
    E -->|Yes| H[Build Message]
    F -->|No| G
    F -->|Yes| H
    H --> I[Post Comment to PR]
    I --> J[Close PR]
    G --> K[End]
    J --> K
```

---

### 2. Stale Issues Workflow (`.github/workflows/stale.yml`)

#### Purpose
Automatically marks and closes issues that have been inactive for a specified period.

#### Triggers
- **Schedule:** Cron `0 9 * * *` (Daily at 09:00 UTC)

#### Jobs

##### `stale` Job
**Runs on:** `ubuntu-latest`

**Permissions:**
- `issues: write`

**Steps:**

1. **Mark and close stale issues**
   - Uses: `actions/stale@v9`
   - Configuration:
     - `days-before-stale: 30`
     - `days-before-close: 7`
     - `exempt-issue-labels: bug`

**Stale Message:**
```
This issue has been marked stale due to inactivity. If this is still a problem,
please add new details (logs, steps to reproduce) and we'll revisit.
```

**Close Message:**
```
Closing due to inactivity. If you can provide a fresh, reproducible case on the latest version,
please open a new bug report with full details. Thanks!
```

#### Timeline

```mermaid
gantt
    title Issue Lifecycle
    dateFormat X
    axisFormat %d
    
    section Active
    Issue Open           :0, 30d
    
    section Stale
    Stale Period        :30d, 7d
    
    section Closed
    Issue Closed        :37d, 1d
```

#### Exemptions
- Issues labeled `bug` are never marked stale
- Activity on an issue resets the stale timer

---

## Script Workflows

### 1. Base Installation Workflow

#### Entry Point
```bash
bash <(curl -s https://raw.githubusercontent.com/buildermethods/agent-os/main/scripts/base-install.sh)
```

#### Process Flow

```mermaid
graph TD
    A[Start base-install.sh] --> B[Download common-functions.sh]
    B --> C[Source common functions]
    C --> D{Base install exists?}
    D -->|No| E[Perform fresh installation]
    D -->|Yes| F[Get current version]
    F --> G[Get latest version from GitHub]
    G --> H[Display version info]
    H --> I{User choice}
    I -->|1| J[Overwrite everything]
    I -->|2| K[Overwrite default profile only]
    I -->|3| L[Overwrite scripts only]
    I -->|4| M[Overwrite config.yml only]
    I -->|5| N[Cancel]
    
    J --> O[Backup to ~/agent-os.backup]
    O --> E
    
    K --> P[Remove profiles/default/]
    P --> Q[Download profile files]
    Q --> R[Installation complete]
    
    L --> S[Remove scripts/]
    S --> T[Download script files]
    T --> U[chmod +x scripts]
    U --> R
    
    M --> V[Backup config.yml]
    V --> W[Download new config.yml]
    W --> R
    
    E --> X[Create ~/agent-os/]
    X --> Y[Fetch file list from GitHub API]
    Y --> Z[Download all files with exclusions]
    Z --> AA[chmod +x scripts]
    AA --> R
    
    N --> AB[Exit]
    R --> AB
```

#### File Exclusions
- `scripts/base-install.sh` (self-exclusion)
- `old-versions/*`
- `.git*`
- `.github/*`

#### Key Operations
1. **Bootstrap** - Download common-functions.sh first
2. **API Query** - Use GitHub API to list all repository files
3. **Batch Download** - Download all files (with exclusions)
4. **Permission Setting** - Make scripts executable
5. **Selective Updates** - Four update strategies for existing installations

---

### 2. Project Installation Workflow

#### Entry Point
```bash
~/agent-os/scripts/project-install.sh [OPTIONS]
```

#### Pre-Conditions
- Base installation must exist at `~/agent-os/`
- Command must be run from project root directory
- Project directory must not be `~/agent-os/` itself

#### Process Flow

```mermaid
graph TD
    A[Start project-install.sh] --> B[Parse command-line arguments]
    B --> C[Validate base installation]
    C -->|Missing| D[Error: Run base-install.sh first]
    C -->|Present| E[Load base config]
    E --> F{Project install exists?}
    F -->|Yes| G{--re-install flag?}
    F -->|No| H[Proceed with installation]
    G -->|Yes| I[Confirm destructive action]
    G -->|No| J[Delegate to project-update.sh]
    I -->|Confirmed| K[Delete existing installation]
    I -->|Cancelled| L[Exit]
    K --> H
    
    H --> M[Merge config layers]
    M --> N[Create agent-os/ directory]
    N --> O[Write project config.yml]
    O --> P[Install standards files]
    
    P --> Q{Single-agent mode?}
    Q -->|Yes| R[Install roles/]
    R --> S[Install commands to agent-os/]
    S --> T{Also multi-agent?}
    T -->|Yes| U[Continue to multi-agent]
    T -->|No| V[Complete]
    
    Q -->|No| U
    U --> W{Multi-agent tool?}
    W -->|claude-code| X[Install to .claude/]
    W -->|other| Y[Tool-specific install]
    
    X --> Z[Install commands to .claude/commands/agent-os/]
    Z --> AA[Install static agents]
    AA --> AB[Generate implementer agents from roles]
    AB --> AC[Generate verifier agents from roles]
    AC --> V
    
    Y --> V
    D --> L
    J --> L
    V --> L
```

#### Configuration Layer Merging
```
Command-line args > Base config (~/agent-os/config.yml)
```

#### Installation Targets

**Always Created:**
- `<project>/agent-os/config.yml`
- `<project>/agent-os/standards/`

**Single-Agent Mode:**
- `<project>/agent-os/roles/`
- `<project>/agent-os/commands/`

**Multi-Agent Mode (Claude Code):**
- `<project>/.claude/commands/agent-os/`
- `<project>/.claude/agents/agent-os/`

#### Dynamic Agent Generation

**Implementers (from `roles/implementers.yml`):**
1. Read role definition (id, description, responsibilities, standards)
2. Load agent template (`agents/templates/implementer.md`)
3. Substitute template variables with role data
4. Process standards patterns (e.g., `global/*` → include all global standards)
5. Write compiled agent to `.claude/agents/agent-os/<role-id>.md`

**Verifiers (from `roles/verifiers.yml`):**
Same process using `agents/templates/verifier.md`

---

### 3. Project Update Workflow

#### Entry Point
```bash
~/agent-os/scripts/project-update.sh [OPTIONS]
```

#### Pre-Conditions
- Base installation exists
- Project installation exists
- Run from project root

#### Process Flow

```mermaid
graph TD
    A[Start project-update.sh] --> B[Parse arguments]
    B --> C[Validate both installations]
    C -->|Missing base| D[Error: No base install]
    C -->|Missing project| E[Error: No project install]
    C -->|Both present| F[Load configurations]
    
    F --> G[Merge config layers]
    G --> H{--re-install flag?}
    H -->|Yes| I[Delegate to project-install.sh]
    H -->|No| J{Dry run?}
    
    J -->|Yes| K[Collect files without writing]
    J -->|No| L[Update mode]
    
    K --> M[Display file list]
    M --> N{Proceed?}
    N -->|Yes| O[Switch to real mode]
    N -->|No| P[Exit]
    O --> L
    
    L --> Q{--overwrite-all?}
    Q -->|Yes| R[Overwrite all files]
    Q -->|No| S{--overwrite-standards?}
    
    S -->|Yes| T[Update standards/]
    S -->|No| U[Skip existing standards]
    
    T --> V{--overwrite-commands?}
    U --> V
    
    V -->|Yes| W[Update commands/]
    V -->|No| X[Skip existing commands]
    
    W --> Y{--overwrite-agents?}
    X --> Y
    
    Y -->|Yes| Z[Regenerate agents]
    Y -->|No| AA[Skip existing agents]
    
    R --> AB[Report: Updated/New/Skipped]
    Z --> AB
    AA --> AB
    AB --> P
    
    D --> P
    E --> P
    I --> P
```

#### Configuration Layer Merging
```
Command-line args > Project config > Base config
```

#### Update Strategies

**Default (No Flags):**
- Skip all existing files
- Add new files only
- Update project config

**--overwrite-all:**
- Overwrite everything
- Regenerate all agents

**Selective Overwrites:**
- `--overwrite-standards` - Update standards only
- `--overwrite-commands` - Update commands only
- `--overwrite-agents` - Regenerate agents only

#### File Tracking
- `UPDATED_FILES[]` - Files that were overwritten
- `NEW_FILES[]` - Files created for first time
- `SKIPPED_FILES[]` - Existing files not updated

---

## User-Facing Command Workflows

### Command Structure

Commands support two modes:
1. **Multi-Agent** - Single command, autonomous execution with multiple agents
2. **Single-Agent** - Step-by-step prompts for manual execution

### Command: `new-spec`

#### Purpose
Initialize and research a new feature specification.

#### Multi-Agent Workflow

```mermaid
sequenceDiagram
    participant U as User
    participant C as new-spec command
    participant SI as spec-initializer
    participant SR as spec-researcher
    
    U->>C: Run new-spec
    C->>SI: Initialize spec structure
    SI->>SI: Create directory
    SI->>SI: Create spec.md skeleton
    SI->>C: Return initialized spec
    C->>SR: Research requirements
    SR->>SR: Gather context
    SR->>SR: Document findings
    SR->>C: Return research
    C->>U: Complete with spec ready for writing
```

#### Single-Agent Workflow
1. **Prompt 1: Initialize Spec** (`1-new-spec.md`)
   - User describes feature
   - Agent creates directory and spec skeleton

2. **Prompt 2: Research** (`2-research-spec.md`)
   - User confirms initialization
   - Agent researches and documents requirements

---

### Command: `create-spec`

#### Purpose
Write a formal specification from an initialized spec.

#### Multi-Agent Workflow

```mermaid
sequenceDiagram
    participant U as User
    participant C as create-spec
    participant SW as spec-writer
    participant TLC as tasks-list-creator
    participant SV as spec-verifier
    
    U->>C: Run create-spec
    C->>SW: Write formal specification
    SW->>SW: Apply standards
    SW->>SW: Structure content
    SW->>C: Return spec.md
    C->>TLC: Generate task breakdown
    TLC->>TLC: Analyze spec
    TLC->>TLC: Create tasks.md
    TLC->>C: Return tasks
    C->>SV: Verify completeness
    SV->>SV: Check standards compliance
    SV->>SV: Validate structure
    SV->>C: Verification report
    C->>U: Complete spec ready for implementation
```

#### Single-Agent Workflow
1. **Prompt 1: Write Spec** (`1-create-spec.md`)
   - Agent writes formal specification
   - Applies all relevant standards

2. **Prompt 2: Create Tasks** (`2-create-tasks-list.md`)
   - Agent breaks down spec into implementable tasks
   - Organizes by area and priority

3. **Prompt 3: Verify** (`3-verify-spec.md`)
   - Agent verifies spec completeness
   - Checks standards compliance
   - Validates task breakdown

---

### Command: `implement-spec`

#### Purpose
Implement a specification by executing all tasks.

#### Multi-Agent Workflow

```mermaid
sequenceDiagram
    participant U as User
    participant C as implement-spec
    participant DE as database-engineer
    participant AE as api-engineer
    participant UD as ui-designer
    participant TE as testing-engineer
    participant BV as backend-verifier
    participant FV as frontend-verifier
    
    U->>C: Run implement-spec
    
    loop For each task
        C->>C: Assign to appropriate implementer
        alt Database task
            C->>DE: Implement task
            DE->>BV: Request verification
            BV->>C: Verification result
        else API task
            C->>AE: Implement task
            AE->>BV: Request verification
            BV->>C: Verification result
        else UI task
            C->>UD: Implement task
            UD->>FV: Request verification
            FV->>C: Verification result
        else Testing task
            C->>TE: Implement task
            TE->>BV: Request verification
            BV->>C: Verification result
        end
    end
    
    C->>U: Implementation complete
```

#### Single-Agent Workflow
1. **Prompt: Implement Spec** (`implement-spec.md`)
   - Comprehensive prompt for implementation
   - Agent works through all tasks
   - User provides feedback and iterations

---

### Command: `plan-product`

#### Purpose
Create product planning artifacts (mission, roadmap, tech stack).

#### Multi-Agent Workflow

```mermaid
sequenceDiagram
    participant U as User
    participant C as plan-product
    participant PP as product-planner
    
    U->>C: Run plan-product
    C->>PP: Gather product information
    PP->>U: Ask questions about product
    U->>PP: Provide answers
    PP->>PP: Create product-mission.md
    PP->>PP: Create product-roadmap.md
    PP->>PP: Create product-tech-stack.md
    PP->>C: Return artifacts
    C->>U: Planning complete
```

#### Single-Agent Workflow
1. **Prompt 1: Gather Info** (`1-plan-product.md`)
   - Agent asks questions about product
   - Documents responses

2. **Prompt 2: Create Mission** (`2-create-mission.md`)
   - Agent writes product mission statement

3. **Prompt 3: Create Roadmap** (`3-create-roadmap.md`)
   - Agent creates feature roadmap

4. **Prompt 4: Create Tech Stack** (`4-create-tech-stack.md`)
   - Agent documents technology choices

---

## Workflow Best Practices

### Specification Phase
1. **Initialize** - Create structure and skeleton
2. **Research** - Gather requirements and context
3. **Write** - Formal specification with standards
4. **Task Breakdown** - Implementation-ready tasks
5. **Verify** - Completeness and compliance check

### Implementation Phase
1. **Assign Tasks** - Route to specialized agents
2. **Implement** - Write code following standards
3. **Verify** - Area-specific verification
4. **Test** - Comprehensive test coverage
5. **Document** - Update documentation and roadmap

### Integration Points

```mermaid
graph LR
    A[Standards] --> B[Spec Writing]
    A --> C[Implementation]
    A --> D[Verification]
    
    E[Workflows] --> B
    E --> C
    E --> D
    
    F[Roles] --> C
    F --> D
    
    G[Commands] --> B
    G --> C
```

---

## Hardcoded Paths and System Assumptions

### Path Assumptions

**Base Installation:**
- Path: `~/agent-os/` (hardcoded)
- Environment: User home directory
- Permissions: User-writable

**Project Installation:**
- Path: `$(pwd)/agent-os/` (current directory)
- Path: `$(pwd)/.claude/` (Claude Code integration)
- Assumption: Run from project root

**Script Locations:**
- Scripts: `~/agent-os/scripts/`
- Profiles: `~/agent-os/profiles/`
- Config: `~/agent-os/config.yml`

### System Assumptions

**Operating System:**
- POSIX-compliant (Linux, macOS)
- Bash shell (version 4.0+)
- Standard Unix utilities (awk, sed, grep)

**Network:**
- HTTPS access to GitHub
- GitHub API availability
- Raw content download capability

**Tools:**
- curl installed and in PATH
- Optional: jq, python3 (for JSON parsing)

### Environment Variables Used

**In Scripts:**
- `HOME` - User home directory
- `PWD` - Current working directory
- `BASH_SOURCE` - Script location resolution

**In GitHub Actions:**
- `GITHUB_TOKEN` - Authentication
- `REPO` - Repository identifier
- `IDEAS_URL` - Discussion URL
- `CONTRIBUTING_URL` - Contributing guidelines URL

---

## Workflow Dependencies

### Installation Dependencies

```mermaid
graph TD
    A[Network Access] --> B[base-install.sh]
    C[Bash Shell] --> B
    D[curl] --> B
    E[Home Directory] --> B
    
    B --> F[~/agent-os/ created]
    
    F --> G[project-install.sh]
    H[Project Directory] --> G
    I[Write Permissions] --> G
    
    G --> J[Project Installation]
```

### Runtime Dependencies

```mermaid
graph TD
    A[Base Config] --> B[Config Resolution]
    C[Project Config] --> B
    D[CLI Args] --> B
    
    B --> E[Effective Config]
    
    E --> F{Mode?}
    F -->|Multi-Agent| G[Claude Code]
    F -->|Single-Agent| H[Generic Tool]
    
    G --> I[Agent Generation]
    G --> J[Command Compilation]
    
    K[Role Definitions] --> I
    L[Agent Templates] --> I
    M[Standards] --> I
    M --> J
    N[Workflows] --> J
```

---

## Error Scenarios and Recovery

### Installation Errors

**Missing Base Installation:**
- Error: "Base installation not found"
- Recovery: Run `base-install.sh`
- Prevention: Check before project-install

**Network Failures:**
- Error: "Failed to download files"
- Recovery: Retry with network access
- Prevention: Validate connectivity first

**Permission Denied:**
- Error: "Cannot write to directory"
- Recovery: Check permissions, run from correct location
- Prevention: Validate write access before operations

**Corrupted Installation:**
- Error: "Missing required files"
- Recovery: Use `--re-install` or `--overwrite-all`
- Prevention: Atomic operations where possible

### Configuration Errors

**Invalid YAML:**
- Error: "Cannot parse config"
- Recovery: Fix syntax or restore backup
- Prevention: Validate before writing

**Mode Conflicts:**
- Error: "Both single and multi-agent enabled"
- Recovery: Choose one mode
- Prevention: Validation in common-functions.sh

**Missing Profile:**
- Error: "Profile not found"
- Recovery: Use 'default' profile or create custom
- Prevention: Validate profile exists before use

---

## Related Documentation

- [Project Roadmap](./roadmap.md) - Modernization strategy
- [Code Map](./codemap.md) - Component details
- [Architecture Diagrams](./architecture.md) - System architecture
- [Configuration Guide](./config.md) - Configuration details
- [Commands Reference](./commands.md) - CLI commands
- [Refactoring Notes](./refactoring-notes.md) - Action items

---

**Last Updated:** 2025-10-13  
**Analysis Version:** 1.0  
**Source Repository:** https://github.com/buildermethods/agent-os
