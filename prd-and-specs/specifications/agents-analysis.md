# Agents Specification & Analysis

## Executive Summary
Describes agent structure, metadata, inheritance, compilation, and orchestration for Agent OS.

---

## Inventory & Directory Structure

- `agents/<profile>/<agent>.md`: Single agent file (YAML frontmatter + Markdown).
- `agents/roles/<role>.md`: Role templates for compilation.
- All agent files support metadata in YAML frontmatter.

---

## Naming Conventions

- Agent filenames: `^[a-z0-9-]+\.md$` (e.g., `spec-writer.md`)
- Role templates: `^[a-z0-9-]+\.md$` in `roles/`
- Metadata keys: `name`, `description`, `tools`, `color`, `model`, `path`

---

## API Mappings

- `GET /agents?profile=default`
- `POST /agents/compile` (compile from role)
- `GET /agents/<id>` (retrieve agent)

---

## Validation & Error Handling

- Metadata and filename are required and validated.
- Compilation must fail gracefully if template or role is missing/invalid.
- Return 400/404 on error with details.

---

## Testing Recommendations

- Create, compile, and retrieve agents using valid/invalid metadata/roles.
- Test fallback to inherited/default fields.

---

## Recommendations

- Use role templates for rapid agent onboarding.
- Always validate agent files before orchestration or execution.
- Document all fields in [examples.md](../examples.md).

---