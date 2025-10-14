# Workflows Specification & Analysis

## Executive Summary
Describes the structure, orchestration, and conventions for workflows that coordinate agents, commands, scripts, and roles in Agent OS.

---

## Inventory & Directory Structure

- `workflows/<profile>/<workflow>.md`: Workflow definitions (YAML frontmatter + Markdown).
- `workflows/<profile>/<workflow>/<n>-<step>.md`: Step files for sequential or multi-agent workflows.

---

## Naming Conventions

- Workflow filenames: `^[a-z0-9-]+\.md$` (e.g., `project-launch.md`)
- Step files: `^\d+-[a-z0-9-]+\.md$` (e.g., `01-initiate.md`)
- Steps must be sequentially numbered, zero-padded if >9.

---

## API Mappings

- `GET /workflows/<id>?profile=default`
- `GET /workflows/<id>?mode=stepwise` (stepwise execution)
- `GET /workflows/<id>/<step>` (retrieve step)

---

## Validation & Error Handling

- Validate workflow and step file names, metadata, and sequence.
- Reject duplicate, missing, or out-of-sequence steps.
- Return 400 for filename/metadata errors, 404 for missing workflow/step.

---

## Testing Recommendations

- Test workflow enumeration, step retrieval, and invalid step files.
- Simulate sequential and parallel execution scenarios.
- Validate integration with agents, commands, and scripts.

---

## Recommendations

- Use step files for complex, multi-agent, or multi-stage workflows.
- Document all fields, steps, and orchestration logic in [examples.md](../examples.md).

---