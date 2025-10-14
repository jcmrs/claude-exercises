# Agent OS: Standards Organization and API Mapping Analysis

## Executive Summary

This document provides comprehensive analysis of the Agent OS standards system, including directory organization, file naming conventions, API mappings, and glob expansion patterns. Standards define coding practices, architectural patterns, and quality guidelines that AI agents must follow.

**Key Findings:**
- 15 standards files organized across 4 categories
- Category-based directory structure enables targeted glob patterns
- Standards are pure Markdown files (no frontmatter required)
- Glob expansion follows lexicographical ordering with header injection

---

## Standards Inventory

### Standards by Category

| Category | Files | Purpose | Role Usage |
|----------|-------|---------|------------|
| `backend/` | 4 | Backend development patterns (API, DB, queries) | Database Engineer, API Engineer |
| `frontend/` | 4 | Frontend development patterns (UI, accessibility) | UI Designer, Frontend Engineer |
| `global/` | 6 | Universal standards (coding style, conventions) | All roles |
| `testing/` | 1 | Testing practices and patterns | Testing Engineer, Verifiers |
| **Total** | **15** | Complete standards library | All agents |

### Complete File Listing

```
profiles/default/standards/
├── backend/
│   ├── api.md              # API endpoint design & REST conventions
│   ├── migrations.md       # Database migration patterns
│   ├── models.md           # Model structure & relationships
│   └── queries.md          # Query optimization & patterns
├── frontend/
│   ├── accessibility.md    # WCAG compliance & a11y patterns
│   ├── components.md       # Component design principles
│   ├── css.md              # CSS/styling conventions
│   └── responsive.md       # Responsive design patterns
├── global/
│   ├── coding-style.md     # General coding conventions
│   ├── commenting.md       # Documentation & comment standards
│   ├── conventions.md      # Naming & organizational conventions
│   ├── error-handling.md   # Error handling patterns
│   ├── tech-stack.md       # Technology choices & rationale
│   └── validation.md       # Input validation standards
└── testing/
    └── test-writing.md     # Test structure & patterns
```

---

## Directory Structure

### Category Organization

**Pattern:** `profiles/{profile}/standards/{category}/{standard-name}.md`

**Categories:**
1. **backend/** - Server-side, database, and API standards
2. **frontend/** - Client-side, UI, and UX standards
3. **global/** - Universal standards applicable to all code
4. **testing/** - Testing methodology and practices

### File Count by Category

```
backend/   ████ 4 files
frontend/  ████ 4 files
global/    ██████ 6 files
testing/   █ 1 file
```

---

## File Naming Conventions

### Standard Files

**Pattern:** `{descriptive-name}.md`

**Rules:**
- Lowercase only
- Hyphen-separated words
- Descriptive, not abbreviated
- No version numbers in filename
- `.md` extension required

**Valid Examples:**
```
api.md
coding-style.md
error-handling.md
test-writing.md
component-patterns.md
```

**Invalid Examples (Avoid):**
```
API.md              # Not lowercase
api_design.md       # Underscore instead of hyphen
api-v2.md           # Version in filename
APIDesign.md        # CamelCase
api                 # No extension
```

### Category Names

**Rules:**
- Lowercase, singular or plural
- Descriptive of content domain
- No special characters except hyphen

**Current Categories:**
- `backend` ✅
- `frontend` ✅
- `global` ✅
- `testing` ✅

**Extensible:** New categories can be added as needed (e.g., `devops`, `security`, `mobile`)

---

## API Endpoint Mappings

### List Standards

**Endpoint:** `GET /standards?profile={profile}&category={category}`

**Query Parameters:**
- `profile` (optional): Profile name, defaults to "default"
- `category` (optional): Filter by category ("backend", "frontend", "global", "testing")

**File System Action:**
- Without category: List all `.md` files in `profiles/{profile}/standards/*/`
- With category: List all `.md` files in `profiles/{profile}/standards/{category}/`

**Response Example:**
```json
[
  {
    "name": "api",
    "category": "backend",
    "profile": "default",
    "path": "standards/backend/api.md",
    "size": 1024
  },
  {
    "name": "coding-style",
    "category": "global",
    "profile": "default",
    "path": "standards/global/coding-style.md",
    "size": 2048
  }
]
```

### Get Standard Content

**Endpoint:** `GET /standards/{category}/{standardName}?profile={profile}`

**File System Mapping:**
```
URL: /standards/backend/api?profile=default
→ File: profiles/default/standards/backend/api.md
```

**Response:**
```json
{
  "name": "api",
  "category": "backend",
  "profile": "default",
  "content": "# API Design Standards\n\n..."
}
```

### Create/Update Standard

**Endpoint:** `POST /standards`

**Request Body:**
```json
{
  "category": "backend",
  "name": "api",
  "profile": "default",
  "content": "# API Design Standard\n\n..."
}
```

**File System Action:**
- Write to: `profiles/{profile}/standards/{category}/{name}.md`
- Create category directory if missing
- Overwrite existing file

### Delete Standard

**Endpoint:** `DELETE /standards/{category}/{standardName}?profile={profile}`

**File System Action:**
- Delete file: `profiles/{profile}/standards/{category}/{name}.md`
- Optionally remove empty category directory

---

## Glob Pattern Expansion

### Supported Patterns

| Pattern | Expansion | Example Files Matched |
|---------|-----------|----------------------|
| `standards/*` | All standards (all categories) | All 15 files |
| `standards/global/*` | All global standards | 6 files in global/ |
| `standards/backend/*` | All backend standards | 4 files in backend/ |
| `standards/frontend/*` | All frontend standards | 4 files in frontend/ |
| `standards/testing/*` | All testing standards | 1 file in testing/ |

### Single-Level Glob Behavior

**Pattern:** `{category}/*`

**Rules:**
- Matches all `.md` files in the specified category directory
- Does NOT recurse into subdirectories (if any exist)
- Returns files in lexicographical (alphabetical) order

**Example:**
```
Pattern: standards/global/*

Matched (in order):
1. coding-style.md
2. commenting.md
3. conventions.md
4. error-handling.md
5. tech-stack.md
6. validation.md
```

### Multi-Category Glob (Wildcard Category)

**Pattern:** `standards/*`

**Expansion Order:**
1. Files sorted by category name (alphabetical)
2. Within each category, files sorted alphabetically

**Example:**
```
Pattern: standards/*

Expanded Order:
1. backend/api.md
2. backend/migrations.md
3. backend/models.md
4. backend/queries.md
5. frontend/accessibility.md
6. frontend/components.md
7. frontend/css.md
8. frontend/responsive.md
9. global/coding-style.md
10. global/commenting.md
11. global/conventions.md
12. global/error-handling.md
13. global/tech-stack.md
14. global/validation.md
15. testing/test-writing.md
```

### Recursive Glob (Future Support)

**Pattern:** `standards/**/*` (if supported)

**Behavior:**
- Would match all files recursively
- Includes files in subdirectories
- Ordering: depth-first or breadth-first (implementation-defined)

**Current Status:** ❌ Not implemented (standards use flat structure)

---

## Template Expansion in Commands/Agents

### Inclusion Syntax

**In Command/Agent Files:**
```markdown
Apply the following standards:

{{standards/global/*}}
```

**After Expansion:**
```markdown
Apply the following standards:

## [Standard: coding-style.md]
# Coding Style Standards

[Content of coding-style.md]

## [Standard: commenting.md]
# Commenting Standards

[Content of commenting.md]

## [Standard: conventions.md]
# Conventions

[Content of conventions.md]
```

### Header Injection

**Rule:** Each included standard is preceded by a header indicating the filename.

**Header Format:** `## [Standard: {filename}]`

**Purpose:**
- Provides context for the included content
- Helps AI agents identify which standard applies to each section
- Enables debugging of template expansions

### Expansion Process

1. **Parse Template:** Identify `{{standards/{pattern}}}` markers
2. **Resolve Glob:** Match files based on pattern
3. **Sort Files:** Lexicographical order
4. **Read Content:** Load each matched file
5. **Inject Headers:** Prepend header before each file's content
6. **Concatenate:** Combine all content in order
7. **Replace Marker:** Substitute marker with concatenated content

---

## Role-Based Standards Assignment

### Role Configuration

In `roles/*.yml` files, standards are assigned using glob patterns:

**Example (from `implementers.yml`):**
```yaml
implementers:
  - id: database-engineer
    standards:
      - global/*      # All global standards
      - backend/*     # All backend standards
      - testing/*     # All testing standards
      
  - id: api-engineer
    standards:
      - global/*
      - backend/*
      - testing/*
      
  - id: ui-designer
    standards:
      - global/*
      - frontend/*    # All frontend standards
      - testing/*
```

### Standards Assignment Matrix

| Role | Global | Backend | Frontend | Testing | Total Standards |
|------|--------|---------|----------|---------|-----------------|
| Database Engineer | ✅ (6) | ✅ (4) | ❌ | ✅ (1) | 11 |
| API Engineer | ✅ (6) | ✅ (4) | ❌ | ✅ (1) | 11 |
| UI Designer | ✅ (6) | ❌ | ✅ (4) | ✅ (1) | 11 |
| Testing Engineer | ✅ (6) | ✅ (4) | ✅ (4) | ✅ (1) | 15 |
| Verifiers | ✅ (6) | ✅ (4) | ✅ (4) | ✅ (1) | 15 |

---

## Standards Content Structure

### Recommended Structure

While standards are free-form Markdown, a recommended structure is:

```markdown
# {Standard Name}

## Overview
Brief description of what this standard covers.

## Principles
Core principles and rationale.

## Rules
Specific, actionable rules.

## Examples
Code examples demonstrating correct usage.

## Anti-Patterns
Common mistakes to avoid.

## References
Links to external resources or related standards.
```

### Content Guidelines

**Do:**
- ✅ Use clear, concise language
- ✅ Provide concrete examples
- ✅ Explain the "why" behind rules
- ✅ Use consistent formatting
- ✅ Keep standards focused and cohesive

**Don't:**
- ❌ Mix multiple unrelated topics in one standard
- ❌ Use vague or ambiguous language
- ❌ Assume prior knowledge without explanation
- ❌ Include outdated or deprecated practices
- ❌ Conflict with other standards

---

## Profile Inheritance

### Standards Resolution

When a profile inherits from a parent:

**Rule:** Child standards override parent standards by filename.

**Example:**
```
profiles/
├── base/
│   └── standards/
│       └── global/
│           ├── coding-style.md    # Parent version
│           └── commenting.md      # Parent version
└── custom/
    └── standards/
        └── global/
            └── coding-style.md    # Child version (overrides)
```

**Resolution for `{{standards/global/*}}`:**
1. `custom/standards/global/coding-style.md` (from child)
2. `base/standards/global/commenting.md` (from parent, not overridden)

### Cross-Profile References

**Not Currently Supported:** Standards cannot reference parent profile explicitly.

**Workaround:** Copy desired parent standards to child profile.

**Future Enhancement:** Support syntax like `{{../base/standards/global/*}}`

---

## Validation and Quality Control

### Validation Checks

| Check | Description | Enforcement |
|-------|-------------|-------------|
| File extension | Must be `.md` | ✅ Required |
| Filename format | Lowercase, hyphen-separated | ⚠️ Recommended |
| Valid Markdown | Parseable Markdown syntax | ⚠️ Recommended |
| No conflicts | Standards don't contradict each other | ⚠️ Manual review |
| Appropriate category | Standard in correct category | ⚠️ Manual review |

### Conflict Detection

**Challenge:** Standards can provide conflicting guidance.

**Example Conflict:**
- `global/coding-style.md`: "Use 2 spaces for indentation"
- `backend/api.md`: "Use 4 spaces for indentation"

**Current Approach:** No automatic detection; rely on manual review.

**Recommendation:** Implement lint tool to detect common conflicts.

---

## Performance Considerations

### Caching Strategy

**In-Memory Cache:**
- Cache standards content by profile and category
- Invalidate on file system changes
- Cache compiled template expansions

**Cache Key Example:**
```
Key: standards:{profile}:{category}:{filename}
Value: File content (Markdown)
TTL: Until file modified
```

### Optimization

**For Glob Patterns:**
- Pre-compute glob expansions during installation
- Store expansion results in index file
- Lazy-load individual standards on demand

**For API Requests:**
- Return metadata (list) quickly
- Stream large standards content
- Use ETags for client-side caching

---

## Error Handling

### Missing Standards

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| Category doesn't exist | 404 | `{"error": "Category not found"}` | Return error |
| Standard doesn't exist | 404 | `{"error": "Standard not found"}` | Return error |
| Profile doesn't exist | 404 | `{"error": "Profile not found"}` | Return error |
| Empty category | 200 | `[]` | Return empty array |

### Glob Expansion Errors

| Scenario | HTTP Status | Response | Behavior |
|----------|-------------|----------|----------|
| No matches | 200 | Empty string | No content inserted |
| Pattern error | 400 | `{"error": "Invalid pattern"}` | Return error |
| Circular reference | 500 | `{"error": "Circular dependency"}` | Abort expansion |
| Recursion limit | 500 | `{"error": "Max depth exceeded"}` | Abort expansion |

---

## Claude Code Integration

### Standards Delivery

**Multi-Agent Mode:**
- Standards expanded into agent definition at installation
- Agent receives complete standards as part of its prompt
- No runtime standards lookup needed

**Single-Agent Mode:**
- Standards expanded into command steps at installation
- Each step includes relevant standards inline
- User/agent has standards context for each step

### Dynamic Standards Updates

**Current Limitation:** Standards are static after installation.

**Future Enhancement:**
- API-driven standards retrieval
- Hot-reload standards during agent execution
- Version-controlled standards with updates

---

## Testing Recommendations

### Unit Tests
- [ ] Glob pattern expansion (single category)
- [ ] Glob pattern expansion (all categories)
- [ ] Lexicographical ordering
- [ ] Header injection
- [ ] Profile inheritance (override behavior)
- [ ] Missing file handling
- [ ] Empty category handling

### Integration Tests
- [ ] API `/standards` listing
- [ ] API `/standards/{category}/{name}` retrieval
- [ ] Standards creation via POST
- [ ] Standards deletion via DELETE
- [ ] Template expansion in commands
- [ ] Template expansion in agents
- [ ] Role-based standards assignment

### Edge Case Tests
- [ ] Empty standard file
- [ ] Very large standard file (>1MB)
- [ ] Standard with special characters
- [ ] Standard with code blocks
- [ ] Conflicting standards (detection)
- [ ] Circular template includes

---

## Related Documentation

- [API Specification](../api-spec_Version2.md) - API endpoints for standards
- [Claude Code Integration Policy](../CLAUDE_CODE_INTEGRATION_POLICY_Version2.md) - Integration patterns
- [Profiles Analysis](./profiles-analysis.md) - Profile structure and inheritance
- [Commands Analysis](./commands-analysis.md) - How standards are used in commands
- [Roles Analysis](./roles-analysis.md) - Role-based standards assignment

---

## Recommendations

### Immediate Actions
1. Add validation for standard filenames
2. Create standards authoring guide
3. Implement conflict detection tool
4. Document standard structure template

### Short-Term Improvements
1. Add standards metadata (version, author, tags)
2. Implement standards versioning
3. Create standards testing framework
4. Add standards quality metrics (completeness, clarity)

### Long-Term Vision
1. Standards marketplace/registry
2. Dynamic standards updates via API
3. AI-assisted standards generation
4. Standards composition (combine multiple standards)
5. Standards analytics (usage tracking)
6. Interactive standards builder UI

---

**Last Updated:** 2025-10-14  
**Analysis Version:** 2.0  
**Agent OS Version Analyzed:** 2.0.3  
**Source Repository:** https://github.com/buildermethods/agent-os
