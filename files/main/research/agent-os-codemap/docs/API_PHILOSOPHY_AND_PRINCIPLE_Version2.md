# Agent OS API Philosophy & Principle

## Introduction

The Agent OS API is intentionally designed to be as **simple, transparent, and extensible as possible**.  
Its power comes not from complex backend logic, but from faithfully exposing the underlying file system and conventions that define the agentic workflows, standards, roles, and profiles.  
This philosophy ensures that the API is robust, flexible, and easy to integrate with any tool or platform.

---

## 1. The API as a Convention-Driven File System

- **Source of Truth:**  
  All agentic content—commands, standards, roles, workflows, and agent definitions—are represented as files (Markdown or YAML) in a structured directory hierarchy.
- **API Role:**  
  The API exposes these files and directories as RESTful resources, mapping HTTP operations directly to file system operations (read, write, list, delete).
- **No Hidden State:**  
  There is no hidden database or secondary state. The file system itself is the canonical source.

---

## 2. Convention Over Configuration

- **Structure is Schema:**  
  The organization and naming of files and directories *is* the schema. Changing conventions is as simple as changing directory structure or file naming patterns.
- **Documentation is Key:**  
  The API is accompanied by detailed documentation describing the directory conventions, file formats, and naming patterns.

---

## 3. API Endpoints as Smart Helpers

- **Primitive Endpoints:**  
  - `GET /files/{path}`: Download any file
  - `PUT /files/{path}`: Upload or update any file
  - `DELETE /files/{path}`: Delete any file
  - `GET /directories/{path}`: List files/subdirectories
- **Convention-Aware Endpoints:**  
  Higher-level endpoints (like `/commands`, `/standards`, `/workflows`) are *convenience wrappers* that understand the conventions and return structured results accordingly.

---

## 4. Simplicity = Power

- **Immediate Extensibility:**  
  New workflows, standards, or agentic features are available as soon as new files/directories are added—no API changes required.
- **Universal Compatibility:**  
  Any tool (CLI, web UI, LLM agent, IDE plugin) can interact with Agent OS simply by reading/writing files via the API.
- **Transparent Evolution:**  
  Schema changes and migrations are visible and auditable via the file system (and Git, if versioned).

---

## 5. Design Implications

- **Don’t Over-Engineer:**  
  Avoid unnecessary layers of abstraction. The API should reflect the file system, not duplicate or obscure it.
- **Favor Documentation Over Code:**  
  Document the conventions and let clients interpret them. The API provides structured access, not business logic.
- **Empower Users & Tools:**  
  Make it easy for users to add, update, or extend workflows, standards, and profiles by simply managing files.

---

## 6. Summary Statement

> **The Agent OS API is a remote, convention-driven file system.  
> Its simplicity is its greatest strength, enabling infinite extensibility, transparency, and ease of integration.**

---

## Further Reading

- [Agent OS API Specification](api-spec.md)
- [Claude Code Integration Policy](CLAUDE_CODE_INTEGRATION_POLICY.md)
- [Versioning and Migration Policy](to-be-created)
