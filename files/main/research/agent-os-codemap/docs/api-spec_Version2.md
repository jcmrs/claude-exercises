# Agent OS API Specification (Preliminary v1.0)

## Overview

This API provides programmatic access to all major operations in Agent OS, enabling installation, project management, configuration, profile/role/agent management, standards and command handling, workflow orchestration, file operations, and integration with external tools.

**Design Principle:**  
The API is a thin, file-based interface: all "agentic" content (commands, standards, roles, workflows, agent definitions) is governed by a structured directory of Markdown/YAML files. The API simply maps HTTP endpoints to read, write, list, and delete operations on these files, preserving full compatibility and auditability with the agent-os repository model.

---

## Authentication

All endpoints require authentication via a bearer token.

- Header: `Authorization: Bearer <token>`

---

## 1. Installation

### 1.1. Base Installation

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

### 1.2. Project Installation

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

### 1.3. Project Update

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

## 2. Project Management

### 2.1. List Projects

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

### 2.2. Create Project

- **POST /projects**
  - **Body:**  
    ```json
    {
      "name": "project1",
      "parent_path": "/agent-os-projects"
    }
    ```
  - **Responses:** 201 Created, 409 Conflict

### 2.3. Delete Project

- **DELETE /projects/{projectId}**
  - **Responses:** 204 No Content, 404 Not Found

---

## 3. Profiles & Roles

### 3.1. Profiles

- **GET /profiles**  
  List all available profiles (directory names under `/profiles`).
- **POST /profiles**  
  Create or update a profile (creates a folder and config file).
- **GET /profiles/{profileName}**  
  Get details and file structure for a profile.
- **DELETE /profiles/{profileName}**  
  Delete a profile and all its files.

### 3.2. Roles

- **GET /profiles/{profileName}/roles**  
  List all roles for a profile (reads `roles/*.yml`).
- **POST /profiles/{profileName}/roles**  
  Create or update a role's YAML file.
- **GET /profiles/{profileName}/roles/{roleId}**  
  Get a specific role's YAML.
- **DELETE /profiles/{profileName}/roles/{roleId}**  
  Delete a role definition.

---

## 4. Standards (File-Backed)

### 4.1. List Standards

- **GET /standards**
  - **Query Params:** `profile`, `category` (e.g., "backend", "frontend", "global", "testing")
  - **Response:** List of standards (metadata, not content).

### 4.2. Get/Update/Delete Standard

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

## 5. Commands (Agentic Workflows, File-Backed)

### 5.1. List Commands

- **GET /commands**
  - **Query Params:** `profile`, `mode` (multi-agent, single-agent)
  - **Returns:** List of commands with metadata.

### 5.2. Command Content

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

### 5.3. Create/Update Command

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

### 5.4. Delete Command

- **DELETE /commands/{commandName}?mode=multi-agent&profile=default**
  - **Removes** the Markdown file(s).

---

## 6. File Operations (Direct File Access)

### 6.1. Download File

- **GET /files/{filePath}**
  - **Returns:** Raw file content for any path under the repo root (with access control).

### 6.2. Upload/Write File

- **POST /files**
  - **Body:**  
    ```json
    {
      "path": "profiles/default/standards/backend/api.md",
      "content": "# New content"
    }
    ```
  - **Writes** to the file (creates if missing).

### 6.3. Delete File

- **DELETE /files/{filePath}**

---

## 7. Workflows (Agentic Orchestration)

### 7.1. List Workflows

- **GET /workflows?profile=default**
  - Returns available workflows as defined in  
    `profiles/{profile}/workflows/` directory.

### 7.2. Workflow Content

- **GET /workflows/{workflowName}?profile=default&phase=implementation**
  - Returns steps in the workflow as Markdown array (maps to  
    `profiles/{profile}/workflows/{phase}/{workflowName}.md`).

---

## 8. Configuration

### 8.1. Get/Set Config

- **GET /config?scope=base|project&projectId=...**
  - Returns YAML config content.
- **PUT /config**
  - Updates config YAML for base or project.

---

## 9. Status, Validation, and Health

### 9.1. System Status

- **GET /status**
  - Returns API and repo health info.

### 9.2. Validate Installation

- **POST /install/validate**
  - **Body:**
    ```json
    {
      "project_path": "/path/to/project"
    }
    ```
  - Validates directory structure, required files, config, etc.

---

## 10. Authentication & Users

### 10.1. Login

- **POST /auth/login**
  - **Body:**  
    ```json
    {
      "username": "user",
      "password": "secret"
    }
    ```
  - **Response:** Bearer token

### 10.2. Get Current User

- **GET /auth/me**

---

## Error Handling

All errors return a JSON object:
```json
{
  "error": "Description",
  "code": 400
}
```

---

## **File Mapping Principle**

- All content returned or modified by this API maps directly to Markdown or YAML files in the agent-os repository.
- The API performs no transformation: it exposes, lists, writes, and deletes files according to the directory conventions of agent-os.
- Any updates or new agentic workflows, standards, commands, or roles are reflected immediately in the underlying file system—and vice versa.

---

**This document is the canonical contract for all agentic API interactions with Agent OS. All endpoints and resource schemas are subject to file-based mapping, as described above.**
