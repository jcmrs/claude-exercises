# Agent OS Analysis Documents

## Overview

This directory contains detailed technical analysis of the Agent OS codebase, focusing on file system conventions, API mappings, and integration patterns. These documents are designed for both human developers and AI agents working with Agent OS.

---

## Document Index

### Core Analysis Documents

| Document | Purpose | Lines | Focus Area |
|----------|---------|-------|------------|
| [api-conventions.md](./api-conventions.md) | **Canonical API Reference** | 674 | Complete API-to-filesystem mapping |
| [profiles-analysis.md](./profiles-analysis.md) | Profile System | 455 | Profile structure, inheritance, conventions |
| [commands-analysis.md](./commands-analysis.md) | Command System | 525 | Command patterns, step discovery, API mappings |
| [standards-analysis.md](./standards-analysis.md) | Standards Library | 497 | Standards organization, glob patterns |
| [workflows-analysis.md](./workflows-analysis.md) | Workflow System | 583 | Workflow patterns, template inclusion |
| [roles-analysis.md](./roles-analysis.md) | Role Definitions | 523 | Role YAML structure, compilation |
| [agents-analysis.md](./agents-analysis.md) | Agent System | 551 | Agent definitions, dynamic generation |
| [scripts-analysis.md](./scripts-analysis.md) | Installation Scripts | 545 | Script inventory, functions, workflows |

**Total:** 4,353 lines of comprehensive technical documentation

---

## Quick Navigation

### By Use Case

**Need to understand API endpoints and file mappings?**  
→ Start with [api-conventions.md](./api-conventions.md)

**Working with profiles?**  
→ See [profiles-analysis.md](./profiles-analysis.md)

**Creating or modifying commands?**  
→ Check [commands-analysis.md](./commands-analysis.md)

**Managing standards?**  
→ Review [standards-analysis.md](./standards-analysis.md)

**Understanding workflows?**  
→ Read [workflows-analysis.md](./workflows-analysis.md)

**Defining roles?**  
→ Consult [roles-analysis.md](./roles-analysis.md)

**Building agents?**  
→ Study [agents-analysis.md](./agents-analysis.md)

**Understanding installation?**  
→ Examine [scripts-analysis.md](./scripts-analysis.md)

---

## Document Structure

Each analysis document follows a consistent structure:

1. **Executive Summary** - Key findings and overview
2. **Inventory** - Complete listing of files/components
3. **Directory Structure** - File trees and organization
4. **Naming Conventions** - Patterns, regex, validation rules
5. **API Endpoint Mappings** - HTTP methods, file system actions, responses
6. **Details by Type** - Specific analysis for each component
7. **Template Compilation** - Expansion rules and examples
8. **Profile Inheritance** - Override behavior and examples
9. **Validation & Quality** - Checks and enforcement rules
10. **Error Handling** - Common scenarios and responses
11. **Performance** - Caching, optimization, targets
12. **Testing** - Recommended tests and edge cases
13. **Related Documentation** - Cross-references
14. **Recommendations** - Immediate, short-term, and long-term actions

---

## Key Conventions Reference

### File Naming Patterns

| Component | Pattern | Example |
|-----------|---------|---------|
| Profile | `{name}` | `default`, `custom` |
| Command | `{name}` | `create-spec`, `implement-spec` |
| Single-Agent Step | `{number}-{name}.md` | `1-create-spec.md` |
| Multi-Agent Command | `{command-name}.md` | `create-spec.md` |
| Role File | `{category}.yml` | `implementers.yml` |
| Standard | `{name}.md` | `api.md`, `coding-style.md` |
| Workflow | `{name}.md` | `implement-task.md` |
| Agent | `{name}.md` | `spec-writer.md` |

### Critical Regex Patterns

```regex
# Single-agent step files
^\d+-.*\.md$

# General markdown files (standards, workflows, agents)
^[a-z0-9-]+\.md$

# Role files
^[a-z0-9-]+\.ya?ml$

# IDs (profiles, roles, agents)
^[a-z0-9-]+$
```

### Template Inclusion Syntax

```markdown
# Standards inclusion
{{standards/global/*}}
{{standards/backend/*}}
{{standards/*}}

# Workflow inclusion
{{workflows/specification/write-spec}}
{{workflows/implementation/implement-task}}

# Variables (in agent compilation)
{{id}}
{{description}}
{{your_role}}
{{areas_of_responsibility}}
```

---

## API Endpoint Summary

### Quick Reference

| Resource | List | Get | Create | Update | Delete |
|----------|------|-----|--------|--------|--------|
| Profiles | `GET /profiles` | `GET /profiles/{name}` | `POST /profiles` | - | `DELETE /profiles/{name}` |
| Roles | `GET /profiles/{p}/roles` | `GET /profiles/{p}/roles/{id}` | `POST /profiles/{p}/roles` | - | `DELETE /profiles/{p}/roles/{id}` |
| Standards | `GET /standards` | `GET /standards/{c}/{n}` | `POST /standards` | - | `DELETE /standards/{c}/{n}` |
| Commands | `GET /commands` | `GET /commands/{n}` | `POST /commands` | - | `DELETE /commands/{n}` |
| Workflows | `GET /workflows` | `GET /workflows/{n}` | `POST /workflows` | - | `DELETE /workflows/{n}` |
| Agents | `GET /agents` | `GET /agents/{n}` | `POST /agents/compile` | - | - |

**Note:** All endpoints require `?profile={profile}` query parameter (defaults to "default").

---

## Convention Highlights

### Single-Agent Step Discovery

**Pattern:** `^\d+-.*\.md$`

**Rules:**
- Must start with digits
- Must have hyphen separator
- Files sorted numerically (not alphabetically)
- Gaps in numbering allowed
- Non-matching files ignored

**✅ Valid:** `1-create.md`, `2-verify.md`, `10-finish.md`  
**❌ Invalid:** `create.md`, `step-1.md`, `1_create.md`

### Profile Inheritance

**Command Override:** Complete replacement (no per-step fallback)  
**Standards Override:** File-level (parent files used if not overridden)  
**Rationale:** Prevents mixed/inconsistent workflows

### Glob Expansion

**Order:** Lexicographical (alphabetical)  
**Header Injection:** `## [Standard: {filename}]`  
**Recursion Limit:** 10 levels (recommended)  
**Cycle Detection:** Required

---

## Integration Guidelines

### For AI Agents (Claude Code)

1. **File Discovery:** Always use API endpoints, never parse file system directly
2. **Step Execution:** Sequential only (no batch execution for single-agent)
3. **Error Handling:** Gracefully handle missing files, invalid patterns
4. **Caching:** Cache API responses when appropriate
5. **Validation:** Validate file names before creation

### For Human Developers

1. **Start with api-conventions.md:** Canonical reference for all mappings
2. **Follow Naming Patterns:** Use established conventions for consistency
3. **Test Glob Patterns:** Verify expansion behavior before deployment
4. **Document Changes:** Update analysis docs when modifying conventions
5. **Validate YAML:** Ensure role files are valid YAML

---

## Testing Coverage

### Unit Test Areas

- [ ] File name validation (all patterns)
- [ ] Step discovery and sorting
- [ ] Glob pattern expansion
- [ ] Template variable substitution
- [ ] Circular dependency detection
- [ ] Profile inheritance resolution
- [ ] YAML parsing
- [ ] API endpoint mappings

### Integration Test Areas

- [ ] Complete command workflows
- [ ] Agent compilation from roles
- [ ] Standards expansion in templates
- [ ] Multi-agent orchestration
- [ ] Profile switching
- [ ] Error scenarios

---

## Maintenance Notes

### Updating These Documents

When Agent OS changes:

1. **Update Analysis Docs:** Modify relevant analysis documents
2. **Update api-conventions.md:** Ensure canonical reference is current
3. **Update Cross-References:** Maintain links between documents
4. **Update Examples:** Keep code examples accurate
5. **Version History:** Note changes in each document

### Review Schedule

- **Quarterly:** Review for accuracy
- **After Major Releases:** Full update pass
- **After Convention Changes:** Immediate update

---

## Related Documentation

### Main Documentation

- [../README.md](../README.md) - Main analysis overview
- [../roadmap.md](../roadmap.md) - Project roadmap
- [../codemap.md](../codemap.md) - Component map
- [../workflows.md](../workflows.md) - Workflow analysis
- [../architecture.md](../architecture.md) - Architecture diagrams
- [../config.md](../config.md) - Configuration mapping
- [../commands.md](../commands.md) - Command reference
- [../refactoring-notes.md](../refactoring-notes.md) - Refactoring notes
- [../test-coverage.md](../test-coverage.md) - Test coverage

### Reference Documents

- [../docs/api-spec_Version2.md](../docs/api-spec_Version2.md) - Full API specification
- [../docs/CLAUDE_CODE_INTEGRATION_POLICY_Version2.md](../docs/CLAUDE_CODE_INTEGRATION_POLICY_Version2.md) - Integration policy

---

## Contribution Guidelines

### Adding New Analysis

1. Follow established document structure
2. Include all required sections
3. Add comprehensive examples
4. Cross-reference related documents
5. Update this README index

### Improving Existing Analysis

1. Maintain document structure
2. Preserve cross-references
3. Update version history
4. Test all code examples
5. Validate all links

---

## Statistics

### Documentation Metrics

- **Total Documents:** 8
- **Total Lines:** 4,353
- **Total Characters:** ~191,000
- **Code Examples:** 100+
- **Tables:** 80+
- **Cross-References:** 50+

### Coverage Metrics

- **Profiles:** 100%
- **Commands:** 100%
- **Standards:** 100%
- **Workflows:** 100%
- **Roles:** 100%
- **Agents:** 100%
- **Scripts:** 100%
- **API Mappings:** 100%

---

## Contact and Support

For questions about these analysis documents:

1. **Check api-conventions.md first** - Canonical reference
2. **Search specific analysis docs** - Detailed component info
3. **Review main documentation** - High-level overviews
4. **Consult source repository** - Original implementation

---

**Last Updated:** 2025-10-14  
**Analysis Version:** 2.0  
**Agent OS Version Analyzed:** 2.0.3  
**Source Repository:** https://github.com/buildermethods/agent-os

**These documents represent a complete technical analysis of Agent OS file system conventions and API mappings.**
