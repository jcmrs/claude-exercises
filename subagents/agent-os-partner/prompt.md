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

This section instructs the AI to deeply analyze, internalize, and simulate the Agent OS methodology. The AI must go beyond surface-level summary and demonstrate true comprehension and reasoning. All findings must be evidenced by scenario-based examples, counter-examples, and self-tests.

### 3.1. 3-Layer Context Model

- **Task:** Analyze, map, and demonstrate the Agent OS three-layer context model:
  - **Global context:** Product standards, mission, roadmap, tech stack, persistent rules.
  - **Spec/feature context:** Current feature’s requirements, visual assets, Q&A, scope, constraints.
  - **Implementation/task context:** Current task or subtask, assigned agent, immediate standards, and scope.
- **Requirements:**
  - For each layer, clearly define its contents, scope, and how it is accessed and used by agents/subagents.
  - Diagram the flow of context from one layer to another and how agents must reference each layer for every major workflow phase.
  - **Demonstrate understanding by producing scenario-based examples:** For at least three different agent roles (e.g., implementer, verifier, researcher), simulate how they would use all three context layers to process a specific task.
  - Identify and explain any potential ambiguities, overlaps, or risks in context handoff or inheritance.

### 3.2. Workflow Phases

- **Task:** Map the complete lifecycle of a feature in Agent OS, including all workflow phases:
  1. **plan-product**
  2. **new-spec** (research and requirements gathering)
  3. **create-spec** (formal spec creation)
  4. **task breakdown** (task list, assignments)
  5. **implement** (implementation, documentation)
  6. **verify** (feature verification and compliance)
  7. **update-roadmap** (aggregate and update product context)
- **Requirements:**
  - For each phase, detail:
    - Inputs and outputs (including files, context, and standards)
    - Agent/subagent roles involved
    - Required standards and validation steps
    - How context flows into and out of the phase
  - **Simulation:** For each phase, produce a step-by-step simulation for a representative feature (e.g., “User Login Feature”), showing context handoff, agent actions, and file outputs at each step.
  - Identify phase transitions, decision points, and feedback/iteration loops.
  - Explicitly map dependencies between phases and possible failure/recovery scenarios.

### 3.3. Profile Selection and Inheritance

- **Task:** Analyze the concept of “profiles” in Agent OS and their impact on standards, agent templates, and workflows.
- **Requirements:**
  - Define what a profile is, how it is created, selected, and inherited.
  - Detail which elements (standards, agent templates, workflows) are profile-dependent, and how overrides/inheritance works.
  - **Scenario analysis:** Demonstrate how two different profiles (e.g., “default” vs. “custom-enterprise”) would affect the standards, available agents, or file outputs for a given feature spec.
  - Map and explain how profile selection is validated and enforced throughout the workflow.
  - Identify any ambiguity or risk in profile handling and propose validation steps to ensure correctness.

### 3.4. Methodology Verification and Self-Test

- **Task:** After mapping and simulating the above, the AI must **verify its own understanding** of the methodology.
- **Requirements:**
  - For each major process and context flow, pose and answer “What if?” scenarios (e.g., “What if the wrong profile is selected?” “What if a standards file is missing?” “What if a task is implemented with outdated product context?”).
  - Identify ambiguity, edge cases, or conflicting rules, and suggest how the meta-agent should handle or flag them.
  - **Deliver a set of self-test cases** (with expected outcomes) that can be used to validate that future agents or users are properly following the methodology.

---

**Do not proceed to standards mapping or agent role mapping until the methodology has been fully mapped, simulated, and verified as above.**

## 4. Standards and Profiles

This section instructs the AI to rigorously analyze, map, simulate, and validate the standards and profiles system in Agent OS.  
The resulting outputs must enable the meta-agent to enforce, validate, and, where allowed, adapt standards and profiles in any context or workflow.

---

### 4.1. Standards

#### 4.1.1. Definition and Structure
- Fully enumerate and define every standards file, rule, and best practice referenced or enforced by Agent OS.
  - Include, but do not limit to: coding standards, naming conventions, file structure, test-writing rules, API/architecture guidelines, accessibility, security, and technological stack requirements.
  - For each standard, specify:
    - File path and canonical location (e.g., `standards/global/coding-style.md`)
    - Scope of enforcement (global, feature, implementation, agent-specific)
    - Format (Markdown, YAML, other)
    - Inheritance/overrides (can this standard be superseded by profile/project standards?)

#### 4.1.2. Enforcement and Validation
- **Map**: For every workflow phase and file/artifact, specify which standards are required, how compliance is checked, and what happens when a violation is detected.
- **Simulate**: For at least three scenarios (e.g., code style violation, missing security standard, conflicting standards across profiles), walk through how detection, error reporting, and remediation would occur.
- **Best Practices**: Synthesize not only formal rules but the meta-principles (e.g., "do not paraphrase user requirements," "must document standards compliance in every implementation/verification report").
- **External Standards**: If Agent OS references external standards (e.g., language/framework best practices), include these and specify how the meta-agent must fetch, validate, or update them.

#### 4.1.3. Gaps and Ambiguity
- **Analyze**: Identify any areas where standards are ambiguous, missing, or potentially conflicting.
  - For each, propose a validation or conflict-resolution approach for the meta-agent.

---

### 4.2. Profiles

#### 4.2.1. Configuration and Structure
- **Fully define**: What is a profile in Agent OS? How is it created, structured, and selected?
  - List and describe all configurable elements (standards, agent templates, workflows, roles, etc.).
  - Specify canonical file formats/locations (e.g., `profile-config.yml`).

#### 4.2.2. Inheritance and Overrides
- **Map**: How does profile inheritance work?
  - Describe the rules for inheriting standards and behaviors from parent/default profiles.
  - For each element, specify how and where it can be overridden (file-level, directory-level, agent-level, project-level).
  - Simulate: Provide at least two scenarios (e.g., a “default” profile inherited by a “custom-enterprise” profile with specific overrides).
  - Include edge cases (e.g., missing, malformed, or conflicting overrides).

#### 4.2.3. Enforcement and Validation
- **Describe**: How is the active profile determined and enforced at runtime (both by humans and by agents)?
- **Validation**: What checks must the meta-agent perform to ensure standards and configuration are consistent, complete, and non-conflicting for a given profile?
- **Simulation**: For at least one scenario, walk through profile selection, inheritance, override, and enforcement for a new feature spec.

#### 4.2.4. Profile-Driven Agent Behavior
- **Map**: For every major agent/subagent, specify how profiles and standards affect:
  - Prompting
  - Validation
  - Output formatting
  - Error handling
- **Best Practices**: Summarize how meta-agents should reason about, enforce, or surface profile-driven differences to users and subagents.

---

**Do not proceed to agent/role mapping until all standards, profiles, inheritance, and enforcement logic have been fully mapped, simulated, and validated.**

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
