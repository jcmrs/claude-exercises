# Scripts Specification & Analysis

## Executive Summary
Describes the structure, conventions, and orchestration of scripts (autonomous task sequences or workflows) in Agent OS.

---

## Inventory & Directory Structure

- `scripts/<profile>/<script>.md`: Script definitions (YAML frontmatter + Markdown).
- `scripts/<profile>/<script>/<n>-<step>.md`: Step files for sequential scripts.

---

## Naming Conventions

- Script filenames: `^[a-z0-9-]+\.md$` (e.g., `data-cleanup.md`)
- Step files: `^\d+-[a-z0-9-]+\.md$` (e.g., `01-fetch-data.md`)
- Steps must be sequentially numbered, zero-padded if >9.

---

## API Mappings

- `GET /scripts/<id>?profile=default`
- `GET /scripts/<id>?mode=stepwise` (stepwise execution)
- `GET /scripts/<id>/<step>` (retrieve step)

---

## Validation & Error Handling

- Validate script and step file names, metadata, and sequence.
- Reject duplicate, missing, or out-of-sequence steps.
- Return 400 for filename/metadata errors, 404 for missing script/step.

---

## Testing Recommendations

- Test script enumeration, step retrieval, and invalid step files.
- Simulate sequential execution and edge cases.

---

## Recommendations

- Use step files for complex, multi-step automations.
- Document all fields, steps, and metadata in [examples.md](../examples.md).

---