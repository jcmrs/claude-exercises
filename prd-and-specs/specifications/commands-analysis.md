# Commands Specification & Analysis

## Executive Summary
Defines structure and orchestration for commands, step files, and multi-agent command files.

---

## Inventory & Directory Structure

- `commands/<profile>/<command>.md` (multi-agent orchestration)
- `commands/<profile>/<command>/<n>-<step>.md` (single-agent, numbered steps)

---

## Naming Conventions

- Multi-agent: `^[a-z0-9-]+\.md$`
- Step: `^\d+-[a-z0-9-]+\.md$`
- Steps must be sequentially numbered, zero-padded if >9 steps.

---

## API Mappings

- `GET /commands/<id>?profile=default`
- `GET /commands/<id>?mode=single-agent&profile=default` (stepwise)
- `GET /commands/<id>/<step>` (individual step)

---

## Validation & Error Handling

- Validate step sequence, filename, and YAML/Markdown syntax.
- Reject duplicate, missing, or out-of-sequence steps.
- Errors: 400 for filename, 404 for missing command/step.

---

## Testing Recommendations

- Test command enumeration, step retrieval, and invalid step files.
- Simulate sequential execution and edge cases.

---

## Recommendations

- Use single-agent step files for sequential automation.
- Multi-agent orchestration files for batch/parallel agent workflows.
- Validate step order strictly before execution.

---