# Agent OS Complete Methodology & File Workflow Mapping: Master Prompt Blueprint

## 1. Introduction

### Goal

The goal of this project is to generate a comprehensive, granular, and fully up-to-date mapping of the Agent OS methodology, process workflow, agent/subagent roles, file-level artifacts, standards, and context dependencies.  
**In addition, the project must result in the design and (if feasible) generation of a “meta-agent”: an agent that can orchestrate, validate, and augment subagents or user workflows for Agent OS, ensuring all interactions, handoffs, and outputs are fully compatible with Agent OS’s requirements and best practices.**

### Scope

- Analyze and document every phase of the Agent OS process, from product context planning to feature spec lifecycle, through task assignment, implementation, verification, and product-level aggregation.
- Map all standards, profiles, agent roles, file formats, and context flows across all layers (global/product, spec/feature, implementation/task).
- Produce canonical tables, flowcharts/diagrams, phase-by-phase checklists, and example artifacts for each output file and handoff.
- Identify and document error cases, ambiguities, and required validation steps.
- **Design and specify the meta-agent:** a supervisory or orchestration agent that can guide, validate, and/or automate the correct preparation and handoff of all necessary Agent OS artifacts, using the mapping and standards produced in this project.
- Reference and use all current, authoritative documentation and source files—no reliance on LLM prior knowledge or summaries.

### What is a “meta-agent”?

In this context, a “meta-agent” is:
- An agent (AI or automation) whose role is to supervise, orchestrate, and/or validate all Agent OS-related workflows.
- It is responsible for ensuring that subagents, users, or other AIs consistently prepare and deliver inputs, outputs, and handoffs in the precise formats, structures, and standards required by Agent OS.
- The meta-agent may prompt for missing information, correct errors, enforce standards, and automate or coordinate multi-phase or multi-feature workflows.
- The meta-agent may also serve as a “bridge” between less-technical users and Agent OS, abstracting away complexity and ensuring smooth, standards-compliant operation.

### Reference to Existing Subagent

- You will be provided with an existing subagent implementation, which serves as a partial foundation or inspiration for the meta-agent.
- Study the subagent’s approach, strengths, and limitations.
- Use it as a reference point, but extend, generalize, and improve upon it as required to fulfill the goals and requirements of this project.

### AI Requirements

- Must be an AI model or agent with a context window large enough to process all referenced documentation, source files, standards, and example artifacts simultaneously (e.g., 1M+ tokens).
- Must be capable of:
  - Parsing and analyzing Markdown, YAML, code, and structured documentation.
  - Synthesizing cross-referenced tables, diagrams, and examples as output.
  - **Designing, specifying, and (if feasible) generating the meta-agent described above, using all mapped requirements, standards, and examples.**
  - Following explicit, multi-step instructions for data gathering, analysis, validation, and synthesis.

### Constraints

- **Freshness:** All analysis, mapping, and example generation must be performed from scratch, using the latest available documentation and source files. The AI must not rely on its internal LLM knowledge or outdated summaries.
- **Completeness:** The entire project must be executed in a single, uninterrupted processing run—no incremental, piecemeal, or conversational approaches. The response must be fully comprehensive and self-contained.
- **Exactness:** All outputs must strictly follow the structures, formats, and standards referenced in source documentation—no omissions, paraphrasing, or unverified assumptions.
- **Validation:** The output must include explicit validation steps and checks for each phase, file, and handoff to ensure agent-os compatibility.
- **Reference Integrity:** Every claim, rule, or format in the output must be traceable to a specific document, file, or authoritative instruction, with references or links where possible.

---

## 2. Source Material

This section specifies all source materials and references the AI must use to complete the project. The instructions below enforce total completeness and freshness—**no reliance on prior knowledge or summaries is permitted**.

### 2.1. Documentation and Knowledge Sources

The AI must use, in its entirety, ALL of the following (and may not skip or partially process any):

1. **Official Anthropic/Claude Documentation**  
   - https://docs.claude.com/en/home  
   - All subpages and linked documentation relevant to agent, prompt, and context window capabilities.

2. **GitHub Project Repository: Agent OS**  
   - https://github.com/buildermethods/agent-os  
   - *Every* file, directory, and subdirectory in this repository must be inspected and analyzed.  
   - *No file, script, workflow, standard, or template may be omitted, regardless of perceived importance or naming.*  
   - Includes, but is not limited to:  
     - `/profiles`
     - `/standards`
     - `/product`
     - `/specs`
     - `/commands`
     - `/workflows`
     - `/agents`
     - `/scripts`
     - All Markdown, YAML, shell, and code files

3. **Agent OS Official Website and Documentation**  
   - https://buildermethods.com/agent-os  
   - *Every* page, subpage, tutorial, example, and linked document.
   - *No omission permitted due to perceived redundancy with repo files.*

4. **Example Artifacts**  
   - The AI must gather and review canonical or example artifacts for every output file and handoff, including (but not limited to):  
     - `requirements.md`
     - `spec.md`
     - `tasks.md`
     - `task-assignments.yml`
     - `implementation/*.md`
     - `verification/*.md`
   - If such artifacts exist in the repo, the website, or documentation, the AI must extract and use them.
   - If they do not exist, the AI must synthesize them in perfect accordance with referenced standards.

5. **Potentially Applicable Research Papers**  
   - (To be supplied by the user as needed.)

### 2.2. Instructions for Source Handling

- The AI must not rely on, reference, or summarize any prior training, internal LLM knowledge, or cached content.  
- Each and every file, document, and web page referenced above must be **freshly accessed, fetched, and inspected in full** as part of this project.  
- *No* directory, file, standard, template, or artifact may be omitted, skipped, or summarized based on its name, past experience, or any other heuristic.
- If new files, pages, or artifacts are discovered during processing (e.g., via links or cross-references), the AI must recursively fetch and analyze them as well.
- If the user supplies additional links or research papers, the AI must integrate them into its process just as rigorously as the above.

### 2.3. Order of Processing

- The AI may process these sources in any order that enables maximum completeness, cross-linking, and validation.
- However, if dependencies or cross-references exist (e.g., standards referenced in templates), the AI must ensure that dependencies are resolved and all references are validated.
- If any ambiguity in source coverage arises, the AI must default to **full inclusion**.

### 2.4. Source Manifest Requirement

- **As it processes each source, the AI must maintain a complete and up-to-date “source manifest.”**
  - The manifest must include:
    - The full canonical path or URL of every file, web page, or document processed.
    - The version, hash, or retrieval timestamp (where possible/applicable).
    - The context in which the source was used (e.g., for table mapping, example artifact, standards reference).
  - The manifest must be included as a dedicated section in the final output.
  - If any source could not be processed (e.g., binary, non-English, error), the manifest must log the reason and context.
  - This manifest serves as the authoritative record for what was analyzed and mapped.

---

**Do not proceed to analysis, mapping, or meta-agent generation until every required source has been fully loaded, parsed, and logged in the manifest.**

---

## 3. Methodology Analysis
- 3-layer context model (global, spec, implementation)
- Workflow phases (plan-product, new-spec, create-spec, implement, verify, update-roadmap)
- Profile selection/inheritance

## 4. Standards and Profiles
- Standards: definitions, structure, enforcement
- Profiles: configuration, inheritance, overrides

## 5. Agent/Role Mapping
- List of all agent and subagent roles
- Role responsibilities, handoff points, required outputs

## 6. File and Artifact Structure
- Directory tree
- File naming conventions
- Format and content requirements (Markdown, YAML, etc.)

## 7. Process Mapping
- Granular table: phase, input, output, agent, file, standards, checks
- Flowcharts and diagrams (Mermaid or other)

## 8. Checklists
- Phase-by-phase checklists for subagents and orchestrators

## 9. Canonical Examples
- requirements.md (interactive Q&A)
- spec.md (formal sections)
- tasks.md (grouped, assigned, checklists)
- task-assignments.yml, implementation report, verification report

## 10. Error Handling & Edge Cases
- Common mistakes and validation steps

## 11. Output Requirements
- Table(s), diagrams, examples, error cases

## 12. Instructions for the AI
- How to process and synthesize all the above
- How to format outputs
- How to handle ambiguity or missing data

## Appendix
- Full references to all URLs and files
