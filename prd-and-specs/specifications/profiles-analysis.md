# Profiles Specification & Analysis

## Executive Summary
Describes user and system profiles, their structure, inheritance, and how they integrate with agents and commands in Agent OS.

---

## Inventory & Directory Structure

- `profiles/<profile>.md`: User/system profile definitions (YAML frontmatter + Markdown).
- `profiles/default.md`: The base profile from which others inherit.

---

## Naming Conventions

- Profile filenames: `^[a-z0-9-]+\.md$` (e.g., `developer.md`)
- Metadata keys: `name`, `description`, `inherits`, `settings`, `permissions`

---

## API Mappings

- `GET /profiles` (list all profiles)
- `GET /profiles/<id>` (retrieve a profile)
- `POST /profiles` (create new profile)
- `PUT /profiles/<id>` (update)

---

## Validation & Error Handling

- Profiles must include required metadata and valid inheritance structure.
- Duplicate profile names or circular inheritance is rejected.
- Return 400 for metadata errors, 404 for not found.

---

## Testing Recommendations

- Create profiles with/without inheritance.
- Verify access to inherited/default fields.
- Simulate permission escalations and validation failures.

---

## Recommendations

- Use base profile for common settings.
- Always validate inheritance and permissions before execution.
- Document all fields and settings in [examples.md](../examples.md).

---