# Agent Panel Prompt v2: Complete Codemap, Convention-Driven API, and Modernization Roadmap for agent-os

## Analysis Target
- **Source repository:** https://github.com/buildermethods/agent-os

## Output Destination
- **Research repository for all generated files:** https://github.com/jcmrs/claude-exercises
- **Target folder within research repo:** /files/main/research/agent-os-codemap/
- If the folder does not exist, create it.

---

## Integrated Reference Documents

### INTEGRATED DOCUMENT: API Specification (Preliminary v1.0)
#### Agent OS API Specification (Preliminary v1.0)

##### Overview

This API provides programmatic access to all major operations in Agent OS, enabling installation, project management, configuration, profile/role/agent management, standards and command handling, workflow orchestration, file operations, and integration with external tools.

**Design Principle:**  
The API is a thin, file-based interface: all "agentic" content (commands, standards, roles, workflows, agent definitions) is governed by a structured directory of Markdown/YAML files. The API simply maps HTTP endpoints to read, write, list, and delete operations on these files, preserving full compatibility and auditability with the agent-os repository model.

---

##### Authentication

All endpoints require authentication via a bearer token.

- Header: `Authorization: Bearer <token>`

---

##### 1. Installation

###### 1.1. Base Installation

- **POST /install/base**
  - **Description:** Install Agent OS base files to a user or system context. Mirrors the script-based installation.
  - **Body:**  
    ```json
    {
      "target_path": "/home/user/agent-os", // required
      "overwrite": false // optional, default false
    }
    ```
  - **Responses:**
    - 201 Created: Base installed
    - 409 Conflict: Base already exists (if `overwrite: false`)
    - 400 Bad Request: Invalid path or permissions

###### 1.2. Project Installation

- **POST /install/project**
  - **Description:** Install Agent OS into a specific project directory (creates directory structure, copies/links files).
  - **Body:**  
    ```json
    {
      "project_path": "/path/to/project",
      "profile": "default",
      "multi_agent_mode": true,
      "single_agent_mode": false,
      "multi_agent_tool": "claude-code",
      "single_agent_tool": "generic",
      "overwrite": false
    }
    ```
  - **Responses:**
    - 201 Created: Project install complete
    - 409 Conflict: Project already exists (if not overwriting)
    - 400 Bad Request: Missing base install or invalid params

###### 1.3. Project Update

- **POST /install/project/update**
  - **Description:** Update an existing project installation. Mirrors update script logic.
  - **Body:**  
    ```json
    {
      "project_path": "/path/to/project",
      "overwrite": false,
      "overwrite_standards": false,
      "overwrite_commands": false,
      "overwrite_agents": false,
      "overwrite_all": false,
      "dry_run": false
    }
    ```
  - **Responses:**
    - 200 OK: Update complete, with file lists
    - 400 Bad Request: Project/base missing, invalid flags

---

##### 2. Project Management

###### 2.1. List Projects

- **GET /projects**
  - **Query Params:** `base_path` (optional)
  - **Responses:** 200 OK  
    ```json
    [
      {
        "name": "project1",
        "path": "/path/to/project1",
        "status": "installed"
      }
    ]
    ```

###### 2.2. Create Project

- **POST /projects**
  - **Body:**  
    ```json
    {
      "name": "project1",
      "parent_path": "/agent-os-projects"
    }
    ```
  - **Responses:** 201 Created, 409 Conflict

###### 2.3. Delete Project

- **DELETE /projects/{projectId}**
  - **Responses:** 204 No Content, 404 Not Found

---

##### 3. Profiles & Roles

###### 3.1. Profiles

- **GET /profiles**  
  List all available profiles (directory names under `/profiles`).
- **POST /profiles**  
  Create or update a profile (creates a folder and config file).
- **GET /profiles/{profileName}**  
  Get details and file structure for a profile.
- **DELETE /profiles/{profileName}**  
  Delete a profile and all its files.

###### 3.2. Roles

- **GET /profiles/{profileName}/roles**  
  List all roles for a profile (reads `roles/*.yml`).
- **POST /profiles/{profileName}/roles**  
  Create or update a role's YAML file.
- **GET /profiles/{profileName}/roles/{roleId}**  
  Get a specific role's YAML.
- **DELETE /profiles/{profileName}/roles/{roleId}**  
  Delete a role definition.

---

##### 4. Standards (File-Backed)

###### 4.1. List Standards

- **GET /standards**
  - **Query Params:** `profile`, `category` (e.g., "backend", "frontend", "global", "testing")
  - **Response:** List of standards (metadata, not content).

###### 4.2. Get/Update/Delete Standard

- **GET /standards/{category}/{standardName}?profile=default**
  - **Returns:** The Markdown content at `profiles/{profile}/standards/{category}/{standardName}.md`
- **POST /standards**  
  - **Body:**  
    ```json
    {
      "category": "backend",
      "name": "api",
      "profile": "default",
      "content": "# API Design Standard\n..."
    }
    ```
  - **Creates or updates** the file at `profiles/{profile}/standards/{category}/{name}.md`.
- **DELETE /standards/{category}/{standardName}?profile=default**
  - **Deletes** the file.

---

##### 5. Commands (Agentic Workflows, File-Backed)

###### 5.1. List Commands

- **GET /commands**
  - **Query Params:** `profile`, `mode` (multi-agent, single-agent)
  - **Returns:** List of commands with metadata.

###### 5.2. Command Content

- **GET /commands/{commandName}?mode=multi-agent&profile=default**
  - **Returns:** Markdown content at  
    `profiles/{profile}/commands/{commandName}/multi-agent/{commandName}.md`
- **GET /commands/{commandName}?mode=single-agent&profile=default**
  - **Returns:** Array of steps. Each entry:
    ```json
    {
      "step": 1,
      "filename": "1-create-spec.md",
      "content": "..."
    }
    ```
    Maps to  
    `profiles/{profile}/commands/{commandName}/single-agent/{stepNumber}-{stepName}.md`

###### 5.3. Create/Update Command

- **POST /commands**
  - For multi-agent:  
    ```json
    {
      "name": "create-spec",
      "mode": "multi-agent",
      "profile": "default",
      "content": "# Multi-agent workflow\n..."
    }
    ```
  - For single-agent:  
    ```json
    {
      "name": "create-spec",
      "mode": "single-agent",
      "profile": "default",
      "steps": [
        { "step": 1, "filename": "1-create-spec.md", "content": "..." },
        { "step": 2, "filename": "2-create-tasks-list.md", "content": "..." }
      ]
    }
    ```
  - **Writes** the corresponding Markdown file(s).

###### 5.4. Delete Command

- **DELETE /commands/{commandName}?mode=multi-agent&profile=default**
  - **Removes** the Markdown file(s).

---

##### 6. File Operations (Direct File Access)

###### 6.1. Download File

- **GET /files/{filePath}**
  - **Returns:** Raw file content for any path under the repo root (with access control).

###### 6.2. Upload/Write File

- **POST /files**
  - **Body:**  
    ```json
    {
      "path": "profiles/default/standards/backend/api.md",
      "content": "# New content"
    }
    ```
  - **Writes** to the file (creates if missing).

###### 6.3. Delete File

- **DELETE /files/{filePath}**

---

##### 7. Workflows (Agentic Orchestration)

###### 7.1. List Workflows

- **GET /workflows?profile=default**
  - Returns available workflows as defined in  
    `profiles/{profile}/workflows/` directory.

###### 7.2. Workflow Content

- **GET /workflows/{workflowName}?profile=default&phase=implementation**
  - Returns steps in the workflow as Markdown array (maps to  
    `profiles/{profile}/workflows/{phase}/{workflowName}.md`).

---

##### 8. Configuration

###### 8.1. Get/Set Config

- **GET /config?scope=base|project&projectId=...**
  - Returns YAML config content.
- **PUT /config**
  - Updates config YAML for base or project.

---

##### 9. Status, Validation, and Health

###### 9.1. System Status

- **GET /status**
  - Returns API and repo health info.

###### 9.2. Validate Installation

- **POST /install/validate**
  - **Body:**
    ```json
    {
      "project_path": "/path/to/project"
    }
    ```
  - Validates directory structure, required files, config, etc.

---

##### 10. Authentication & Users

###### 10.1. Login

- **POST /auth/login**
  - **Body:**  
    ```json
    {
      "username": "user",
      "password": "secret"
    }
    ```
  - **Response:** Bearer token

###### 10.2. Get Current User

- **GET /auth/me**

---

##### Error Handling

All errors return a JSON object:
```json
{
  "error": "Description",
  "code": 400
}
```

---

##### **File Mapping Principle**

- All content returned or modified by this API maps directly to Markdown or YAML files in the agent-os repository.
- The API performs no transformation: it exposes, lists, writes, and deletes files according to the directory conventions of agent-os.
- Any updates or new agentic workflows, standards, commands, or roles are reflected immediately in the underlying file system—and vice versa.

---

**This document is the canonical contract for all agentic API interactions with Agent OS. All endpoints and resource schemas are subject to file-based mapping, as described above.**


---

### INTEGRATED DOCUMENT: Claude Code Integration Policy (Version 2)
#### Claude Code Integration Policy for Agent OS

##### Overview

This policy governs how Agent OS exposes agentic workflows, standards, and templates to Claude Code and similar tools, ensuring consistent, robust, and user-friendly integration.  
It should be updated as conventions or technical needs evolve.

---

##### 1. Workflow Step Execution

- **Single-Agent Workflows**
  - All steps for a command are discovered and returned as an **ordered array**.
  - **Execution is sequential:** Claude Code (or any client) presents one step at a time, waits for completion/feedback, then proceeds to the next.
  - **Batch execution is discouraged** unless the use case is fully automated.

- **Multi-Agent Workflows**
  - Returned as a single Markdown prompt for the agent orchestrator.

---

##### 2. File Naming & Discovery

- **Single-Agent Steps**
  - File pattern: `{stepNumber}-{stepName}.md` (e.g., `1-create-spec.md`)
  - **Only** files matching `/^\d+-.*\.md$/` are treated as steps.
  - Steps are sorted numerically by `stepNumber`.
  - Gaps in numbering are allowed and handled gracefully (e.g., steps 1, 3, 5).

- **Multi-Agent**
  - Single file: `{commandName}.md` in the `multi-agent` subdirectory.

---

##### 3. Profile & Command Inheritance

- **Profile Inheritance**
  - If a profile inherits from a parent, any command in the child profile **overrides** (replaces) the parent version (all steps).
  - **No per-step fallback:** If the child has any steps for a command, *all* are taken from the child; parent steps are ignored.

---

##### 4. Standards and Template Expansion

- **Inclusion Syntax**
  - Use patterns like `{{standards/backend/*}}` in templates or prompts to automatically include all relevant standard Markdown files.
  - Expansion is performed in **lexicographical (alphabetical) order** of file names.
  - **Headers:** Each included standard should be preceded by a Markdown header indicating the filename (e.g., `## [Standard: coding-style.md]`).

- **Glob Behavior**
  - Single-level globs (`*`) are supported.
  - If recursive globs (`**/*`) are supported, document and test accordingly.

- **Conflicts**
  - If multiple standards provide the same guideline or heading, no deduplication is performed—content is concatenated as-is.
  - Consider warning users of duplicates.

---

##### 5. Template Recursion

- **Includes:** Templates may include other templates or standards by using inclusion syntax.
- **Recursion Limit:** Implement a reasonable max depth (e.g., 10) to prevent infinite loops.
- **Cycle Detection:** Track included files per expansion; abort with a clear error if a cycle is detected.

---

##### 6. Error Handling

- **Missing Step Files:** Omitted from result; optionally warn or include a `missing_steps` array in API responses.
- **Parse/Expansion Errors:** Return clear, structured error messages with file and step context.
- **Recursion/Include Errors:** Abort with an error if recursion depth is exceeded or a cycle is detected.

---

##### 7. Formatting & Markers

- **Markdown Only:** All prompts, standards, and templates must be valid Markdown.
- **No special hidden markers** unless documented; if frontmatter is used, specify supported keys.

---

##### 8. Testing and Validation

- **Test most common workflows:** `create-spec`, `implement-spec`, `plan-product`, `new-spec`.
- **Edge Case Testing:** Include cases for:
  - Missing or out-of-order step files
  - Circular template includes
  - Large standards expansions
  - Profile inheritance conflicts

---

##### 9. Performance

- **Caching:** Frequently accessed standards/templates should be cached in memory.
- **Concurrency:** Multiple workflows may run concurrently; implementation must use async/atomic file operations.

---

##### 10. Change Management

- **Policy updates must be versioned** and communicated to users and integrators.
- Major changes to conventions (file patterns, inheritance, expansion logic) require at least minor version bumps and migration notes.

---

##### Contact

For questions, integration support, or to propose changes to this policy, contact the Agent OS maintainers.

---

## Objectives

You are to produce a complete, actionable, and convention-aware analysis and documentation suite of the agent-os codebase, with a special focus on the integrated API-first, file-system-driven architecture and the Claude Code integration policy embedded above.  
Your outputs will directly inform both human maintainers and AI-based tools (like Claude Code) for future refactoring, documentation, extensibility, and integration.

---

## Core Requirements

### 1. **Scope and Coverage**

- Analyze all workflows, scripts, commands, standards, profiles, roles, configuration files, and integration points.
- Document both **explicit code/logic** and **implicit conventions**—how files and directories are used as the schema.
- Validate all findings against the file system: perform a recursive directory and file listing, and cross-check outputs for completeness.

### 2. **Structured Output**

#### **A. Main Output Files**

- Place all generated documentation, codemaps, and diagrams (as Markdown) in `/files/main/research/agent-os-codemap/`.
- Each logical output must be a separate, well-named Markdown file.
- Required outputs (all cross-linked and referenced):
  - `/roadmap.md` — High-level project overview and modernization roadmap
  - `/codemap.md` — Component/script map with Mermaid diagrams
  - `/workflows.md` — CI/CD and runtime workflow breakdowns (with visuals)
  - `/architecture.md` — Architecture diagrams and refactoring guidance
  - `/config.md` — Configuration/environment mapping
  - `/commands.md` — CLI and user-facing command index
  - `/refactoring-notes.md` — Action items and blockers for modernization
  - `/test-coverage.md` — (If applicable) Test and validation map

#### **B. Output Structure (Within Each File)**

- Start with an **executive summary**: key findings, conventions, and recommendations.
- Include **directory/file trees** (ASCII or Mermaid), **conventions tables**, and **explicit mapping between API endpoints and file system structure**.
- Use **tables** for step patterns, inheritance rules, glob syntax, and integration points.
- For diagrams, use **Mermaid** code blocks.

#### **C. Formatting and Linkage**

- All links are **relative** to the research folder.
- Cross-link between all output files where relevant.
- Reference the integrated API spec and Claude Code policy wherever API endpoints or integration flows are discussed.

#### **D. Additional Output Files**

- For each major area (`profiles`, `commands`, `standards`, `workflows`, etc.), generate a summary file in `/docs/analysis/` with:
  - Subdirectory trees
  - Convention details
  - Notable files/patterns
  - Potential issues, inconsistencies, and recommendations

---

## 3. **Analysis Tasks**

### **A. Workflows & Scripts**
- Generate a complete, recursive listing of `/scripts/`.
- For each script, analyze:
  - Type, entry points, main function
  - Call graph and dependencies
  - Workflow logic (init, update, uninstall, etc.)
  - Hardcoded paths/system assumptions
- Analyze `.github/workflows/*.yml` for pipelines, triggers, dependencies.

### **B. Workflow Dynamics & Visualization**
- Map interactions between scripts and workflows.
- Generate Mermaid diagrams:
  - Job dependencies (GitHub Actions)
  - Script call graphs
  - User/developer flows (install, update, uninstall)
- Map data/config/artifact flows.

### **C. Key Elements & Inventory**
- Identify all environment variables, external dependencies, CLI commands, deprecated/redundant logic.
- Highlight “init” vs. “update” vs. “uninstall” flows.
- Flag code tightly coupled to scripting.
- Review `profiles/` for roles/commands/integration points.
- Inspect `config.yml` for variables, defaults, and usage.

### **D. Architecture & Refactoring Assessment**
- Provide high-level architecture diagrams and module breakdowns.
- Analyze pain points and upgrade blockers.
- Identify areas for modularization or serverless/API migration.
- Outline blockers for migration and manual intervention.

### **E. Testing & Integration Points**
- List automated tests, coverage, gaps.
- Map integration points with external services/APIs.

---

## 4. **API and Convention Analysis (Enhanced)**

- Map API endpoints to file/directory conventions, as specified in the **embedded API Spec (above)**.
- For workflows, commands, standards, and profiles:
  - Document how API requests map to file reads/writes
  - For single-agent multi-step workflows, specify:
    - File naming pattern: `{stepNumber}-{stepName}.md` (strict, no variations unless documented)
    - Step numbering: sequential preferred, gaps allowed, handled gracefully
    - Step discovery: only files matching regex `/^\d+-.*\.md$/`
    - Step metadata: filename-based order is canonical; frontmatter optional
    - Error handling: missing steps skipped but flagged; non-step files ignored; out-of-order files sorted numerically
    - Performance: in-memory for typical file sizes, caching for standards/templates, async I/O, atomic writes for concurrency
    - Inheritance: child profile fully overrides parent for commands; no per-step fallback
    - Standards globs: single-level (`*`), recursive only if supported; expand in lex order; headers between includes
    - Template recursion: max depth (recommend 10); detect cycles; clear errors for infinite loops
    - Claude Code integration: sequential, step-by-step execution; no special markers unless documented
- Cross-link findings to the **embedded Claude Code Integration Policy**.

---

## 5. **Completeness and Fallbacks**

- If any resource constraint (rate limit, API error) occurs, pause and resume as needed.
- Do not skip any directories/files/analysis steps due to temporary constraints.
- Log and revisit deferred steps.
- If a constraint cannot be worked around, document it in the output for manual review.

---

## 6. **Completeness Guarantee**

Before declaring the analysis and documentation complete:
- Systematically review all analysis tasks and outputs.
- Cross-check the actual repository contents (recursive listing) against outputs.
- Ensure every script, workflow, config, profile, and documentation section is analyzed and outputted.
- Cross-link all documentation and codemaps.
- Only consider the task complete when every checklist item is satisfied and all outputs are actionable and cross-linked.

---

## 7. **Purpose, Extensibility, and User Focus**

- All outputs must be clear, actionable, and structured for both immediate human understanding and AI-driven refactoring/modernization.
- Highlight extensibility points, plugin/hook opportunities, and integration best practices based on the **embedded API Spec** and **Claude Code Integration Policy**.
- Explicitly document any areas of uncertainty, ambiguity, or open questions, and suggest next steps or escalation paths.

---

**Be explicit, convention-driven, and exhaustive.  
Tailor all findings and recommendations to support both human maintainers and AI agents (like Claude Code), maximizing clarity, extensibility, and future-proofing.**

---
