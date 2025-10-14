# Roles Specification & Analysis

## Executive Summary
Defines role templates, inheritance, and their use in agent and workflow compilation.

---

## Inventory & Directory Structure

- `roles/<role>.md`: Role template files for agent/workflow compilation.

---

## Naming Conventions

- Role template filenames: `^[a-z0-9-]+\.md$` (e.g., `researcher.md`)
- Metadata keys: `name`, `description`, `inherits`, `fields`, `permissions`

---

## API Mappings

- `GET /roles` (list all roles)
- `GET /roles/<id>` (retrieve a role)
- `POST /roles` (create new role)
- `PUT /roles/<id>` (update)

---

## Validation & Error Handling

- Roles must include required metadata and valid inheritance structure.
- Detect and reject duplicate or circular inheritance.
- Return 400 for metadata errors, 404 for not found.

---

## Testing Recommendations

- Create roles with/without inheritance.
- Attempt to compile agents/workflows from role templates.
- Simulate invalid inheritance or missing fields.

---

## Recommendations

- Use role templates for consistency and scale.
- Validate inheritance for all roles.
- Document all fields and permissions in [examples.md](../examples.md).

---