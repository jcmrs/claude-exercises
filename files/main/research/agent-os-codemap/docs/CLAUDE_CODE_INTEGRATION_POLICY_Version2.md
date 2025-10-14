# Claude Code Integration Policy for Agent OS

## Overview

This policy governs how Agent OS exposes agentic workflows, standards, and templates to Claude Code and similar tools, ensuring consistent, robust, and user-friendly integration.  
It should be updated as conventions or technical needs evolve.

---

## 1. Workflow Step Execution

- **Single-Agent Workflows**
  - All steps for a command are discovered and returned as an **ordered array**.
  - **Execution is sequential:** Claude Code (or any client) presents one step at a time, waits for completion/feedback, then proceeds to the next.
  - **Batch execution is discouraged** unless the use case is fully automated.

- **Multi-Agent Workflows**
  - Returned as a single Markdown prompt for the agent orchestrator.

---

## 2. File Naming & Discovery

- **Single-Agent Steps**
  - File pattern: `{stepNumber}-{stepName}.md` (e.g., `1-create-spec.md`)
  - **Only** files matching `/^\d+-.*\.md$/` are treated as steps.
  - Steps are sorted numerically by `stepNumber`.
  - Gaps in numbering are allowed and handled gracefully (e.g., steps 1, 3, 5).

- **Multi-Agent**
  - Single file: `{commandName}.md` in the `multi-agent` subdirectory.

---

## 3. Profile & Command Inheritance

- **Profile Inheritance**
  - If a profile inherits from a parent, any command in the child profile **overrides** (replaces) the parent version (all steps).
  - **No per-step fallback:** If the child has any steps for a command, *all* are taken from the child; parent steps are ignored.

---

## 4. Standards and Template Expansion

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

## 5. Template Recursion

- **Includes:** Templates may include other templates or standards by using inclusion syntax.
- **Recursion Limit:** Implement a reasonable max depth (e.g., 10) to prevent infinite loops.
- **Cycle Detection:** Track included files per expansion; abort with a clear error if a cycle is detected.

---

## 6. Error Handling

- **Missing Step Files:** Omitted from result; optionally warn or include a `missing_steps` array in API responses.
- **Parse/Expansion Errors:** Return clear, structured error messages with file and step context.
- **Recursion/Include Errors:** Abort with an error if recursion depth is exceeded or a cycle is detected.

---

## 7. Formatting & Markers

- **Markdown Only:** All prompts, standards, and templates must be valid Markdown.
- **No special hidden markers** unless documented; if frontmatter is used, specify supported keys.

---

## 8. Testing and Validation

- **Test most common workflows:** `create-spec`, `implement-spec`, `plan-product`, `new-spec`.
- **Edge Case Testing:** Include cases for:
  - Missing or out-of-order step files
  - Circular template includes
  - Large standards expansions
  - Profile inheritance conflicts

---

## 9. Performance

- **Caching:** Frequently accessed standards/templates should be cached in memory.
- **Concurrency:** Multiple workflows may run concurrently; implementation must use async/atomic file operations.

---

## 10. Change Management

- **Policy updates must be versioned** and communicated to users and integrators.
- Major changes to conventions (file patterns, inheritance, expansion logic) require at least minor version bumps and migration notes.

---

## Contact

For questions, integration support, or to propose changes to this policy, contact the Agent OS maintainers.
