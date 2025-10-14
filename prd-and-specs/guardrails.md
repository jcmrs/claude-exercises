# Guardrails for Claude Code Modernization

This document establishes strict guardrails and best practices to ensure safety, consistency, and reliability during all AI-guided automation, refactoring, and development work involving Claude Code or similar AI agents.

---

## 1. File & Naming Conventions

- **Enforced Patterns:**  
  All files (agents, commands, steps, standards, roles) must follow the naming and directory conventions defined in [API Conventions](./specifications/api-conventions.md).
- **Regex Validation:**  
  Filenames are validated using regex patterns. Invalid files are ignored or rejected by the API.

## 2. API-First Principle

- **No Direct Filesystem Access:**  
  Claude Code must interact with project data *only* via the API endpoints. Direct filesystem parsing is prohibited.
- **Step Discovery:**  
  Single-agent steps are always returned as ordered arrays from the API. Never discovered by listing files manually.

## 3. Execution & Orchestration

- **Sequential Processing:**  
  For single-agent commands, each step must be executed and confirmed in sequence. Batch or out-of-order processing is not allowed.
- **Multi-agent Mode:**  
  Orchestration files are treated as atomic prompts; sub-agent delegation must follow the process described in the command or workflow file.

## 4. Validation & Error Handling

- **Strict Validation:**  
  - File naming, YAML/Markdown syntax, and required metadata must pass validation checks.
  - Any validation error should trigger an immediate halt and explicit error reporting.
- **Error Codes:**  
  All errors must be mapped to explicit HTTP response codes as described in [API Conventions](./specifications/api-conventions.md#error-handling).

## 5. Template Expansion & Circularity

- **Template Guards:**  
  - All template variables and glob patterns must resolve to valid, existing files.
  - Circular references and recursion beyond 10 levels are forbidden and must result in errors.
- **Header Injection:**  
  All expanded inclusions must inject source headers as per convention.

## 6. Profile Inheritance

- **Override Rules:**  
  - Child profiles fully override parent commands, agents, standards, or workflows at the file level.
  - No partial or per-step fallback is allowed for commands.
  - Standards and workflows fallback only at the file level.

## 7. Testing & Verification

- **Pre-Deployment Checks:**  
  All new or modified agents, commands, roles, or standards must pass the recommended unit/integration tests before being used in production or automation.
- **Edge Cases:**  
  Scenarios such as duplicate steps, missing templates, or circular includes must be explicitly tested.

## 8. Prompt and Response Formatting

- **Prompts:**  
  All prompts to Claude Code must follow the prescribed format, referencing commands, steps, and agents by their canonical API names/IDs.
- **Responses:**  
  All API responses must provide structured, parseable data (e.g., JSON with metadata) rather than freeform text.

## 9. Documentation and Change Logging

- **Documentation:**  
  Any change to conventions, API, or workflow patterns must be reflected in the specification and analysis docs before deployment.
- **Versioning:**  
  All changes must be logged with version history in the relevant Markdown files.

---

## References

- [api-conventions.md](./specifications/api-conventions.md)
- [agents-analysis.md](./specifications/agents-analysis.md)
- [commands-analysis.md](./specifications/commands-analysis.md)
- [profiles-analysis.md](./specifications/profiles-analysis.md)
- [standards-analysis.md](./specifications/standards-analysis.md)
- [workflows-analysis.md](./specifications/workflows-analysis.md)